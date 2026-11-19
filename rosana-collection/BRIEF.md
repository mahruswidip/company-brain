# Rosana Collection — BRIEF

> Sumber: dua rangkuman GPT owner, 18 Nov 2026 (fokus Juli–Okt 2026). Belum diverifikasi ke repository.
> Lihat aturan membaca di `../README.md`.

## 1. Bisnis
- **Fashion muslim**, terutama abaya.
- Model penjualan: **Ready Stock** dan **Jastip** (jasa titip). Ada kategori **Jastip Saudi**.
- Produk: abaya dengan variasi ukuran, warna, model (contoh: Abaya Al Shal Dante).
- Target pelanggan, lokasi toko, domain publik: ⚠️ PERLU DIKONFIRMASI.

## 2. Orang
| Peran | Nama |
|---|---|
| Owner / PIC | Bu Ika |
| Pengelola teknis | Mahrus |
| Pengguna internal | Bu Ika, admin operasional |
| Sales | Ada modul komisi sales; peran & hak akses ⚠️ |

- Pelanggan tercatat di modul customer. Apakah pelanggan bisa transaksi mandiri lewat website: ⚠️ PERLU DIKONFIRMASI.

## 3. Tech stack
| Komponen | Teknologi |
|---|---|
| Bahasa | PHP 8.3 |
| Framework | Laravel 13 |
| Admin | Filament v5 (native, dipertahankan) |
| Database | SQLite — dikonfirmasi aktif di produksi (audit 25 Okt 2026) |
| Authorization | Filament Shield / Spatie Permission |
| Audit log | Spatie Activity Log |
| Queue / cache / session | Driver database (Redis ada di VPS tapi tidak dipakai) |
| Testing | `php artisan test` (feature test & form test) |

- `routes/web.php` dipakai; `routes/api.php` belum ada (per inspeksi terakhir).
- Penambahan package / perubahan stack yang tidak perlu: dibatasi.

### Frontend
- **Website katalog** publik: https://rosanacollection.store/ — repo `mahruswidip/WEBSITE-ROSANA-COLLECTION-2026`. Framework: ⚠️ cek repo.
- Frontend operasional = Filament.
- Mobile app: tidak ada info.

### Hosting
- VPS Vultr `rosana-group`.
- Produksi: `/var/www/collection/backend_collection`, DB `database/database.sqlite`.
- Development VPS: `/var/www/collection-dev/backend_collection` (tercatat tidak aktif per 25 Okt 2026).
- Development juga pernah lokal via Laravel + Cloudflare Tunnel.
- Deploy via Git di server (pernah terhambat perubahan lokal `package-lock.json`).
- CI/CD, staging aktif: ⚠️ PERLU DIKONFIRMASI.

### Repository
- **Terkonfirmasi 19 Nov 2026 (cek git di VPS oleh owner):** repo = **`mahruswidip/WEBSITE-ROSANA-COLLECTION-2026`** (monorepo backend + frontend), di VPS `/var/www/collection`, branch utama **`master`** (bukan `main`), sinkron dengan GitHub, working tree bersih. `backend_collection` adalah foldernya di dalam repo itu.

### Service layer yang ada
`ProductCreateService`, `ProductPrimaryImageService`, `StockMovementService`, `JastipOrderService`, `OrderService`, `ShippingAddressService`, `CustomerReplyAutofillService`.
Service khusus pembuatan customer: ⚠️ belum pasti ada.

## 4. Alur utama

### A. Produk & stok
1. Admin membuat produk: nama, kategori, supplier, kode produk toko, harga, dll.
2. Struktur **parent product + variant** (warna/ukuran). Produk tanpa variasi → satu varian default.
3. Data: SKU, kode produk toko, barcode, harga beli, harga jual, fee penjualan, berat, foto, stok, reserved stock.
4. Semua perubahan stok lewat `StockMovementService`.

### B. Ready Stock
1. Pilih customer → masukkan produk → isi penerima & alamat → simpan draft.
2. Pilih ongkir (otomatis/manual) → konfirmasi → invoice.
- Tahapan pengiriman, resi, penyelesaian order: ⚠️ PERLU DIKONFIRMASI.

