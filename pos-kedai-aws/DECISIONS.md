# POS Kedai AWS — DECISIONS

> Format entri baru: `## [YYYY-MM-DD] Judul` → Keputusan / Alasan / Dampak.

## Katalog snapshot + harga per channel
- **Keputusan:** harga & nama disimpan sebagai snapshot di baris pesanan; harga per channel (direct/gofood/grabfood/shopeefood).
- **Alasan:** histori pesanan tidak berubah saat harga katalog berubah ✂️.

## Server menerbitkan tanggal bisnis & bukti checkout
- **Alasan:** jam HP tidak bisa dimanipulasi.

## Satu perangkat aktif per tenant
- **Alasan:** mencegah berbagi akun.

## Sync idempoten (UUID + fingerprint SHA-256)
- **Alasan:** aman retry saat offline; konflik ditolak 409.

## Cetak ESC/POS mode teks
- **Alasan:** mode raster bikin printer tersendat & struk terpotong. [INGATAN]

## VPS sendiri, bukan AWS
- Alasan tidak tertulis di repo ⚠️ (kemungkinan biaya — belum pasti). [INGATAN]

## Update in-app self-hosted
- **Keputusan:** update APK via server sendiri (SHA-256 + PackageInstaller), tanpa Play Store. [INGATAN]
