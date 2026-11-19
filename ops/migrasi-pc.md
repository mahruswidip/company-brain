# Migrasi kantor AI: Mac → PC kantor (Windows + WSL2)

Disusun 19 Nov 2026 dari pengecekan langsung di Mac. Tidak ada isi secret di dokumen ini.
Mac tetap jalan: service Paperclip tidak dihentikan, tidak ada export sungguhan yang dijalankan (hanya `export:preview`, hanya baca).

## 1. Repo dan remote GitHub

| Folder di Mac | Remote | Status |
|---|---|---|
| `~/companyku/company-brain` (symlink `~/company-brain`) | `mahruswidip/company-brain` (alias `github-brain`) | Git dibuat 19 Nov, 1 commit lokal. Push ke `main` menunggu deploy key yang diterima GitHub (lihat setup-log). |
| `~/Documents/pos-saas` | `mahruswidip/pos-saas` (alias `github-pos`) | Sudah di GitHub. `main` + `feat/ui-kasir-tahap1`. |
| `~/baditourweb` (frontend/ + backend/) | `mahruswidip/baditour` (alias `github-badi`) | Sudah di GitHub. Hanya `main`. |
| `~/companyku/plugins/kantor-virtual` | `mahruswidip/kantor-virtual` (alias `github-kantor`) | Sudah di GitHub. `main`. |

Belum ada di GitHub / tidak ditemukan sebagai git di Mac ini:
- `~/ai_video_maker_claude`: **bukan git repo** (lihat bagian 2).
- `~/pos-aws`: prototipe UI Flutter lama, bukan git. Brief bilang jangan dipakai. Tidak perlu pindah.
- Kode Rosana (di VPS, dicek owner 19 Nov 2026; sudah di GitHub semua, tidak ada salinan git di Mac):
  - `/var/www/rosana` → `git@github.com:mahruswidip/WEBSITE-ROSANA-GROUP-2026` (branch `main`, sinkron). Untracked: `kbihuinvitation/` (belum diputuskan, lihat OPEN_QUESTIONS.md).
  - `/var/www/collection` → `git@github.com:mahruswidip/WEBSITE-ROSANA-COLLECTION-2026` (branch **`master`**, sinkron, bersih).
  - `MOBILE-APP-ROSANA-BENAR` (Flutter) → `mahruswidip/MOBILE-APP-ROSANA-BENAR`, publik. Belum ada clone di Mac ini.
- Cadangan lama (tidak perlu pindah): `~/baditourweb-frontend-git.bak`, `~/baditourweb-backend-git.bak`, `~/companyku/company-brain.bak-20261118`, `~/companyku/company-brain.zip`.
- `~/baditourweb/assets/footage/` (3,6 GB) sengaja tidak masuk repo. Lihat bagian 2.

## 2. Folder di luar git

### `~/ai_video_maker_claude` (5,2 GB, bukan git repo)
| Subfolder | Ukuran | Catatan |
|---|---|---|
| `clients/badi/footage` | 3,6 GB | footage mentah |
| `clients/badi/reels` | 1,3 GB | output video (mp4 sampai 246 MB per file) |
| `clients/badi/flyer` | 54 MB | |
| `clients/badi/fonts`, `logobadi.jpg` | 1,6 MB | kecil |
| `clients/rosana/reels` | 342 MB | output video |
| `clients/nipindo` | 12 KB | hanya `profile.md`, `voice.md`, `brand.env` |
| `.claude/` dan `.agents/` | 212 KB masing-masing | skill dan konfigurasi |
| `tools/`, `docs/`, `riset/`, `AGENTS.md`, `CLAUDE.md` | kurang dari 100 KB | |

- Total 641 file video/audio. **Yang besar = `clients/badi/footage` + `clients/badi/reels` + `clients/rosana/reels` (≈5,2 GB).** Selain itu kecil.
- `clients/*/brand.env`: dicek 19 Nov (hanya nama variabel). Tidak ada API key/token/password. Isinya setelan visual: `TEXT_COLOR BOX_COLOR ACCENT_COLOR` (ketiga klien), badi juga `TITLE_FONT LINE_FONT BRAND_FONT TITLE_SIZE LINE_SIZE TEXT_FX TITLE_COLOR VIGNETTE GRADE LOGO END_BG TAGLINE TAGLINE_COLOR TOP_SHADE LOGO_TOP LOGO_TOP_W LOGO_TOP_Y LOGO_CROP`. Aman lewat Drive; kalau nanti ada rahasia ditambahkan, pindahkan manual.
- Usul: bagian kecil (skill, tools, docs, profile/voice, fonts, flyer) cukup lewat git. Footage dan reels dipindah manual (disk eksternal atau rsync), jangan lewat GitHub.
- 13 skill Paperclip menunjuk ke `~/ai_video_maker_claude/.claude/skills`. Di PC path ini berubah, skill perlu diimpor ulang.

