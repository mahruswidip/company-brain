# POS Kedai AWS — BRIEF

> Sumber: Claude di Mac owner, 18 Nov 2026 — cek langsung `~/Documents/pos-saas`.
> Tag: [REPO] dari file · [INGATAN] memory Claude Mac, belum dicek ulang (SSH ke VPS ditolak saat verifikasi).
> ✂️ = teks sumber terpotong saat dicopas; cek ulang. Lihat aturan membaca di `../README.md`.

## ⚠️ Penting
- **Repo asli: `~/Documents/pos-saas`.** `~/pos-aws` hanya prototipe UI Flutter (mock, bukan git) — **jangan edit / jalankan dari `~/pos-aws`.**
- **"AWS" = nama kedai makanan owner** (dikonfirmasi owner 18 Nov 2026), bukan Amazon Web Services. Tidak ada infra Amazon sama sekali (config `AWS_*` hanya bawaan Laravel).

## 1. Produk & bisnis
- [REPO] **"POS SaaS"** — POS Android **multi-tenant** untuk kedai makanan kecil. Tema UI: "Warm Espresso, Material 3".
- [REPO] Langganan **Rp100.000**, verifikasi & aktivasi manual ✂️.
- [INGATAN] Dipakai di kedai owner sendiri, 1 ✂️ + printer Bluetooth 58mm.
- Kedai: **AWS** (milik owner). **1 outlet**, Jl. Erlangga No. 116, Purworejo, Kota Pasuruan (depan Idaku Pasuruan). Telp: 0856-5555-6049 / 0895-3952-220.
- **Target saat ini: hanya Kedai AWS.** Belum dijual ke kedai lain walau arsitekturnya multi-tenant.
- **Roadmap:** pihak kedai sudah puas dengan fitur yang ada. Pajak, diskon, shift/tutup kas, stok, void/refund **tidak** direncanakan — jangan dikerjakan tanpa permintaan owner. Fokus: stabilitas, backup, perbaikan bug.

### Menu aktual (owner, 18 Nov 2026)
| Kategori | Item | Harga |
|---|---|---|
| Mie | Cwimie Biasa | 13K |
| Mie | Cwimie Komplit | 15K |
| Mie | Yamin Biasa | 13K |
| Mie | Yamin Komplit | 15K |
| Non Mie | Pangsit Tumis | 16K |
| Non Mie | Pangsit Kuah | 14K |
| Minuman | Es Teh | 4K |
| Minuman | Teh Hangat | 4K |
| Minuman | Es Jeruk | 5K |
| Minuman | Jeruk Hangat | 5K |
| Minuman | Es Sirup | 4K |
| Minuman | Es Sirup Susu | 5K |
- Bisa request pedas.
- Ini menu di brosur; data di aplikasi adalah sumber yang dipakai kasir. Kalau beda, laporkan — jangan ubah katalog produksi tanpa approval.
- Distribusi APK: **tidak lewat Play Store / App Store** (mungkin nanti). Update lewat mekanisme in-app self-hosted.

### Peran pengguna [REPO]
| Peran | Akses |
|---|---|
| Super Admin | Panel admin web ✂️, password 12–128 karakter |
| OWNER | Kelola kasir/PIN, katalog, zona waktu bisnis, daftarkan perangkat |
| CASHIER | PIN 6 digit (dibuat OWNER), login per perangkat terdaftar |
- Tidak ada peran dapur/admin lain; dapur hanya menerima tiket cetak.

## 2. Tech stack
| Bagian | Info |
|---|---|
| Mobile [REPO] | Flutter (Dart SDK ^3.13), ✂️ (state mgmt & DB lokal terpotong; DB lokal SQLite). **Android saja.** `applicationId` masih placeholder `com.example.pos_saas` |
| Backend [REPO] | PHP ^8.5, Laravel ^13.17, ✂️ (admin panel versi terpotong), PostgreSQL (`pdo_pgsql`), hash Argon2id |
| Infra [INGATAN] | **VPS tunggal** (bukan AWS) ✂️ + PHP-FPM + Postgres, domain `139-180-222-53.sslip.io`, HTTPS via certbot. Session/cache file, queue `sync`, tanpa worker |
| Printer [REPO] | Bluetooth Classic ESC/POS 58mm (struk + tiket dapur). [INGATAN] mode teks (bukan raster), keep-alive 90 detik |
| Hardware lain | Tidak ada scanner / laci kas |

### Path & environment
- Mac: `~/Documents/pos-saas` → `mobile/`, `backend/`, `docs/`, `scripts/`, `.toolchain/`.
- Toolchain Flutter/JDK/PHP ada di `.toolchain/`, **tidak di PATH**.
- Dev: lokal (`pos_saas_dev`, HP via `adb reverse`). Produksi: VPS.
- Base URL via `--dart-define=POS_API_BASE_URL`; non-lokal wajib HTTPS.

