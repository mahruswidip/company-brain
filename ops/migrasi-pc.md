# Migrasi kantor AI: Mac → PC kantor (Windows + WSL2)

Disusun 19 Nov 2026 dari pengecekan langsung di Mac. Tidak ada isi secret di dokumen ini.
Mac tetap jalan: service Paperclip tidak dihentikan, tidak ada export yang dijalankan.

## 1. Repo dan remote GitHub

| Folder di Mac | Remote | Status |
|---|---|---|
| `~/companyku/company-brain` (symlink `~/company-brain`) | `mahruswidip/company-brain` (alias `github-brain`) | **Belum ada remote.** Repo git baru dibuat 19 Nov, belum ada commit. Menunggu deploy key dipasang. |
| `~/Documents/pos-saas` | `mahruswidip/pos-saas` (alias `github-pos`) | Sudah di GitHub. `main` + `feat/ui-kasir-tahap1`. |
| `~/baditourweb` (frontend/ + backend/) | `mahruswidip/baditour` (alias `github-badi`) | Sudah di GitHub. Hanya `main`. |
| `~/companyku/plugins/kantor-virtual` | `mahruswidip/kantor-virtual` (alias `github-kantor`) | Sudah di GitHub. `main`. |

Belum ada di GitHub / tidak ditemukan sebagai git di Mac ini:
- `~/ai_video_maker_claude`: **bukan git repo** (lihat bagian 2).
- `~/pos-aws`: prototipe UI Flutter lama, bukan git. Brief bilang jangan dipakai. Tidak perlu pindah.
- Kode Rosana Travel dan Rosana Collection: tidak ada repo git di Mac ini. Menurut brief kodenya ada di VPS. **Belum diverifikasi**, perlu dicek di VPS sebelum migrasi.
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
- `clients/*/brand.env` berisi data brand; **periksa isinya sebelum disalin atau di-commit** (belum dibaca isinya).
- Usul: bagian kecil (skill, tools, docs, profile/voice, fonts, flyer) cukup lewat git. Footage dan reels dipindah manual (disk eksternal atau rsync), jangan lewat GitHub.
- 13 skill Paperclip menunjuk ke `~/ai_video_maker_claude/.claude/skills`. Di PC path ini berubah, skill perlu diimpor ulang.

### `~/baditourweb/assets` (3,6 GB)
Footage dokumentasi keberangkatan 2 Nov 2026, 1.077 file. Tidak ada di GitHub. Pindah manual.

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
  - Rosana Collection, Rosana Travel, Onboarding: **path tidak terbaca**, cek di UI.
  - Semua path ini absolut Mac dan harus diganti path WSL di PC.
- **Skill (23, semua `local_path`):** 13 dari `~/ai_video_maker_claude/.claude/skills` (ig-*, agentmail, complain, slack, suggestion-box, dan lain-lain), 9 bawaan `@paperclipai/server/skills` (paperclip, paperclip-board, paperclip-create-agent, dan lain-lain), 1 `first-task`. Skill bawaan ikut terpasang dengan CLI. Skill buatan sendiri butuh foldernya ikut pindah.
- **Routine:** tidak ada yang terbaca lewat API (daftar kosong). Belum diverifikasi lewat UI.
- **Plugin:** hanya `mahruslabs.kantor-virtual`.
- **Service Mac (launchd, khusus Mac):** `~/Library/LaunchAgents/ing.paperclip.paperclipai.plist`. Menjalankan `paperclipai run --instance default`, `RunAtLoad`, `KeepAlive`, `PAPERCLIP_HOME=~/.paperclip`, PATH `/usr/bin:/bin:/usr/sbin:/sbin:~/.local/bin:/opt/homebrew/bin`. Di WSL2 tidak berlaku; ganti dengan systemd (kalau diaktifkan di WSL) atau `paperclipai service install` di PC.
- **PATH Mac:** `~/.zshrc` hanya menambah `~/.local/bin`. Homebrew: `node`, `php`, `composer`, `ffmpeg`, `gh`, `gitleaks` terpasang. Di WSL pasang padanan lewat apt atau nvm.
- **Postgres.app:** terlihat berjalan di Mac. Belum dicek apakah dipakai Paperclip atau hanya pos-saas lokal. Paperclip punya folder `db/` sendiri (155 MB).
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

**Rekomendasi sementara:** A untuk struktur (agent, project, skill, task) dan B hanya kalau riwayat penuh dibutuhkan. Uji A dulu dengan `export:preview`, lalu `import --dry-run` di PC.

## 6. Urutan yang disarankan di PC

1. Pasang WSL2 (Ubuntu), Node, git, php/composer, ffmpeg, gitleaks, `paperclipai` versi `2026.1106.0`.
2. Buat key SSH baru; pasang sebagai deploy key per repo.
3. Clone empat repo ke folder di dalam WSL (bukan `/mnt/c`, supaya cepat).
4. Pindahkan footage/reels manual.
5. Jalankan Paperclip, import perusahaan (opsi A), set ulang path workspace dan skill.
6. Pasang plugin `kantor-virtual`, cek halaman `/MAH/kantor`.
7. Setelah PC stabil: matikan service Mac, cabut key lama.

## 7. Yang belum dicek

- Isi `clients/*/brand.env` dan apakah ada secret di `ai_video_maker_claude`.
- Lokasi kode Rosana Travel dan Rosana Collection.
- Routine di UI Paperclip.
- Path workspace Rosana Collection, Rosana Travel, Onboarding.
- Isi sebenarnya paket export (belum ada `export:preview`).
- Apakah Postgres.app dipakai Paperclip.
