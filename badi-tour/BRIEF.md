# Badi Tour — BRIEF

> Sumber: Claude di Mac owner, 18 Nov 2026 — cek langsung repo & folder.
> Tag: [REPO] dari file kode · [FOLDER] dari `ai_video_maker_claude` · [INGATAN] memory Claude Mac, belum dicek ulang.
> ✂️ = teks sumber terpotong saat dicopas; cek ulang ke Claude Mac. Lihat aturan membaca di `../README.md`.

## ✅ Data resmi (keputusan owner, 18 Nov 2026)
Sumber kebenaran: **dua flyer final** di `assets/flyer-final-umrah-reguler.png` dan `assets/flyer-final-umroh-liburan.png`.

| Hal | Data resmi |
|---|---|
| No. PPIU | **04012300238990002** (nomor 0204240080456 di brief lama SALAH, jangan dipakai) |
| WhatsApp | Jombang **0812-7897-8948** · Surabaya **0821-2344-789** (nomor 0858-5509-4326 di brief lama tidak dipakai) |
| Sosial media (per cabang) | Surabaya: IG [@baditoursurabaya](https://www.instagram.com/baditoursurabaya/) · TikTok [@baditoursurabaya](https://www.tiktok.com/@baditoursurabaya) — Jombang: IG [@baditourjombang](https://www.instagram.com/baditourjombang/) · TikTok [@baditourjombang](https://www.tiktok.com/@baditourjombang) |
| Alamat kantor | Jl. A. Yani 214, Jombang, Jawa Timur |
| Tagline | "Perjalanan nyaman, ibadah lebih tenang." |
| CTA | "Konsultasi & daftar sekarang" |

### Paket (per flyer final 18 Nov 2026 — **paket akan terus bertambah/berubah**)
> Paket tidak tetap. Sumber data paket yang live = **Filament admin → DB** (website membaca via API). Agent jangan meng-hardcode daftar paket di bawah sebagai permanen; untuk konten, ambil paket terbaru dari admin/flyer terbaru, dan tanya owner kalau ragu.

**Umrah Reguler — Keberangkatan Desember 2026**
| Durasi | Harga | Bonus |
|---|---|---|
| 12 hari | Rp38,6 jt | Free city tour Taif + Nasi Mandhi |
| 16 hari | Rp39,9 jt | Free city tour Taif + Nasi Mandhi |
- Penerbangan: direct SUB – JED – SUB (PP).
- Hotel Mekkah: Snood / Nada Deafah. Hotel Madinah: Kayan International.

**Umroh Liburan — Promo Akhir Tahun**
| Durasi | Harga | Bonus |
|---|---|---|
| 16 hari | Rp37,9 jt | Free city tour Taif |
- Hotel Mekkah: Manazil / setaraf. Hotel Madinah: Royal Andalus / setaraf.
- Tanggal keberangkatan: **dinamis**, menunggu kabar dari Bu Berdi (owner Badi Tour). Jangan ditulis sebelum ada kabar.

**Sudah termasuk (kedua paket):** tiket pesawat direct PP, makan fullboard, perlengkapan umrah, hotel, air zamzam 5 liter, muthowif & tour leader, transportasi bus AC, visa Siskopatuh, city tour Mekkah & Madinah.
**Belum termasuk:** paspor, vaksin, transportasi daerah, perlengkapan pribadi.

- Paket 9 hari (Rp36,5 jt) di brief lama sudah tidak berlaku.
- Logo **"Hawa"** di flyer = logo kerja sama dengan company lain. Tetap dipertahankan di flyer sesuai flyer final.

### Keputusan lain (owner, 18 Nov 2026)
- **Izin wajah jamaah: sudah disetujui semua**, untuk footage sekarang maupun yang akan datang (termasuk klip menangis).
- Identitas kantor: ikuti pola flyer final (IG Surabaya + alamat Jombang + dua nomor WA).

## 1. Bisnis
- **Badi Tour & Travel**, PT. Rindu Ka'bah Suci — umrah/haji. [FOLDER]
- Positioning: **nyaman dan value** — bukan luxury, bukan murah.
- Target audiens: muslim Indonesia ✂️ (sisanya terpotong). Budget: ⚠️ PERLU DIKONFIRMASI.
- **Owner Badi Tour: Bu Berdiyani & Dr Berdi.** Bu Berdiyani pernah minta musik nasyid di reel; Dr Berdi sumber footage & kabar tanggal keberangkatan.
- **Admin & leads:** dipegang karyawan Bu Berdiyani yang memegang nomor WA kantor.

## 2. Website — tech stack
| Bagian | Info |
|---|---|
| Frontend [REPO] | Next 16.4.6, React 19.3.0, Tailwind 4.3.3, `output: 'export'` (static) |
| Backend [REPO] | Laravel ^13.17, Filament ^5.11, PHP ^8.3 (server PHP 8.4 [INGATAN]) |
| Database | Lokal SQLite; produksi ⚠️ (kredensial di `.env` server, tidak dicek) |
| Paket dinamis | Diubah dari Filament admin → DB → frontend baca live via `lib/packages-api.ts`, `data/packages.ts` = fallback/SEO ✂️ |
| Artikel & galeri | Masih statis ✂️ |
| Admin | MFA wajib [INGATAN] |
| Path Mac | `~/baditourweb/{frontend,backend,assets}` |

### Git & deploy
- Repo GitHub privat: **`mahruswidip/baditour`** (satu repo, root `~/baditourweb`, berisi `frontend/` dan `backend/`, branch `main`). Push via alias SSH `github-badi` (deploy key khusus repo). `assets/footage/` dan `.env` tidak ikut repo. `.git` lama frontend dicadangkan di `~/baditourweb-frontend-git.bak`.
- Hosting [INGATAN]: Rumahweb shared hosting, backend live di subdomain ✂️ (domain terpotong). SSH pakai key, port non-standar (detail tidak ditulis di sini).
- Deploy frontend [INGATAN]: `npm run build` → tar isi `out/` lewat SSH → `public_html`.
- Deploy backend [INGATAN]: tar app + `database/migrations` → `php artisan migrate --force`.
- Server: **tanpa node/rsync, exec dimatikan, jangan `composer install` di server.**
- Environment: **lokal saja, tidak ada staging.**

## 3. Fitur website [REPO]
- Halaman: Beranda, Tentang Kami, Paket ✂️, Galeri, Artikel, Hubungi Kami, sitemap, robots.
- Form `CtaForm` hanya membuka `wa.me` dengan pesan — **lead tidak disimpan di server**.
- Tombol WhatsApp floating, dua kantor (default Surabaya).
- Tracking (Meta Pixel / GA / gtag): **tidak ada**.

## 4. Sosial media
- **4 akun: IG & TikTok untuk cabang Surabaya dan Jombang** (lihat "Data resmi"). Link sosial di `site.ts` masih `#` → perlu diisi.
- Follower, kompetitor acuan, konten berperforma bagus: ⚠️ belum diketahui → Trend Scout & Social Media Lead mengumpulkan sendiri (follower dari profil publik, kompetitor dari riset niche umroh Jawa Timur), lalu minta owner menyetujui daftar kompetitor.
- Konten yang sudah dibuat [FOLDER]: 3 reel ter-render (`00-dokumentasi`, `02-umroh-4-rukun`, `03-khandamah`), rencana 10 reel di `plan-10-reels.md`, flyer 20 versi Desember + 6 versi Liburan.
- **Sudah diposting (owner, 18 Nov 2026):** reel terbaru hasil video maker Claude di Mac; flyer **v19** (Desember/Reguler) dan **v6** (Umroh Liburan).
- Alur kerja [FOLDER]: skill `/ig-*` (✂️ daftar terpotong), `tools/build_cinematic.sh` (ffmpeg), footage mentah → `shots`/`texts` tsv → render. 1 reel per sesi, tanpa subagent, riset tren sebulan sekali. Detail: `docs/alur-kerja-mingguan.md`.
- `voice.md`: **masih kosong** (`clients/badi/voice.md` dan `~/.claude/instagram/voice.md` masih template).
- Leads: ke WhatsApp. CTA: "konsultasi" via WA. DM keyword auto-reply: belum (TODO).

## 5. Brand & desain [FOLDER]
- Bahasa: Indonesia, **hangat, sopan, jelas**. Hindari klaim berlebihan & bahasa jualan agresif.
- Warna **wajib**: Emerald `#164B30`, Gold ✂️ (kode gold terpotong/tergabung — cek ulang). Pendukung: `#00AA29`, `#E5B900`, putih, Charcoal `#222B25`.
- Logo: pakai `logobadi.jpg` asli; jangan digambar ulang / di-recolor.
- Font: brief → Montserrat (judul) + Inter/Plus Jakarta ✂️ (opsional); website → Newsreader + Inter. Owner: hanya warna yang wajib, gaya bebas [INGATAN].
- Tagline: "Perjalanan nyaman, ibadah lebih tenang." — dipakai di web & flyer.
- Referensi gaya resmi: dua flyer final di `assets/`.
- Flyer: HTML → JPG 1080×1350 (`flyer/<campaign>/v*.html`).
- Tools di Mac: Canva, Xcode, ChatGPT ✂️. Ada `badi.cdr` di Downloads tapi CorelDRAW tidak terinstall.

## 6. Status (18 Nov 2026)
- **Selesai:** web live 2026-11-17, backend Filament + API, paket dari flyer resmi, 3 reel ter-render.
- **Dikerjakan:** artikel/galeri ✂️ [INGATAN].
- **Utang teknis (lama, sudah beres 19 Nov 2026: satu repo `mahruswidip/baditour`):** frontend belum di-commit & tanpa remote, backend tanpa git — **owner setuju: commit keduanya + buat repo GitHub privat** (tugas awal). Tidak ada tracking (Meta Pixel/GA **ditunda** — keputusan owner), link sosial `#`, airline sengaja kosong.
- **Perlu dicek:** `site.ts` & seeder sudah sesuai data resmi di atas (PPIU, WA, paket).
- **Rencana:** 10 reel, flyer, mulai posting.

## 7. Konvensi kerja
- Frontend: **Next 16 punya breaking changes — baca `node_modules/next/dist/docs/` dulu** (aturan di `AGENTS.md`). Struktur: `app/`, `components/`, `data/`, `lib/`. Tidak ada test; hanya `npm run lint`.
- Backend: Laravel + Filament, ada `CLAUDE.md` (Laravel Boost), folder `tests` + `phpunit.xml`. Apakah test rutin dijalankan: ⚠️.
- Git: belum ada konvensi branch/commit.
- Konten: per klien di `clients/<slug>/`, jangan tukar footage/harga/brand antar klien, sebut nama klien di awal jawaban, output wajib di dalam `clients/<slug>/`, balas bahasa Indonesia casual.

## 8. Belum dicek
Isi DB produksi, reel mana yang sudah diposting.
