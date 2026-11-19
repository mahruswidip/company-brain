# Badi Tour — DECISIONS

> Format entri baru: `## [YYYY-MM-DD] Judul` → Keputusan / Alasan / Dampak.

## [2026-11-18] Data resmi dari owner
- **Keputusan:** PPIU 04012300238990002. Flyer Umrah Reguler (12 hr Rp38,6 jt / 16 hr Rp39,9 jt) dan Umroh Liburan (16 hr Rp37,9 jt) final. Paket 9 hari dan PPIU 0204240080456 tidak berlaku.
- **Catatan:** paket akan terus bertambah/berubah; sumber live = Filament admin/DB.

## [2026-11-18] Meta Pixel / GA ditunda
- **Keputusan:** belum dipasang; dipasang nanti (kemungkinan saat mulai iklan).

## [2026-11-18] Izin wajah jamaah
- **Keputusan:** semua footage jamaah boleh dipakai (sekarang & ke depan), termasuk klip menangis.

## [2026-11-18] Git & repo
- **Keputusan:** commit frontend & backend, buat repo GitHub privat untuk keduanya.

## Frontend static export
- **Keputusan:** Next.js `output: 'export'`.
- **Alasan:** Rumahweb hanya PHP, tanpa Node. [INGATAN]

## Backend terpisah di subdomain
- **Keputusan:** Laravel + Filament di subdomain; frontend baca paket live via API.
- **Alasan:** paket bisa diubah tanpa rebuild frontend.

## Snapshot statis paket di frontend
- **Keputusan:** `data/packages.ts` sebagai fallback & SEO.

## Hanya warna yang wajib
- **Keputusan:** warna brand wajib, gaya desain bebas. Dua desain sebelumnya ditolak karena "AI slop". [INGATAN]
- **Ditolak:** blok warna besar, frame arch / hero lebar, gaya Cormorant / Swiss-minimal.

## Airline sengaja kosong
- Flyer resmi tidak mencantumkannya ✂️ (alasan lengkap terpotong).

## Konten dikerjakan solo, satu reel per sesi
- **Keputusan:** tanpa subagent, 1 reel per sesi, riset tren sebulan sekali.
- **Alasan:** hemat kuota Claude Pro.

## Footage 2 Nov dipakai dengan label
- **Keputusan:** dipakai sebagai "Dokumentasi jamaah November 2026", bukan diklaim sebagai paket Desember ✂️.
