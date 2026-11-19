# Rosana Travel — BRIEF

> Sumber: rangkuman GPT owner, 18 Nov 2026. Belum diverifikasi ke repository.
> Lihat aturan membaca di `../README.md`.

## 1. Bisnis
- Biro perjalanan **Umrah dan Haji**, bagian dari **Rosana Group**.
- Produk: paket Umrah, Haji, Umrah VIP/Private, program perjalanan khusus.
- Target pelanggan: calon jamaah Umrah/Haji, keluarga, corporate, alumni jamaah.
- Kantor pusat: **Pasuruan, Jawa Timur**. Cabang: Malang, Probolinggo, Jember, Situbondo, Surabaya.
- Website publik: `rosanatravel.com`.
- Brand terkait: **Nipindo Travel** — sistem wajib membedakan identitas & data kedua brand.

## 2. Orang
| Peran | Nama |
|---|---|
| Owner | Ibu Hj. Rosana Hayati |
| IT / developer | Mahrus Widi Pratama |
| Pembayaran | Mbak Ica |
| Legal | Ibu Evi |
| Tim lapangan | Pak Faiz |

## 3. Pengguna sistem
- **Super Admin / Admin** — operasional.
- **Staf** — jamaah, paket, keberangkatan, transaksi.
- **Owner** — kewenangan tertentu terkait pembayaran.
- **Jamaah** — portal, paket, booking, pembayaran.
- **Marketing** — prospek, termasuk lead dari expo.
- Agen/reseller: ⚠️ PERLU DIKONFIRMASI apakah punya role sendiri.

## 4. Tech stack (audit teknis Oktober 2026)
| Komponen | Info |
|---|---|
| Backend | Laravel 12.52.0 |
| Admin panel | Filament 5.2.1 |
| PHP | requirement `^8.2`; server 8.3.31 saat audit (rentan PHP2Shell, patch 8.3.36 tersedia) |
| Auth API | Laravel Sanctum 4.3.1 |
| Database produksi | **SQLite** |
| Portal jamaah | React + TypeScript + Vite + React Router + Tailwind + Axios (`frontend_rosanagroup`) |
| Website publik baru | Next.js 16.3.8 — masih pengembangan (`frontend_public`) |
| Website publik lama (live) | CodeIgniter 3 di Rumahweb |
| PDF | DOMPDF 3.1.2 |
| Server | VPS Vultr, Ubuntu 24.04, Nginx, PHP-FPM |
| DNS | Cloudflare |
| Storage | Laravel local/private storage |

- Laravel 10 + Filament v4 + Next.js 15 (`front_jamaah`) = info fase lama, **tidak berlaku**.
- Versi tepat per 18 Nov 2026: ⚠️ cek repo.

### Repository
**Terkonfirmasi 19 Nov 2026 (cek git di VPS oleh owner):** repo web = **`mahruswidip/WEBSITE-ROSANA-GROUP-2026`** (monorepo backend + frontend), di VPS `/var/www/rosana`, branch utama **`main`**, sinkron dengan GitHub. Di VPS ada folder `kbihuinvitation/` yang belum di git (lihat OPEN_QUESTIONS.md). Tabel di bawah adalah nama folder/modul dari rangkuman lama, bukan repo GitHub terpisah.

| Repo | Isi | Status |
|---|---|---|
| `backend_rosanagroup` | Laravel API, admin Filament, booking, jamaah, pembayaran, Expo | Aktif |
| `frontend_rosanagroup` | Portal React/Vite + Expo | Aktif |
| `frontend_public` | Website publik baru (Next.js) | Aktif dikerjakan (Okt 2026) |
| `front_jamaah` | Frontend Next.js fase awal | ⚠️ kemungkinan tidak aktif |
| `MOBILE-APP-ROSANA-BENAR` | Mobile app Flutter Rosana Travel — https://github.com/mahruswidip/MOBILE-APP-ROSANA-BENAR | **Aktif** |

### Website publik `rosanatravel.com`
- **Masih CI3 lama** di Rumahweb (dikonfirmasi owner 18 Nov 2026); DNS belum dipindah ke VPS.
- Pengganti: Next.js di `frontend_public` — sudah lolos lint, typecheck, build. **Cutover belum dilakukan, masih rencana.**
- Backup mingguan otomatis Rumahweb **nonaktif** (batas penggunaan hosting), termasuk Nov 2026.

### Mobile app
- **Aktif** (dikonfirmasi owner 18 Nov 2026). Repo: `mahruswidip/MOBILE-APP-ROSANA-BENAR`.
- Versi Flutter, state management, arsitektur, cara rilis, distribusi: ⚠️ belum diketahui → **tugas audit pertama Eng Mobile** (baca `pubspec.yaml`, struktur `lib/`, README), lalu update bagian ini.
- Proyek POS SaaS (Flutter/Riverpod/Drift) adalah proyek lain — bukan app Rosana Travel.

### Deployment & backup
- Backend dan frontend deploy terpisah. GitHub Actions pernah dipakai untuk frontend (pernah gagal).
- Backup SQLite pernah dibuat & diverifikasi (2 Okt 2026).
- Snapshot VPS `rosana-group-pre-php2shell-patch-20261023` (23 Okt 2026) — dibuat **sebelum** patch keamanan.
- Restore end-to-end, prosedur rollback teruji, frekuensi backup otomatis: ⚠️ belum ada.
- CI/CD terbaru, environment dev/staging: ⚠️ PERLU DIKONFIRMASI.

## 5. Alur utama