### Deploy [INGATAN]
- Tarball ke VPS ✂️. Backup `pg_dump` harian 03:00, simpan 14 hari (di VPS yang sama).
- Repo GitHub privat: **`mahruswidip/pos-saas`** (nama persis `pos-saas`, bukan `pos-sass`), remote `origin` via alias SSH `github-pos` (deploy key khusus repo). Branch `main` dan `feat/ui-kasir-tahap1` sudah di-push (19 Nov 2026).
- `scripts/release-android.sh`: build APK rilis + upload update in-app (self-hosted, SHA-256 + PackageInstaller, tanpa Play Store).

### Offline [REPO]
- Pesanan disimpan lokal, antre di `PosOutbox` (idempoten: UUID + `idempotencyKey`).
- Lease offline 72 jam: **rencana, belum jalan** (`phase3b1.md`).

## 3. Fitur & alur [REPO]
### Jual
1. Katalog di-sync dari server ✂️.
2. Simpan pesanan **pending** atau langsung **bayar**.
3. Saat bayar: app minta **tanggal bisnis + bukti checkout dari server**, ambil nomor antrean per hari, simpan pembayaran, antre cetak struk + tiket dapur, sync ke `POST /v1/cashier/orders`.

### Bayar nanti
- Pesanan disimpan tanpa bayar, tiket dapur langsung antre, konfirmasi bayar menyusul.

### Menu
- Item, kategori, grup opsi (varian pilih-satu, topping multi-pilih) dengan `min_select`/`max_select`, catatan maks 200 karakter.
- Harga per channel: `direct`, `gofood`, `grabfood`, `shopeefood`. Override opsional, fallback ke `direct`.

### Pesanan & pembayaran
- Tipe: `dineIn`, `takeaway` (+ `tableNumber`).
- Metode: `cash`, `qris` (statis, hanya dicatat), `platform` (ojol, dicatat manual). **Tidak ada payment gateway.**

### Kasir & owner
- Daftar transaksi hari ini, laporan harian (OWNER; tab Server & HP ini), cetak ulang dengan audit, edit pesanan ✂️.
- 1 perangkat aktif per tenant.

### Tidak ada di kode (grep nihil)
Pajak, service charge, diskon/promo, shift / buka-tutup kas, stok / bahan baku / resep, HPP, refund, void.
Apakah memang belum direncanakan: ⚠️ PERLU DIKONFIRMASI.

## 4. Integrasi pihak ketiga
**Tidak ada.** Ojol hanya channel harga + metode `platform`. QRIS hanya dicatat. Tidak ada akuntansi / WhatsApp.

## 5. Status (18 Nov 2026)
- [INGATAN] **LIVE di VPS sejak 2026-11-16.** HP kasir masih versi 0.1.0+1 — update pertama harus manual.
- [REPO] Branch aktif `feat/ui-kasir-tahap1` ✂️.
- [REPO] Test: backend 9 file Feature + 1 skrip Postgres; mobile 12 file test. Hasil: ⚠️ belum dijalankan.
- [REPO] Rencana/utang: lease offline 72 jam, rekonsiliasi ganti perangkat, kebijakan expiry/renewal langganan (`phase3b1.md`).

## 6. Area berisiko
- **Uang:** tidak ada void/refund/shift/rekonsiliasi kas; pesanan bisa diedit.
- **Kunci signing APK:** walau tidak lewat Play Store, Android hanya mau meng-update app yang ditandatangani kunci yang sama. Kalau kunci signing rilis hilang, update in-app gagal → HP kasir harus uninstall-install ulang → pesanan lokal yang belum sync hilang. Kunci yang dipakai `scripts/release-android.sh`: ⚠️ cek. **Belum di-backup di luar Mac** (owner, 18 Nov 2026) → tugas awal.
- **Backup DB:** hanya di VPS yang sama. **Backup off-site belum ada** (owner, 18 Nov 2026) → tugas awal.
- **Keamanan:** SSH root dari Mac ke VPS. Password super admin `admin@admin.com` sudah diganti owner (18 Nov 2026).
- **Printer:** raster bikin struk terpotong (sudah diganti mode teks); koneksi cold 3–9 detik.
- **Biaya:** hanya VPS.

## 7. Konvensi kerja [REPO]
- Dart: `dart format`, `flutter analyze`, `flutter test`. Struktur feature-first `features/<nama>/{data,domain,presentation}`. Jalankan build_runner setelah ubah tabel ✂️.
- PHP: `vendor/bin/pint --test`, `composer test` (SQLite in-memory).
- Commit: **bahasa Inggris, Conventional Commits** (`feat(...)`, `fix(...)`, `perf(...)`). Bahasa dokumentasi & pesan error UI: ✂️ cek.
- Toolchain dari `.toolchain/`, bukan PATH.

## 8. Belum dicek
Isi VPS (SSH ditolak), hasil test, detail halaman laporan.
