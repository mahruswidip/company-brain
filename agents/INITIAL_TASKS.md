# Tugas awal — dibuat Kepala Kantor di hari pertama

Urut sesuai prioritas. Task 🔒 = butuh approval owner sebelum ada perubahan di produksi.

## Minggu 1 — Keselamatan produksi & fondasi
| # | Task | Agent | Catatan |
|---|---|---|---|
| 1 | **Collection: cek backup SQLite produksi.** Ada/tidak, lokasi, frekuensi. Kalau belum ada → usulkan skrip backup harian + salinan off-site. | Eng Rosana Collection | Baca-saja dulu. Pasang skrip = 🔒 |
| 2 | **Travel: buat backup baru + uji restore** di lingkungan non-produksi. Cek versi PHP di VPS (target ≥ 8.3.36). | Eng Rosana Travel | Snapshot 23 Okt dibuat sebelum patch. Perubahan di VPS = 🔒 |
| 3 | **POS: backup DB off-site** (sekarang hanya di VPS yang sama) + **backup kunci signing APK** di luar Mac. | Eng POS | Owner konfirmasi keduanya belum ada. Keystore jangan pernah di-commit ke git. Tujuan backup ditentukan owner. 🔒 |
| 4 | **Badi Tour: commit frontend, init git backend, buat repo GitHub privat** keduanya. Cek .gitignore sebelum commit pertama. | Eng Badi Tour | Disetujui owner |
| 5 | **POS: buat repo GitHub privat** untuk `pos-saas`. Pastikan keystore & .env masuk .gitignore. | Eng POS | Disetujui owner |
| 6 | **Jalankan Graphify** di semua repo, simpan `GRAPH_REPORT.md` ke `company-brain/<project>/graph/`. | Masing-masing engineer | Gratis (tanpa kredit LLM untuk kode) |

## Minggu 2 — Audit & verifikasi brief
| # | Task | Agent |
|---|---|---|
| 7 | **Collection: audit kebenaran aplikasi** vs permintaan Bu Ika & RULES.md (ongkir, invoice, stok, fee, refund). Hasil: daftar temuan, urut dari paling berisiko. | Eng Rosana Collection → QA Reviewer |
| 8 | **Collection: cek `normalizeCostPrice()`** (0 vs null) dan validasi supplier di form/service/bot vs keputusan CONFIRMED. | Eng Rosana Collection |
| 9 | **Travel: audit idempotensi webhook Xendit** (tidak boleh pembayaran ganda) + catat metode pembayaran & versi API yang aktif. | Eng Rosana Travel → QA Reviewer |
| 10 | **Travel: pastikan refund manual bisa diinput & terdata di admin.** Kalau belum ada, usulkan desain. | Eng Rosana Travel |
| 11 | **Badi Tour: isi link sosial di `site.ts`** + cocokkan site.ts & seeder dengan Data resmi. | Eng Badi Tour |
| 12 | **Badi Tour: isi `voice.md`** + usulan daftar kompetitor. | Social Media Lead + Trend Scout |

## Minggu 3 — Mulai produksi rutin
| # | Task | Agent |
|---|---|---|
| 13 | **Mobile Rosana Travel: audit repo** (versi Flutter, state management, cara rilis) → update BRIEF. | Eng Mobile |
| 14 | **Collection: rancangan rekonsiliasi ongkir JNE di aplikasi** (import tagihan, cocokkan AWB, selisih laba/beban). | Eng Rosana Collection |
| 14b | **Collection: desain flag "harga beli belum lengkap"** di produk/order + pemisahan di laporan laba. | Eng Rosana Collection → QA → approval owner |
| 15 | **Badi Tour: konten rutin** — rencana mingguan, 1 reel per sesi, flyer sesuai paket terbaru. | Tim creative |

## Ditunda
- Meta Pixel / GA di website Badi Tour (keputusan owner: nanti).
- CollectionClaw (keputusan owner: ditunda).