### Booking
1. Admin membuat master **Paket**.
2. Satu Paket punya banyak **Keberangkatan**.
3. Admin mengatur tanggal, kuota, info operasional per Keberangkatan.
4. Keberangkatan berstatus `OPEN` bisa menerima booking.
5. Jamaah memilih paket + keberangkatan.
6. Sistem membuat/menghubungkan `booking_group` ke keberangkatan.
7. Jamaah dimasukkan sebagai `booking_participants`.
8. Harga disimpan sesuai paket atau penyesuaian khusus.
9. Booking terhubung ke invoice dan pembayaran.

### Pembayaran
1. Booking punya harga total.
2. DP standar 30%, pelunasan 70%, boleh bertahap.
3. Kursi terpakai saat booking `dp_paid` atau `paid` (lihat RULES).
4. Payment gateway: **Xendit — LIVE PRODUCTION** (dikonfirmasi owner 18 Nov 2026). Pembuatan invoice Xendit sudah diimplementasikan; DP via Xendit pernah berhasil diuji; ada sinkronisasi status (`payment:sync-pending`).
5. Midtrans pernah dipakai/dibahas, tapi bisnis sudah pindah ke Xendit.
- ⚠️ PERLU DIKONFIRMASI: metode pembayaran Xendit yang aktif, webhook production end-to-end di versi terbaru, versi API Xendit.

### Dokumen jamaah
- Kebutuhan: dokumen jamaah, paspor, visa, manifest. Modul room assignment pernah ada.
- ⚠️ PERLU DIKONFIRMASI: model penyimpanan dokumen, alur verifikasi paspor/visa, generator manifest.

### Notifikasi
- Mekari Qontak dipakai untuk CS dan AI Control Tower.
- Email invoice pernah masuk daftar kerja.
- ⚠️ PERLU DIKONFIRMASI: notifikasi otomatis dari backend, template, trigger, antrean, retry.

### Expo
- Fitur lead capture dan resource untuk kegiatan expo (backend + frontend).

## 6. Integrasi pihak ketiga
| Layanan | Kegunaan | Status |
|---|---|---|
| Xendit | Payment gateway | **LIVE PRODUCTION** |
| Mekari Qontak | WhatsApp/CS Rosana & Nipindo | Integrasi AI Control Tower pernah diuji berhasil |
| Cloudflare | DNS | Aktif |
| Vultr | VPS | Aktif |
| GitHub Actions | Deploy frontend | Pernah dipakai |

Email provider, object storage: ⚠️ PERLU DIKONFIRMASI.

## 7. Status (per 18 Nov 2026, belum diverifikasi)
**Pernah dilaporkan selesai:** backend Laravel + Filament, API jamaah (Sanctum), master Paket & Jamaah, Booking & Participants, Keberangkatan + kompatibilitas API, invoice/pembayaran (fase sebelumnya), portal jamaah, Expo lead capture.

**Sedang dikerjakan / belum terverifikasi:**
- Migrasi data jamaah & sistem lama.
- Validasi relasi booking–jamaah–invoice–pembayaran.
- Migrasi website ke VPS.
- Worker/scheduler pembayaran & notifikasi.
- Backup & kesiapan rollback produksi.

**Masalah yang diketahui:**
- Shared hosting pernah 503 karena inode penuh.
- Konfigurasi worker/scheduler produksi belum ditemukan saat inspeksi.
- `/api/pakets` masih pakai auth Sanctum → belum cocok untuk website publik.
- Backup/snapshot VPS belum terverifikasi.
- PHP-FPM 8.3.31 punya isu keamanan (PHP2Shell); patch 8.3.36 tersedia.
- Deploy frontend via GitHub Actions pernah gagal.

**Status produksi end-to-end:** ⚠️ PERLU DIKONFIRMASI.

## 8. Area berisiko (hati-hati ekstra, wajib QA)
- **Pembayaran:** status transaksi, callback gateway, pencatatan DP/pelunasan.
- **Kuota:** hitungan kursi berdasarkan status pembayaran.
- **Migrasi data:** relasi booking, jamaah, paket, keberangkatan.
- **Histori keuangan:** reschedule tidak boleh merusak snapshot invoice/pembayaran lama.
- **API lama:** kompatibilitas `paket_id` vs `keberangkatan_id`.
- **Data pribadi:** identitas & paspor jamaah.
- **Infrastruktur:** patch PHP, backup, rollback, worker/scheduler.

## 9. Konvensi kerja
- Pola Laravel: Controller, Model, Service, Policy. Admin pakai Filament.
- QA standar: lint, typecheck, tests, build.
- Browser/runtime QA dilakukan manual oleh owner kalau agent tidak punya akses.
- Pengujian keuangan pakai data sintetis, jangan sentuh transaksi asli.
- Branching, format commit, PR policy, standar Pint/PSR, coverage: ⚠️ PERLU DIKONFIRMASI.

## 10. Prioritas untuk tim agent
1. **P0 — Keselamatan produksi:** verifikasi backup & restore sebelum perubahan berisiko. Patch PHP.
2. **P0 — Integritas pembayaran:** Xendit live; jangan rusak alur transaksi & histori pembayaran.
3. **P1 — Konsistensi booking:** pertahankan aturan kapasitas `dp_paid`/`paid`.
4. **P1 — Audit repository:** periksa implementasi aktual sebelum merencanakan fitur/refactor.
5. **P2 — Refund:** pastikan refund manual bisa diinput & terdata di admin (aturan sudah final, lihat RULES A.10–15).
