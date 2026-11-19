# Rosana Travel — DECISIONS

> Keputusan yang sudah diambil. Jangan dibalik tanpa approval owner.
> Format entri baru: `## [YYYY-MM-DD] Judul` → Keputusan / Alasan / Dampak.

## [2026-11-18] Refund, pembatalan, pelunasan
- **Keputusan:** refund manual oleh tim keuangan ke rekening asal transfer, wajib diinput di admin. Tanpa pembulatan, tanpa denda telat, tanpa ketentuan/potongan pembatalan yang mengikat. Batas pelunasan = 1 minggu sebelum manasik.

## [2026-11-18] Konvensi git
- **Keputusan:** branch per tugas → lint/typecheck/tests/build → PR → review QA → merge setelah approval → deploy dengan izin terpisah. Berlaku untuk semua project.

## [2026-11-18] Xendit live production
- **Keputusan:** Xendit adalah payment gateway utama dan sudah live di production (konfirmasi owner). Midtrans tidak lagi jadi fokus.

## [2026-10-01] Aturan kapasitas kursi
- **Keputusan:** kursi terpakai saat `dp_paid` atau `paid`; `draft`/`invoiced`/`cancelled` tidak. Kuota = kapasitas total; sisa kursi dihitung.
- **Ditolak:** temporary hold 15 menit.
- **Alasan:** booking yang belum membayar tidak boleh mengonsumsi kapasitas permanen.

## Website publik baru pakai Next.js (`frontend_public`)
- **Keputusan:** menggantikan website CI3 lama. Cutover belum terkonfirmasi.

## Paket dan Keberangkatan dipisah (1:N)
- **Keputusan:** satu Paket punya banyak Keberangkatan.
- **Alasan:** satu jenis paket dijual di beberapa tanggal dengan kuota dan operasional berbeda.

## Keberangkatan = identitas operasional utama booking
- **Keputusan:** jamaah, room assignment, reschedule, dan kapasitas mengacu ke Keberangkatan.

## Kompatibilitas API lama dipertahankan sementara
- **Keputusan:** `paket_id` tetap diterima untuk kasus tidak ambigu (tepat satu keberangkatan OPEN). Klien baru diarahkan ke `keberangkatan_id`.

## Migrasi database additive
- **Keputusan:** tidak ada backfill/relink destruktif.
- **Alasan:** mencegah perubahan histori lama.

## Harga transaksi boleh beda dari harga master
- **Keputusan:** admin bisa menetapkan harga khusus per parent/booking.

## DP 30% / pelunasan 70%
- **Keputusan:** skema standar; pembayaran bertahap didukung.

## Backend = pusat data operasional
- **Keputusan:** frontend jamaah hanya mengonsumsi API backend; logika bisnis terpusat di backend.

## Isolasi Rosana Travel dan Nipindo di AI Control Tower
- **Keputusan:** infrastruktur & engine boleh bersama; data, knowledge, WhatsApp, konteks pelanggan terpisah.

## Keputusan keuangan & operasional sensitif butuh manusia
- **Keputusan:** prinsip human-in-the-loop untuk keuangan dan operasional sensitif (juga dipakai di AI Control Tower Rosana Group).

## Perubahan stack (riwayat)
- Laravel 10 + Filament v4 + Next.js 15 → Laravel 12 + Filament 5 + React/Vite (portal) + Next.js 16 (website publik baru). Terverifikasi audit Okt 2026.
- Alasan perubahan: ⚠️ PERLU DIKONFIRMASI.