### `~/baditourweb/assets/footage` (3,6 GB): duplikat
- Terkonfirmasi owner: salinan dari `~/ai_video_maker_claude/clients/badi/footage` (lokasi **kanonik**). Gw cek: kedua folder 1.077 file dengan nama dan ukuran sama persis (dibandingkan lewat nama dan ukuran, isi tidak di-hash).
- Owner yang menghapus salinan `~/baditourweb/assets/footage` setelah backup Drive terverifikasi. **Agent tidak boleh menghapus apa pun.**
- Tidak ada di GitHub (`assets/footage/` ada di `.gitignore` repo baditour).

### Plugin `kantor-virtual`
- Versi 0.6.4, sumber di `~/companyku/plugins/kantor-virtual` (sudah di GitHub).
- Terpasang dari path lokal absolut. Di PC: clone, `npm install`, `npm run build`, lalu `paperclipai plugin install <path absolut baru>`.
- `~/companyku/plugins/skills-lock.json` mencatat skill `paperclip-create-plugin` (sumber `paperclipai/paperclip`, commit `3dffb2c`).

## 3. SSH key (hanya dicatat, JANGAN disalin)

Di `~/.ssh` Mac:
- `agent_ro_rosana`, `agent_ro_rosana.pub`: key read-only agent untuk Rosana.
- `agent/`: folder key agent.
- `pos_vps_ed25519`: akses VPS (`pos-vps`, Ubuntu 24.04, host sama dengan `rosana-group`).
- `rumahweb_badi`: akses Rumahweb (hosting Badi Tour).
- Deploy key GitHub per repo: `github_kantor`, `github_pos`, `github_badi_fe` (dipakai alias `github-badi`), `github_brain`.
- Alias di `~/.ssh/config`: `pos-vps`, `github-kantor`, `github-pos`, `github-badi`, `github-brain`.

Di PC: buat key baru untuk setiap keperluan, daftarkan public key baru ke VPS, Rumahweb, dan sebagai deploy key GitHub. Setelah PC jalan, cabut deploy key lama yang tidak dipakai lagi. Deploy key GitHub di Mac dibuat tanpa passphrase; di PC sebaiknya pakai passphrase atau ssh-agent.

## 4. Konfigurasi Paperclip (company "Mahrus Labs", prefix MAH)

- **Versi CLI:** `paperclipai 2026.1106.0`, Node v26.13.1. Samakan versi di PC (plugin SDK juga dipin `2026.1106.0`).
- **Agent (11):** Eng POS, Eng Rosana Collection, Social Media Lead, Eng Rosana Travel, ATLAS, QA Reviewer, Designer, Video Editor, Eng Badi Tour, Trend Scout, Eng Mobile. Semua adapter `paperclip_runner`.
- **Project (6):** POS Kedai AWS, Konten & Desain, Badi Tour, Rosana Collection, Rosana Travel, Onboarding. Workspace yang terbaca:
  - POS Kedai AWS → `/Users/mahruswidi/Documents/pos-saas`
  - Konten & Desain → `/Users/mahruswidi/ai_video_maker_claude`
  - Badi Tour → `/Users/mahruswidi/baditourweb`
  - Rosana Collection, Rosana Travel, Onboarding, dan Konten & Desain: **tidak punya workspace** di export (Konten & Desain punya workspace `ai_video_maker_claude` di Paperclip tapi tidak diekspor karena tanpa `repoUrl` portabel).
  - Semua path ini absolut Mac dan harus diganti path WSL di PC.