### C. Jastip abaya
1. Admin membuat order Jastip (draft).
2. Pilih customer atau input customer baru (nama, alamat, kota/kecamatan, WhatsApp, kode pos). Penerima boleh sama dengan customer.
3. Tambah satu/beberapa item (produk, jumlah, harga). Supplier dicatat.
4. Isi alamat pengiriman (draft boleh tanpa kode pos).
5. Tentukan ongkir secara eksplisit (quote otomatis atau manual).
6. Sistem simpan snapshot penerima & ongkir, hitung total.
7. Admin konfirmasi → invoice dibuat lewat service → aktivitas dicatat.
- Produk Jastip nonkatalog boleh pakai SKU internal. Harga beli boleh kosong saat order dibuat.
- ⚠️ PERLU DIKONFIRMASI: proses beli di Saudi, barang tiba di Indonesia, barang hilang/rusak, serah ke ekspedisi, update resi, konfirmasi diterima.

### D. Ongkir & ekspedisi
- Ekspedisi yang terbukti dipakai: **JNE**.
- **Rekonsiliasi ongkir** saat ini terbukti dikerjakan di **Excel/spreadsheet** (tagihan Juli, Agustus, September 2026 sudah diproses & digabung). Kolom: AWB/resi, customer, berat tagihan, ongkir JNE, ongkir customer, selisih, status pencocokan (`MATCH`, `TIDAK DITEMUKAN`, `COCOK NAMA + TANGGAL`).
- Aturan selisih sudah FINAL: positif = laba, negatif = beban, nol = impas (lihat RULES B.4).
- Di Laravel sudah ada: shipping quote, ongkir manual, `shipping_cost_estimated` & `shipping_cost_charged`, snapshot ongkir.
- Modul rekonsiliasi JNE di Laravel, import Excel tagihan JNE, pencocokan AWB otomatis: ⚠️ PERLU DIKONFIRMASI (kemungkinan belum).
- Integrasi tracking/resi API: ⚠️ PERLU DIKONFIRMASI.

### E. Pembayaran & keuangan
- Modul tercatat: invoice, pembayaran, bukti pembayaran, wallet, refund, void, disbursement/withdrawal, komisi sales, laporan laba-rugi, Product Fee Settings.
- Keputusan MVP Jastip (16 Jul 2026): payment/refund/void/disbursement/withdrawal **dikeluarkan dari cakupan MVP**, walau sebagian modulnya sudah ada di kode.
- Bukti pembayaran: satu invoice bisa punya beberapa bukti bayar (`InvoicePaymentProofsTest.php`).
- **Pembayaran: transfer manual + bukti bayar saja** (owner, 18 Nov 2026). Tidak ada payment gateway.
- Fee jastip & komisi sales diatur owner Collection di halaman admin (tanpa rumus tetap). Margin = harga jual − harga beli. Refund diinput admin, disetujui Bu Ika, tanpa potongan.
- Dashboard keuangan owner: rumus detail ⚠️ cek kode & kebutuhan Bu Ika.
- Lokasi penyimpanan bukti pembayaran mungkin di disk `local` — perlu dicek.

### F. CollectionClaw (bot Telegram)
Flow pembuatan produk yang sudah diimplementasikan:
1. User Telegram dipetakan ke akun internal (1:1). Pengguna awal: Bu Ika, Mahrus.
2. Bot memandu input produk → user kirim foto → cek duplikat → user konfirmasi.
3. `ProductCreateService` membuat produk; foto asli + hasil optimasi diproses; audit log; idempotency untuk callback berulang.

| Phase | Hasil | Commit |
|---|---|---|
| 1 | Telegram mapping, intake, audit, idempotency | `1ff70e2` |
| 2B.1 | Wizard pembuatan produk | `03e9447` |
| 2B.2 | Finalisasi produk, duplicate handling, media | `eeb0a13` |
| 3A | Jastip via Telegram — **belum terkonfirmasi selesai** (webhook pernah balas HTTP 200 di uji manual; wizard end-to-end, attachment, konfirmasi order, deploy produksi ⚠️) | – |
| 3B | Belum ada bukti selesai | – |
| OpenClaw / AI parser | Ditunda | – |

## 5. Integrasi pihak ketiga
| Layanan | Kegunaan | Status |
|---|---|---|
| Telegram Bot API | CollectionClaw | Diimplementasikan & diuji lokal |
| Cloudflare Tunnel | Webhook ke server lokal | Pernah dipakai |
| JNE | Ongkir & pengiriman | Terbukti di spreadsheet |
| WhatsApp parser | Parsing alamat/pesan customer | Pernah diimplementasikan |
| Spatie | Permission & activity log | Aktif |
| Mekari Jurnal / Accurate | Akuntansi | Hanya opsi, **bukan integrasi aktif** |

