# Rosana Collection — DECISIONS

> Keputusan yang sudah diambil. Jangan dibalik tanpa approval owner.
> Format entri baru: `## [YYYY-MM-DD] Judul` → Keputusan / Alasan / Dampak.

## [2026-11-18] Penanda harga beli belum lengkap
- **Keputusan:** disetujui. Produk/order dengan harga beli belum diketahui (disimpan `0`) diberi flag "harga beli belum lengkap"; laporan laba memisahkan/menandainya.
- **Alasan:** tanpa flag, laba terlihat lebih besar dari aslinya.

## [2026-11-18] Prioritas & penundaan CollectionClaw
- **Keputusan:** CollectionClaw ditunda. Prioritas: kestabilan & kebenaran aplikasi sesuai permintaan Bu Ika → rekonsiliasi ongkir di aplikasi → laporan keuangan.

## [2026-11-18] Fee, komisi, margin, refund, pembayaran
- **Keputusan:** fee jastip & komisi sales ditentukan owner Collection di admin (tanpa rumus). Margin = jual − beli. Tanpa pembulatan. Refund diinput admin, disetujui Bu Ika, tanpa potongan. Pembayaran hanya transfer manual + bukti bayar (tanpa Xendit).

## [2026-11-18] SOP penamaan produk v1.0 disetujui Bu Ika

## [2026-11-18] Selisih ongkir JNE
- **Keputusan:** `selisih = ongkir_customer − ongkir_jne`; positif = laba, negatif = beban, nol = impas.

## [2026-11-18] Harga beli Jastip belum diketahui = 0
- **Keputusan:** sistem memakai `0`; produk tetap bisa dibuat & diproses. Harga beli 0 karena belum diketahui tidak dianggap biaya aktual di laporan laba final.
- **Dampak:** `normalizeCostPrice()` (0 → null) perlu disamakan.

## [2026-11-18] Supplier opsional
- **Keputusan:** supplier `nullable` untuk semua produk Jastip (katalog & nonkatalog); tidak ada validasi universal yang mewajibkan supplier.

## Stack: Laravel 13 + Filament v5 native + SQLite
- **Keputusan:** stack dipertahankan; tidak ganti framework/package tanpa kebutuhan jelas.
- **Alasan pemilihan awal:** ⚠️ PERLU DIKONFIRMASI.

## Ready Stock dan Jastip dipisah sebagai alur order
- **Keputusan:** masing-masing punya form & service; aturan shipping berlaku untuk keduanya.

## Service layer untuk logika bisnis
- **Keputusan:** produk via `ProductCreateService`, stok via `StockMovementService`, Jastip via `JastipOrderService`. Logika tidak bergantung pada form Filament. Invoice tidak ditulis langsung dari bot.

## Draft tanpa kode pos
- **Keputusan:** draft boleh tanpa kode pos; kode pos wajib saat minta quote otomatis.

## Ongkir wajib eksplisit sebelum konfirmasi
- **Keputusan:** nilai kosong ≠ gratis ongkir; tidak ada fallback otomatis ke manual.
- **Alasan:** mencegah invoice dengan nilai pengiriman tidak jelas.

## Snapshot alamat penerima
- **Keputusan:** alamat order tidak ikut berubah saat master customer berubah.

## [2026-07-16] Cakupan MVP Jastip
- **Keputusan:** payment, refund, void, disbursement, withdrawal dikeluarkan dari cakupan MVP Jastip (walau sebagian modul sudah ada di sistem).

## CollectionClaw via Telegram dengan identitas internal
- **Keputusan:** 1 akun internal = 1 akun Telegram; tindakan final butuh konfirmasi; idempotency wajib. Pengguna awal Bu Ika & Mahrus.

## AI tidak diberi akses langsung ke DB/shell
- **Keputusan:** AI hanya parsing/usulan; Laravel memvalidasi dan mengubah data.

## AI parsing ditunda
- **Keputusan:** AI parser (OpenClaw) dikerjakan setelah alur deterministik stabil.

## Pembuatan produk via bot (Phase 2B.2)
- **Keputusan:** produk dibuat setelah konfirmasi; produk tanpa variasi = satu varian default; SKU varian pertama = kode produk toko; harga beli awal diisi `0` (sesuai keputusan 18 Nov 2026).

## [2026-07] SOP Standarisasi Penamaan Produk & Inventarisasi v1.0
- Dibuat untuk Bu Ika/admin. Disetujui Bu Ika (dikonfirmasi 18 Nov 2026).

## Pemisahan dari Rosana Travel
- **Keputusan:** backend, database, proses, dan workspace AI terpisah dari Rosana Travel.