- **Skill (23, semua `local_path`):** 13 dari `~/ai_video_maker_claude/.claude/skills` (ig-*, agentmail, complain, slack, suggestion-box, dan lain-lain), 9 bawaan `@paperclipai/server/skills` (paperclip, paperclip-board, paperclip-create-agent, dan lain-lain), 1 `first-task`. Skill bawaan ikut terpasang dengan CLI. Skill buatan sendiri butuh foldernya ikut pindah.
- **Routine:** API `/routines` mengembalikan daftar kosong, dan `export:preview` tidak memuat satu pun issue recurring/routine. Kesimpulan: tidak ada routine. (UI belum dilihat langsung.)
- **Plugin:** hanya `mahruslabs.kantor-virtual`.
- **Service Mac (launchd, khusus Mac):** `~/Library/LaunchAgents/ing.paperclip.paperclipai.plist`. Menjalankan `paperclipai run --instance default`, `RunAtLoad`, `KeepAlive`, `PAPERCLIP_HOME=~/.paperclip`, PATH `/usr/bin:/bin:/usr/sbin:/sbin:~/.local/bin:/opt/homebrew/bin`. Di WSL2 tidak berlaku; ganti dengan systemd (kalau diaktifkan di WSL) atau `paperclipai service install` di PC.
- **PATH Mac:** `~/.zshrc` hanya menambah `~/.local/bin`. Homebrew: `node`, `php`, `composer`, `ffmpeg`, `gh`, `gitleaks` terpasang. Di WSL pasang padanan lewat apt atau nvm.
- **Database Paperclip:** `embedded-postgres` bawaan (port 54329, data di `~/.paperclip/instances/default/db`, 155 MB), **bukan** Postgres.app. Backup otomatis tiap 60 menit, retensi 30 hari, di `data/backups`. Postgres.app di Mac tidak dipakai Paperclip.
- **Ukuran data:** `~/.paperclip/instances/default` 1,3 GB (`runtime` 1,0 GB, `db` 155 MB, `data` 113 MB, `logs` 7 MB). `~/.paperclip/cli` 2,6 GB hanya instalasi CLI, tidak perlu disalin.
- **Berisi kunci:** `instances/default/secrets/` (`master.key`, `decision-signing.key`, `paperclip-cloud-connector.json`). Jangan di-commit ke git.

## 5. Opsi memindahkan Paperclip

**A. Export/import perusahaan** (`paperclipai company export` / `import`)
- Hasilnya paket markdown. Default hanya `company,agents`. Bisa ditambah `projects,issues,tasks,skills`.
- Import mendukung `--target new|existing`, `--collision rename|skip|replace`, `--dry-run`; ada juga `export:preview` dan `import:preview` untuk uji dulu.
- Kelebihan: bersih, lintas OS, tidak membawa secret instance.
- Risiko: riwayat run, log, komentar, dan state runtime kemungkinan tidak ikut (perlu dibuktikan lewat `export:preview`, belum dijalankan). ID issue/agent bisa berubah. Skill `local_path` hanya referensi, foldernya harus ada di PC. Path workspace harus diatur ulang. Plugin harus dipasang ulang.

**B. Salin `~/.paperclip/instances/default`**
- Kelebihan: membawa semuanya (database, riwayat, secret).
- Risiko: berisi path absolut Mac di database, `config.json`, dan workspace. Database Postgres bawaan lintas OS (macOS ke Linux) belum tentu kompatibel kalau disalin sebagai file; sebaiknya lewat dump (`paperclipai db:backup`). `secrets/` ikut berpindah, jadi lewat jalur aman saja, bukan GitHub. Dua instance dengan identitas sama yang jalan bersamaan bisa bentrok (cloud connector, routine ganda). Versi CLI harus sama.