Payment gateway, email provider, object storage: ⚠️ PERLU DIKONFIRMASI.

## 6. Status (per 18 Nov 2026, belum diverifikasi)
**Ada di kode (inventaris Juli 2026, belum tentu teruji end-to-end di produksi):** produk & varian, foto, stock movement, customer, ready stock, order Jastip, invoice, pembayaran & bukti, wallet, refund/void, komisi sales, laporan laba-rugi, snapshot alamat, ongkir otomatis/manual, parser alamat WhatsApp, activity log, permissions, idempotency keys.

**Belum terkonfirmasi selesai:** Jastip via Telegram, integrasi AI/OpenClaw penuh, API auth untuk agent, rekonsiliasi ongkir di aplikasi, laporan keuangan owner versi terbaru, QA browser end-to-end.

**Bug & utang teknis yang diketahui:**
- Dropdown pemetaan User Internal di Filament tidak interaktif (workaround: Tinker).
- Telegram `/start` pernah tidak merespons.
- Deploy Git terhambat perubahan lokal `package-lock.json` di server.
- Bukti pembayaran mungkin tersimpan di disk `local`.
- Konsistensi SKU, harga beli kosong, duplikasi produk belum diputuskan final.

**Prioritas (owner, 18 Nov 2026):**
1. **Kestabilan & kebenaran aplikasi** — audit apakah aplikasi sudah benar, efektif, clean, dan sesuai yang diminta Bu Ika.
2. **Rekonsiliasi ongkir JNE di aplikasi** (menggantikan Excel).
3. **Laporan keuangan** untuk owner Collection.
- **CollectionClaw (Telegram) DITUNDA.** Jangan dikerjakan sampai owner membukanya lagi.
- SOP Standarisasi Penamaan Produk & Inventarisasi v1.0 **sudah disetujui Bu Ika** — boleh dijadikan acuan validasi.

## 7. Area berisiko
- **Keuangan:** total order vs invoice, harga beli kosong → profit tidak lengkap, selisih ongkir JNE vs customer, refund/void memengaruhi saldo, komisi sales memengaruhi margin, pembayaran/bukti ganda.
- **Data:** SKU duplikat, produk sama harga beda, stok tidak konsisten, alamat customer berubah memengaruhi order lama, salah parsing alamat WhatsApp, data pribadi customer, webhook Telegram dobel.
- **Infrastruktur:** SQLite di produksi, backup & recovery ⚠️ belum diketahui, perubahan DB produksi, file lokal di server mengganggu deploy, konfigurasi endpoint webhook.

## 8. Konvensi kerja
- Laravel 13 + Filament v5 native. Ikuti pola & service yang sudah ada. Jangan duplikasi business logic. Hindari package/abstraksi yang tidak perlu.
- Testing: `php artisan test`. Pernah: CollectionClaw Foundation 13 tests / 30 assertions PASS; shipping/address PASS. Full suite belum terbukti rutin dijalankan.
- Ada SOP "Standarisasi Penamaan Produk & Inventarisasi Rosana Collection" v1.0 (Juli 2026, untuk Bu Ika/admin) — status persetujuan ⚠️.
- Branching, format commit, PR policy, konvensi penamaan: ⚠️ PERLU DIKONFIRMASI.

## 9. Keputusan bisnis yang sudah CONFIRMED (owner, 18 Nov 2026)
| Aturan | Nilai |
|---|---|
| Selisih ongkir positif | Laba |
| Selisih ongkir negatif | Beban |
| Selisih ongkir nol | Impas |
| Harga beli belum diketahui | `0` |
| Supplier kosong | `null` diperbolehkan |
| Konfirmasi Jastip | Ongkir harus eksplisit |
| Total invoice | Subtotal + ongkir customer |

## 10. Wajib diverifikasi sebelum agent mengubah kode
1. **Backup SQLite produksi** — otomatis/tidak, frekuensi, lokasi, retensi, restore terakhir: semua belum diketahui. Ini harus beres dulu sebelum perubahan berisiko.
2. Konsistensi kode dengan keputusan CONFIRMED: `normalizeCostPrice()` (0 vs null), validasi supplier di form/service/bot.
3. Status Phase 3A (Jastip via Telegram).
4. Rekonsiliasi ongkir JNE: masih Excel atau sudah ada modul.
