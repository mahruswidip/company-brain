# Rosana Collection — RULES

> Aturan yang TIDAK BOLEH dilanggar agent. Kalau tugas bertentangan dengan aturan ini, berhenti dan tanya Kepala Kantor.
> Aturan ini hanya untuk Rosana Collection — aturan Rosana Travel (DP 30%, keberangkatan, dll) TIDAK berlaku di sini.

## A. Ongkir (paling sering salah — baca teliti)
1. Draft **boleh** disimpan tanpa kode pos.
2. Quote ongkir otomatis **wajib** kode pos.
3. Ongkir belum dipilih (`null`) **≠** gratis ongkir (`0`).
4. Ongkir Rp0 hanya sah jika **dipilih secara eksplisit**.
5. **Tidak boleh** fallback otomatis ke ongkir manual saat quote otomatis gagal.
6. Konfirmasi order & invoice **wajib** punya keputusan ongkir eksplisit.
7. Nilai Rp20.000 dan Rp0 yang muncul di test adalah skenario uji, **bukan tarif baku**.

## B. Uang & invoice
1. **Total invoice Jastip = Σ subtotal item + `shipping_cost_charged`** (ongkir yang ditagih ke customer).
2. Invoice dibuat lewat service yang ada. **Bot/agent tidak boleh menulis model invoice langsung.**
3. **Harga beli Jastip yang belum diketahui = `0`** (FINAL, owner 18 Nov 2026). Produk Jastip tetap bisa dibuat & diproses tanpa harga beli.
   - Harga beli `0` karena belum diketahui **tidak boleh** dianggap biaya pembelian aktual di laporan laba final.
   - **Penanda "harga beli belum lengkap" DISETUJUI owner (18 Nov 2026):** produk/order yang harga belinya belum diketahui diberi flag; laporan laba memisahkan/menandai order dengan flag aktif. Implementasi menyentuh laporan keuangan → wajib QA + approval owner.
   - `normalizeCostPrice()` pernah dilaporkan mengubah `0` → `null`. **Cek & samakan dengan keputusan ini** (lewat PR + approval).
4. **Rekonsiliasi ongkir** (FINAL, owner 18 Nov 2026):
   ```
   selisih_ongkir = ongkir_customer - ongkir_jne
   > 0 → LABA Rosana Collection
   < 0 → BEBAN Rosana Collection
   = 0 → IMPAS
   ```
   - Penanggung internal (departemen/PIC) & jurnal akuntansi: ⚠️ PERLU DIKONFIRMASI.
   - Data yang tidak cocok jangan dipaksa digabung.
5. **Fee, komisi, margin, refund** (FINAL, owner 18 Nov 2026):
   - **Fee jastip: tidak ada rumus.** Nilainya ditentukan owner Collection di halaman admin.
   - **Persentase & pembagian komisi sales:** ditentukan owner Collection di halaman admin.
   - **Margin = harga jual − harga beli/kulakan.**
   - **Tidak ada pembulatan.**
   - **Refund diinput di admin** oleh admin (karyawan Bu Ika). **Tidak ada potongan pembatalan.** Refund **disetujui owner Collection (Bu Ika)**.
   - Jangan meng-hardcode nilai fee/komisi; selalu baca dari pengaturan admin.
6. **Pembayaran: transfer manual + bukti bayar saja. Collection TIDAK memakai Xendit / payment gateway.** Jangan menambahkan payment gateway.
7. Semua perubahan yang menyentuh uang, stok, atau laporan → wajib lolos QA Reviewer → wajib approval owner sebelum produksi.

## C. Order & data customer
1. Data penerima & ongkir disimpan sebagai **snapshot** di order. Perubahan profil customer tidak boleh mengubah order lama.
2. Draft belum dianggap pesanan aktif sampai dikonfirmasi.
3. Alur status yang diketahui: draft → konfirmasi → invoice. Untuk enum & transisi: **baca enum, model, migration, service, policy, feature test** — pakai status aktual di repo. Jangan buat enum/transisi baru berdasarkan asumsi.
4. Satu order boleh berisi banyak item.

## D. Produk & stok
1. Pembuatan produk lewat `ProductCreateService`. Perubahan stok lewat `StockMovementService`. Jastip lewat `JastipOrderService`.
2. Produk tanpa variasi → satu varian default.
3. Cegah duplikasi produk & transaksi (idempotency wajib untuk webhook/callback).
4. **Supplier opsional (`nullable`)** (FINAL, owner 18 Nov 2026) — berlaku untuk produk Jastip katalog maupun nonkatalog. **Tidak boleh** ada validasi universal yang mewajibkan supplier di semua order Jastip. Cek apakah validasi form, service, dan flow bot sudah konsisten (`createJastipProduct()` pernah dilaporkan sudah mengizinkan `null`).
5. Harga beli belum diketahui = `0` (lihat B.3).

## D2. Status label untuk agent
- `CONFIRMED` — keputusan bisnis yang sudah disetujui, wajib diikuti.
- `NEEDS_CODE_VERIFICATION` — cek implementasi langsung di repo.
- `NEEDS_BUSINESS_DECISION` — tidak boleh ditentukan sepihak oleh agent.

## E. AI / bot (CollectionClaw)
1. Bot/AI **tidak boleh** akses langsung ke database atau shell. AI hanya parsing/usulan; Laravel yang memvalidasi & mengubah data.
2. Tindakan final butuh konfirmasi pengguna.
3. Satu akun internal = satu akun Telegram.
4. Validasi input & idempotency wajib sebelum agent diizinkan membuat transaksi.

## F. Database, deploy, testing
1. Database produksi = SQLite di `database/database.sqlite`. Migrasi produksi → wajib approval owner (dan backup dulu).
2. Jangan ubah `.env` atau migrasi lama.
3. Jangan tambah package / ganti stack tanpa kebutuhan jelas.
4. Aturan QA:
   - Data uji pakai prefix `QA`.
   - Jangan ubah data produksi.
   - Jangan kirim pesan/integrasi eksternal saat QA.
   - Jangan lakukan pembayaran sungguhan.
   - Jangan hapus order/invoice lama.
   - Jangan ubah konfigurasi.
   - Buka ulang data setelah disimpan untuk cek.
   - Catat ID order & invoice hasil uji.

## G. Isolasi
Rosana Collection adalah workspace/brand terpisah. Data, akses, knowledge, dan integrasi tidak boleh tercampur dengan Rosana Travel atau brand lain.