**Hasil `export:preview` (19 Nov 2026, include `company,agents,projects,issues,tasks,skills`, read-only, tidak ada yang ditulis):**
- **Ikut:** 11 agent (role, adapter dan runtime config tanpa path `cwd`, permissions, budget, tampilan), 6 project, 23 skill (13 skill lokal `konten-desain` ikut sebagai isi file: SKILL.md dan script; 9 bawaan Paperclip dan `first-task` ikut sebagai paket file), 24 issue (20 done, 3 cancelled, 1 backlog) dengan **110 komentar**, dokumen (rencana, continuation-summary, runbook), 3 attachment (2 blob, ±2 MB), serta `README.md`, org chart, dan `.paperclip.yaml`. Total 110 file, payload ±3,7 MB.
- **Tidak ikut:** riwayat run heartbeat dan log (isi `runtime/`, `logs/` ±1 GB), plugin (`kantor-virtual` harus dipasang ulang), secret dan API key agent (`envInputs` kosong; `secrets/` tidak ikut), path workspace Mac, routine (tidak ada), dan data biaya/aktivitas.
- **Peringatan dari Paperclip:** (1) workspace `ai_video_maker_claude` project Konten & Desain dilewati karena tanpa `repoUrl` portabel; (2) task `mah-17` dan `mah-18` mereferensi workspace itu, tidak bisa diekspor portabel.
- **Perhatian:** 14 file di export (terutama `continuation-summary`) masih berisi path absolut Mac (`/Users/mahruswidi/...`); hanya teks sejarah, tidak mempengaruhi fungsi. Status ikut apa adanya (MAH-5 dan MAH-6 tercatat `done` pada saat preview).
- **Catatan hasil:** karena run dan log tidak ikut, opsi A cukup untuk struktur, brief, task, dan komentar. Opsi B hanya perlu kalau riwayat run/log dibutuhkan.

**Rekomendasi:** pakai opsi A (export/import) lalu `import --dry-run` di PC; atur ulang workspace (`pos-saas`, `baditourweb`, `ai_video_maker_claude`) ke path WSL; pasang ulang plugin. Opsi B (salin instance) tidak dipakai kecuali ada kebutuhan riwayat.

## 6. Rencana di PC (Windows + WSL2)

1. Pasang WSL2 (Ubuntu), Node, git, php/composer, ffmpeg, gitleaks, `paperclipai` versi `2026.1106.0`.
2. **Deploy key baru per repo di PC** (jangan salin key Mac), alias SSH:
   | Alias | Repo |
   |---|---|
   | `github-rosana` | `mahruswidip/WEBSITE-ROSANA-GROUP-2026` |
   | `github-collection` | `mahruswidip/WEBSITE-ROSANA-COLLECTION-2026` |
   | `github-mobile` | `mahruswidip/MOBILE-APP-ROSANA-BENAR` |
   | `github-pos` | `mahruswidip/pos-saas` |
   | `github-badi` | `mahruswidip/baditour` |
   | `github-brain` | `mahruswidip/company-brain` |
   | `github-kantor` | `mahruswidip/kantor-virtual` |
   Owner memasang public key baru di tiap repo (write). Setelah PC jalan, cabut deploy key Mac yang lama.
3. **Clone 7 repo** ke dalam filesystem WSL (bukan `/mnt/c`): WEBSITE-ROSANA-GROUP-2026 (branch `main`), WEBSITE-ROSANA-COLLECTION-2026 (branch `master`), MOBILE-APP-ROSANA-BENAR, pos-saas, baditour, company-brain, kantor-virtual.
4. **Footage dan reels lewat Google Drive** ke lokasi kanonik `ai_video_maker_claude/clients/badi/footage` (dan `reels`, `flyer`). Salinan `baditourweb/assets/footage` tidak ikut dipindah.
5. Bagian kecil `ai_video_maker_claude` (`.claude/skills`, `tools`, `docs`, profile/voice, fonts, `brand.env`) lewat Drive atau salin manual. `brand.env` saat ini tidak berisi rahasia; kalau kelak berisi, pindah manual, bukan lewat Drive umum.
6. **SSH key agent read-only baru di PC** (pengganti `agent_ro_rosana`); public key dipasang ke VPS oleh owner. Key lama tidak disalin.
7. Jalankan Paperclip di PC, `company import` (dry-run dulu), atur ulang path workspace dan skill lokal, pasang plugin `kantor-virtual`, cek `/MAH/kantor`.
8. Setelah PC stabil: matikan service Mac, cabut key lama, owner hapus `~/baditourweb/assets/footage` (setelah backup Drive terverifikasi).

## 7. Keputusan / belum dicek

- `kbihuinvitation/` di VPS Rosana: masuk repo Rosana atau repo sendiri (ada di OPEN_QUESTIONS.md).
- Folder `~/pos-aws` (prototipe lama, bukan git): tidak dipindah kecuali owner minta.
- UI Paperclip belum dilihat langsung untuk routine; kesimpulan "tidak ada" berasal dari API dan export.
