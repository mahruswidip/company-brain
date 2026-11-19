# POS Kedai AWS — RULES

> Aturan yang TIDAK BOLEH dilanggar agent.

## A. Repo & lingkungan
1. Kerja hanya di `~/Documents/pos-saas`. **Jangan edit / jalankan apa pun dari `~/pos-aws`** (prototipe).
2. Pakai toolchain dari `.toolchain/` (Flutter/JDK/PHP tidak di PATH).
3. Base URL non-lokal wajib HTTPS.

## B. Uang & harga
1. Harga satuan = harga dasar channel + opsi (override channel kalau ada; fallback `direct`).
2. Total = Σ (harga satuan × qty). **Integer rupiah**, kalkulasi pakai BigInt, harga final wajib > 0.
3. **Tidak ada** pembulatan, pajak, atau diskon. Jangan menambahkannya tanpa keputusan owner.
4. Baris pesanan menyimpan **snapshot harga & nama**. Repository menolak snapshot yang harganya sudah berbeda dari katalog.
5. Tunai: `received >= total`, kembalian dihitung. Non-tunai: `received` harus kosong.
6. Metode `platform` hanya valid untuk channel non-`direct`, dan sebaliknya (`order_repository.dart:344`).
7. Server memvalidasi ulang total & bukti checkout (`CheckoutProof`). Nomor antrean unik per `(tenant, business_date)`.
8. Status: `pending` (belum bayar) → `paid`. **Belum ada void/refund.** Pembayaran langganan bisa di-reversal (cabut akses, bukan refund).

## C. Waktu
1. Aplikasi server berjalan di **UTC**. Pernah ada bug timezone di deploy (data bergeser −7 jam, sudah dibetulkan) — jangan ubah timezone server.
2. Tanggal bisnis memakai **zona waktu tenant**, diterbitkan server (jam HP tidak dipercaya).

## D. Sync & perangkat
1. Sync idempoten (UUID + fingerprint SHA-256). Konflik → ditolak 409. Jangan dilonggarkan.
2. 1 perangkat aktif per tenant. Jangan dilonggarkan tanpa keputusan owner.

## E. Printer
1. ESC/POS **mode teks**, bukan raster.

## F. Produksi & keamanan
1. **Perubahan DB produksi dan pembuatan akun admin butuh persetujuan eksplisit owner.** Migrasi PostgreSQL butuh persetujuan tersendiri (README).
2. Deploy produksi → wajib approval owner.
3. Jangan tulis password / API key / keystore ke repo, brief, atau output.
4. File kunci signing APK (keystore apa pun yang dipakai `scripts/release-android.sh`) jangan dihapus / dipindah / di-regenerate, dan jangan ganti kunci signing — update in-app akan gagal di HP kasir.
5. Rilis APK lewat `scripts/release-android.sh` → wajib approval owner (update in-app menyentuh semua HP kasir).

## G. Kualitas
- Sebelum PR: `dart format`, `flutter analyze`, `flutter test`, `vendor/bin/pint --test`, `composer test` lolos.
- Commit bahasa Inggris, Conventional Commits.
