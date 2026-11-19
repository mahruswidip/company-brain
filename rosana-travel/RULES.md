# Rosana Travel — RULES

> Aturan yang TIDAK BOLEH dilanggar agent. Kalau tugas bertentangan dengan aturan ini, berhenti dan tanya Kepala Kantor.
> Aturan ini hanya untuk Rosana Travel — tidak berlaku untuk Rosana Collection.

## A. Uang & pembayaran
1. DP standar = **30% × total harga**. Pelunasan = **70% × total harga**. Pembayaran boleh bertahap.
2. Harga transaksi bisa berbeda dari harga master paket (penyesuaian harga khusus per parent/booking).
3. Harga transaksi wajib disimpan sebagai **snapshot**. Perubahan master paket tidak boleh mengubah histori keuangan.
4. Snapshot harga dan invoice lama **tidak boleh diubah**.
5. Histori booking/pembayaran **tidak boleh diubah secara destruktif** (tidak hapus, tidak overwrite).
6. Semua perubahan yang menyentuh pembayaran → wajib lolos QA Reviewer → wajib approval owner sebelum produksi.

7. Rumus yang ada di sistem: `settlement_paid = total_cash_paid - total_refunded`. Ada konsep invoice **overdue**.
8. Invoice yang **belum dibayar** boleh di-void lewat mekanisme yang tersedia. Invoice yang **sudah dibayar tidak boleh dibatalkan**.
9. Reschedule wajib mempertahankan histori pembayaran yang berhasil.

### Refund, pembatalan, pelunasan (FINAL, owner 18 Nov 2026)
10. **Refund dikembalikan manual oleh tim keuangan** ke rekening yang dulu dipakai transfer. Sistem **tidak** memproses refund otomatis, tapi refund **wajib terdata dan diinput di admin**.
11. **Tidak ada pembulatan** nominal.
12. **Tidak ada denda** keterlambatan pelunasan (untuk saat ini).
13. **Tidak ada** ketentuan pembatalan yang mengikat dan **tidak ada persentase potongan** pembatalan.
14. **Batas waktu pelunasan = satu minggu sebelum manasik umroh** paket/keberangkatan tersebut.
15. Jangan menambahkan rumus refund, potongan, atau denda tanpa keputusan owner.

## A2. Xendit — LIVE PRODUCTION (dikonfirmasi owner 18 Nov 2026)
1. Xendit adalah integrasi **production**, bukan sandbox.
2. **Jangan** melakukan transaksi uji dengan pembayaran sungguhan.
3. **Jangan** mengubah histori pembayaran berhasil.
4. Perubahan webhook **tidak boleh** menyebabkan pencatatan pembayaran ganda (cek idempotensi).
5. **Jangan** mengubah integrasi / konfigurasi Xendit production tanpa approval owner.
6. Sinkronisasi status pembayaran pernah memakai command `payment:sync-pending` — cek sebelum membuat mekanisme baru.

## B. Paket & keberangkatan
1. Relasi: **Paket 1:N Keberangkatan**. `keberangkatans.paket_id` adalah relasi utama.
2. `booking_groups.keberangkatan_id` adalah referensi utama booking.
3. Data operasional (jamaah, room assignment, reschedule, kapasitas) mengacu ke **Keberangkatan**, bukan hanya Paket.
4. Relasi jamaah–booking–keberangkatan **tidak boleh hilang**.

### Status keberangkatan
```
DRAFT → OPEN ↔ CLOSED → DEPARTED → COMPLETED
CANCELLED (terminal)
```
- Hanya `OPEN` yang menerima booking atau reschedule.

### Kapasitas (keputusan 1 Okt 2026, dikonfirmasi ulang 18 Nov 2026)
1. Kursi dihitung **terpakai** saat status booking `dp_paid` **atau** `paid`.
2. Status `draft`, `invoiced`, `cancelled` **tidak** mengonsumsi kapasitas.
3. Kolom kuota menyimpan **kapasitas total**, bukan sisa kursi. Jangan mengurangi kolom kuota secara langsung.
4. Sisa kursi = kuota − jumlah booking berstatus `dp_paid`/`paid` (dihitung, tidak disimpan).
5. **Tidak ada** temporary hold. Usulan hold 15 menit sudah **ditolak** — jangan dibuat.
6. Jangan ubah mekanisme penghitungan kuota tanpa approval owner.

## C. API booking (`/api/book`)
1. Utamakan `keberangkatan_id`.
2. `paket_id` lama tetap diterima **hanya jika** paket itu punya tepat satu Keberangkatan `OPEN`.
3. Jika ada beberapa keberangkatan aktif → pengguna wajib memilih keberangkatan.
4. Booking dengan paket & keberangkatan yang tidak cocok **harus ditolak**.
5. Kompatibilitas klien lama wajib dipertahankan.
6. Kode error yang sudah ditetapkan (jangan diganti nama):
   - `DEPARTURE_REQUIRED`
   - `NO_OPEN_DEPARTURE`
   - `PAKET_DEPARTURE_MISMATCH`

## D. Database, migrasi, backup
1. Migrasi bersifat **additive**. Tidak ada backfill/relink destruktif yang mengubah histori lama.
2. Jangan mengubah migration lama tanpa alasan & approval.
3. Migrasi ke database produksi → wajib approval owner.
4. **Backup lama bukan jaminan rollback.** Snapshot `rosana-group-pre-php2shell-patch-20261023` dibuat **sebelum** patch keamanan (bisa berisi server yang masih rentan). Restore end-to-end belum pernah diuji.
5. Sebelum perubahan berisiko di produksi: buat & verifikasi backup terbaru + pastikan bisa di-restore.
6. Jangan mengubah konfigurasi produksi secara sepihak.

## D2. Git (disetujui owner 18 Nov 2026 — berlaku untuk semua project)
1. Satu branch per tugas → lint/typecheck/tests/build → PR → review QA → merge setelah approval.
2. Jangan push langsung ke branch production.
3. Deploy produksi hanya dengan otorisasi terpisah dari owner.
4. Hindari perubahan di luar scope tugas.

## E. Data & privasi
1. Data jamaah (identitas, paspor) adalah data pribadi. Jangan ditaruh di log, commit, atau output agent.
2. Data **Rosana Travel dan Nipindo Travel wajib terisolasi** (identitas, knowledge, WhatsApp, konteks pelanggan). Infrastruktur boleh dipakai bersama, data tidak.
3. Data antar-workspace AI Control Tower tidak boleh bocor.

## E2. Audit dulu, jangan berasumsi
1. Role agen/reseller: audit role, permission, policy yang ada **sebelum** membuat role baru.
2. Modul paspor, visa, manifest: jangan dianggap sudah ada tanpa memeriksa kode & database.
3. Proyek POS SaaS (Flutter/Riverpod/Drift) adalah proyek **lain** — bukan mobile app Rosana Travel.

## F. Testing
1. Uji fitur keuangan hanya dengan data sintetis.
2. Jangan mengubah transaksi pelanggan asli.
3. Jangan melakukan pembayaran sungguhan.
4. Sebelum PR: lint, typecheck, tests, build harus lolos.
