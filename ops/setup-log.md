# Setup log: Paperclip di Mac

> Catatan perubahan setup Paperclip (bukan VPS; untuk VPS lihat `vps-hardening-log.md`). Tanpa rahasia.

## 2026-11-18: PATH service Paperclip (launchd)

**Gejala:** agent Claude baru (Social Media Lead, Trend Scout, Video Editor, Designer) paused dengan `harness_command_unavailable` ("Command not found in PATH: claude").

**Penyebab:** service berjalan lewat launchd dengan PATH minimal. `claude` ada di `~/.local/bin`, yang tidak termasuk. `paperclipai service install` tidak punya opsi env/PATH; `.env` instance tidak bisa menimpa PATH yang sudah ada (`override: false`).

**Perubahan:** di `~/Library/LaunchAgents/ing.paperclip.paperclipai.plist` ditambah `EnvironmentVariables.PATH` = `/usr/bin:/bin:/usr/sbin:/sbin:/Users/mahruswidi/.local/bin:/opt/homebrew/bin`. Shim `paperclipai` masih menambah direktori node di depannya, jadi PATH efektif service = PATH lama + `~/.local/bin` + `/opt/homebrew/bin`.

**Backup:** `~/Library/LaunchAgents/ing.paperclip.paperclipai.plist.bak-20261118`

**Rollback:**
```
U=$(id -u)
launchctl bootout gui/$U/ing.paperclip.paperclipai
cp -p ~/Library/LaunchAgents/ing.paperclip.paperclipai.plist.bak-20261118 ~/Library/LaunchAgents/ing.paperclip.paperclipai.plist
launchctl bootstrap gui/$U ~/Library/LaunchAgents/ing.paperclip.paperclipai.plist
```

**PERINGATAN:** setelah `paperclipai update` atau `paperclipai service install`, cek ulang PATH di plist (`plutil -p ~/Library/LaunchAgents/ing.paperclip.paperclipai.plist`). Keduanya bisa menimpa file itu. Gejala kalau hilang: agent Claude baru gagal verifikasi dengan `harness_command_unavailable`.

**Hasil (bootout + bootstrap, tidak ada run aktif):** health ok; test-environment claude dan codex pass (hello probe lolos); 11 agent idle/ready.

## 2026-11-18: 7 agent baru + project "Konten & Desain"

- Agent dibuat lewat API dari `agents/*.md`: Eng Rosana Collection, Eng Mobile, Social Media Lead, Trend Scout, Video Editor, Designer. Eng Badi Tour sudah ada, diselaraskan (prompt, budget 1000, heartbeat off, wakeOnDemand true). Prompt ketujuh agent sama persis dengan file.
- Semua: heartbeat off, wakeOnDemand true, budget 1000 sen, tanpa routine.
- Project "Konten & Desain" (workspace `~/ai_video_maker_claude`, non_git_path) untuk task Social Media Lead, Trend Scout, Video Editor, Designer.
- Uji MAH-17 (Trend Scout, daftar skill): cwd benar; skill `/ig-*` TIDAK muncul (skill proyek di `.claude/skills` tidak terbaca oleh agent). Belum diterapkan ke 3 agent lain. Opsi berikutnya: impor skill lewat menu Skills Paperclip.
- Catatan: project "Badi Tour" belum punya workspace (`~/baditourweb` belum diset).

## 2026-11-18: skill /ig-* lewat Skills Paperclip

**Cara impor** (API, bukan edit DB): `POST /api/companies/{id}/skills/scan-projects` dengan `mode: preview` untuk melihat kandidat, lalu `mode: import` dengan `selection: [{workspaceId, path, slug}]`. Sumber dipilih dari `.claude/skills/<slug>` di workspace project "Konten & Desain" (varian Claude; folder `.agents/skills` adalah varian Codex, beda path output `~/.Codex/...` vs `~/.claude/...`). Pasang ke agent: `POST /api/agents/{id}/skills/sync` dengan `mode: add`.

**Uji:** MAH-17 (sebelum impor): `/ig-*` tidak muncul. MAH-18 (setelah impor ig-viral ke Trend Scout): muncul. Tidak ada skill dijalankan.

**Hasil:** 13 skill ig-* terimpor. Pemasangan:
- Social Media Lead: semua 13.
- Trend Scout: ig-viral, ig-audit.
- Video Editor: ig-reel.
- Designer: ig-carousel.

**Catatan:**
- Nama skill di runtime memakai sufiks hash, mis. `ig-viral--a59e60da43` (bukan `/ig-viral` polos). Agent tetap menemukannya lewat deskripsi, tapi prompt yang menulis `/ig-*` secara literal bisa kurang pas.
- File pendukung (`swipe.py`, `hookscore.py`, `beats.py`) ikut tersalin ke folder skill agent (terkonfirmasi untuk ig-viral).
- Skill menulis output ke `~/.claude/instagram/...`, yaitu HOME terisolasi milik agent, bukan workspace. Gunakan path `clients/badi/...` di workspace untuk hasil yang harus bertahan (sesuai prompt Trend Scout).
- Skill adalah salinan versi di `~/.paperclip/instances/default/skills/...`. Kalau file di `~/ai_video_maker_claude/.claude/skills` diubah, cek status update skill di Paperclip.

## 2026-11-18: workspace project "Badi Tour"

Workspace tunggal `baditourweb` (`~/baditourweb`, non_git_path, primary) sebagai cwd, supaya satu agent menjangkau `frontend/` (repo git) dan `backend/` (belum git, lihat MAH-5). Sempat ada workspace terpisah frontend dan backend; diganti atas keputusan owner.

## 2026-11-18: budget ATLAS tetap 0

Keputusan owner: budget ATLAS dibiarkan 0. Alasan: di kode Paperclip, budget hanya membatasi agent jika nilainya > 0 (`budgetMonthlyCents > 0 && spent >= budget`), jadi 0 = tanpa batas, bukan nol. ATLAS (Kepala Kantor) mengoordinasi agent lain, dan batas keras 1000 sen bisa menghentikannya di tengah koordinasi. Agent lain tetap 1000 sen. Spend ATLAS dipantau manual.

## Status akhir setup (2026-11-18)

Setup Paperclip selesai. 11 agent idle/ready, tanpa routine. Task uji MAH-17 dan MAH-18 sudah done. Tidak ada task atau run baru yang dibuat setelah itu.

## 2026-11-19: plugin Kantor Virtual (terpasang, ready)

- Lokasi: `~/companyku/plugins/kantor-virtual` (git repo, di luar kode inti Paperclip). Paket `@mahruslabs/kantor-virtual`, manifest id `mahruslabs.kantor-virtual`.
- SDK: `@paperclipai/plugin-sdk` dipin persis `2026.1106.0` (sama dengan Paperclip terpasang).
- `paperclipai plugin init` gagal di instalasi ini (`Package package.json not found at .../packages/shared/package.json`): scaffolder butuh checkout monorepo + pnpm. Proyek ditulis manual mengikuti template scaffolder, pakai npm.
- Capability (hanya baca): `agents.read`, `issues.read`, `ui.page.register`, `ui.sidebar.register`, `ui.dashboardWidget.register`. Tanpa invoke/wakeup/write.
- Verifikasi: `npm run typecheck`, `npm test`, `npm run build` lolos.
- Capability disetujui owner; commit pertama `84c6d78`; `paperclipai plugin install` OK, id `9854b3f8-dfe1-432e-ae44-8c21bd6afedb`, status ready.
- Run agent: 79 run sebelum dan sesudah install (terakhir 02:13 UTC); plugin tidak memicu run.
- Batas: "menunggu owner" = task in_review atau ber-assignee user; tidak menangkap task done yang laporannya menunggu keputusan owner (contoh MAH-20) dan backlog tanpa assignee (MAH-7), yang muncul di Inbox.
- Peringatan: setelah `paperclipai update`, cek ulang plugin (`paperclipai plugin list`), naikkan pin SDK ke versi baru, jalankan ulang typecheck/test/build. API SDK bisa berubah antar versi.

## 2026-11-19: Kantor Virtual v0.2

- Terpasang ulang (uninstall + install; `plugin install` menolak "already installed"). v0.2.0 ready, commit di repo plugin.
- "Menunggu owner" kini = definisi Inbox: `GET /api/companies/:id/sidebar-badges` (`inbox`) + jumlah `isUnreadForMe` dari `GET /api/companies/:id/issues?touchedByUserId=me&inboxArchivedByUserId=me&status=<INBOX_MINE_ISSUE_STATUSES>`. Dipanggil dari UI plugin (same-origin, GET saja); bukan lewat capability worker. Peringatan: jika endpoint/query Inbox berubah setelah `paperclipai update`, hitungan ini ikut salah; cek ulang.
- Sidebar pakai kelas Tailwind host; label nama 2 baris; `?demo=1` = data palsu lokal tanpa panggilan API.
- Uji klik SVG (CDP, mouse event nyata): demo ATLAS -> `/MAH/issues/MAH-20`; live ATLAS -> `/MAH/agents/atlas/overview`.
- Run agent tetap 79 (terakhir 02:13 UTC) setelah semua uji.

## 2026-11-19: Kantor Virtual v0.3 (tampilan)

- Adegan SVG ditulis ulang di `src/ui/scene.tsx`: karakter ~2,7x lebih besar, tampilan per tim, rambut/hijab deterministik dari id agent, dekorasi per ruangan, lantai/dinding/jendela/pintu, Pantry. Definisi "menunggu owner" tidak diubah.
- Status: kerja = duduk + ngetik + gelembung task; idle = di Pantry (meja kosong); menunggu = tanda tanya kuning; paused/error = berdiri + lampu merah.
- Siang/malam sesuai jam WIB; paksa dengan `?time=night` atau `?time=day`. Pratinjau: `?demo=1`.
- Layout dihitung dinamis dari jumlah agent per zona (agent baru otomatis dapat meja; zona tak dikenal masuk "Ruang Lain").
- v0.3.0 terpasang (ready). Uji klik SVG ulang lolos (demo ATLAS -> `/MAH/issues/MAH-20`, live pantry ATLAS -> `/MAH/agents/atlas/overview`). Run agent tetap 79.

## 2026-11-19: Kantor Virtual v0.4 (gaya visual)

- Render diganti: peta tile top-down di `<canvas>` 2D (tanpa PixiJS/dependensi baru) + overlay SVG untuk teks, gelembung task, dan target klik. Peta statis digambar sekali; hanya karakter/monitor digambar ulang (30 fps, berhenti saat tab tersembunyi, hormati `prefers-reduced-motion`). Kode: `src/ui/{world,art,look,OfficeCanvas}.ts(x)`.
- Animasi: agent idle berjalan ke Pantry (sofa/beanbag), status kerja berjalan ke meja lalu ngetik + gelembung, menunggu = tanda tanya, paused/error berdiri + lampu merah. Jalur lewat pintu di dinding (lane antar meja).
- Aset: TIDAK ada aset pihak ketiga. Semua pixel art (tile, furnitur, karakter) original, digambar lewat kode di `src/ui/art.ts`; karakter deterministik dari id agent. Repo referensi (Parcha-ai/ai-office) hanya jadi inspirasi gaya/susunan; tidak ada sprite, tileset, latar, atau kode yang disalin. Lihat `README.md` di repo plugin.
- Tetap: sumber data, definisi "menunggu owner", capability baca-saja, klik, `?demo=1`, `?time=`, layout otomatis. Baru: `?demo=1&cycle=1` (agent demo bergantian idle/kerja supaya jalan kakinya terlihat).
- v0.4.0 terpasang (uninstall + install). Uji klik SVG lolos. Run agent tetap 79.

## 2026-11-19: Kantor Virtual v0.5 (proporsi & layout)

- Peta 640x~300 logis: skala ~1,75x di laptop 1440 lebar, karakter ±44 px (sebelumnya ±30), meja diperkecil (40 px; Kepala 56 px) dan karakter lebih terlihat (duduk menampilkan 17 baris badan). Muat satu layar 1440x900 tanpa scroll (11 agent); scroll baru muncul bila agent melebihi kapasitas (Eng >6 per baris, Pantry >21).
- Layout: Kepala (kecil) + Engineering (lebar) di atas; QA | Pantry | Studio Creative di bawah. Pantry: sofa di dinding menghadap meja kopi kecil, beanbag melingkar di sekeliling meja (ring 2 otomatis bila agent >11).
- Label nama: dihitung ulang tiap frame agar tidak saling tumpuk atau menimpa karakter (geser bawah/atas/samping; bila sempit disembunyikan dan muncul saat hover). Label meja memudar saat ada agent lewat.
- Paused/error: berdiri di belakang kursi sendiri (kursi digambar di depan kaki) + lampu merah; antar meja ada jarak.
- Jalan: rute lewat lane antar meja, walkway ruangan, pintu/koridor; antrian di belakang agent yang berjalan searah, offset lane per agent untuk papasan; batas tunggu 1 detik supaya tidak macet.
- Batas: kapasitas Pantry 21 slot; agent di zona tak dikenal ("other") masuk baris ruangan ekstra di bawah (boleh scroll). Tak ada perubahan sumber data, definisi "menunggu owner", capability, klik, `?demo`, `?time`.
- v0.5.0 terpasang (uninstall + install). Uji klik SVG lolos; run agent tetap 79.

## 2026-11-19: Kantor Virtual v0.5.1

- Papan nama meja: bila pemilik idle (di Pantry), opacity 40% dan skala 0,8; penuh (opacity 100%) saat di-hover. Tetap memudar 12% saat ada agent lewat.
- Pantry: slot beanbag dihitung dengan jarak busur sama sepanjang elips di sekeliling meja kopi (tes: rasio jarak maks/min < 1,12). Ring 2 (agent >11) memakai cara sama.
- Tidak ada perubahan lain. v0.5.1 terpasang; run agent tetap 79.

## 2026-11-19: Kantor Virtual v0.6 (mode 3D isometrik)

- Mode tambahan 3D: three.js r170 (MIT), kamera orthographic isometrik tetap (tanpa orbit/zoom/pan), dinding cutaway, objek low-poly dari primitive (meja, kursi, dual monitor, rak buku, tanaman, papan tulis, sofa, beanbag, mesin kopi, kamera+tripod, ring light), karakter chibi deterministik dari id agent (baju per tim, rambut, hijab, Kepala berjas). Label/gelembung/tanda tanya = CSS2DRenderer; klik -> halaman agent/task. Toggle 2D/3D di halaman; 2D default; pilihan disimpan di localStorage `kv-mode` per browser (`?view=2d|3d` menimpa sekali).
- Tanpa aset pihak ketiga selain library three.js (lisensi MIT, tercatat di README plugin).
- Bundel: `dist/ui/index.js` 50 KB (15 KB gzip; halaman 2D tidak berubah ukuran berarti) + `dist/ui/scene3d.js` 516 KB (135 KB gzip) hanya diunduh saat 3D dibuka.
- Catatan teknis penting: host Paperclip meng-import modul UI lewat Blob URL dan hanya menulis ulang spesifier bare (`react`, SDK ui) di teks modul utama. Karena itu: (1) jangan minify bundel UI utama (`from"react"` tanpa spasi tidak ditulis ulang); (2) code-splitting/import relatif tidak jalan; (3) bundel 3D dimuat dengan URL absolut `${location.origin}/_plugins/<id>/ui/scene3d.js` (id dicari lewat `GET /api/plugins` berdasarkan pluginKey). Cek ulang ini setelah `paperclipai update`.
- Performa 3D: geometri statis digabung jadi satu mesh (vertex color), bayangan statis (tidak di-update), render hanya saat ada yang bergerak/berubah, maks 30 fps, berhenti saat tab tersembunyi, hormati `prefers-reduced-motion`.
- Refactor: logika gerak dipindah ke `src/ui/sim.ts` (dipakai 2D & 3D). Tes baru menemukan bug sejak v0.5: `goal` direset tiap frame saat berjalan, sehingga agent bisa jalan ulang setelah tiba dan slot Pantry bisa dobel; sudah diperbaiki (berlaku untuk 2D juga).
- Peta 3D muat 1440x900 tanpa scroll (kontainer 1120x616). WebGL diuji lewat Chrome headless (SwiftShader); belum diuji di GPU nyata. Tak ada perubahan data/definisi "menunggu owner"/capability. Run agent tetap 79.

## 2026-11-19: Kantor Virtual v0.6.1 (3D)

- Layout 3D sendiri (`computeLayout3D`, 448 unit lebar): baris atas Kepala | QA | Pantry, baris bawah Engineering (3 kolom x 2 baris) | Creative; rasio denah ~1,25 sehingga diamond isometrik mengisi kontainer. Layout 2D TIDAK diubah (`computeLayout`, `W=640`); rute/pintu/koridor kini data per-layout (`L.out`, `L.corridors`, `L.doorways`, `L.zgaps`).
- Agent idle tetap di meja dengan pose santai (bersandar, monitor mati, cangkir kopi). Pantry maks 4 agent idle (3 sofa + 1 beanbag), dipilih deterministik (`pantryPick`: hash id+periode) dan bergilir tiap 3 menit; sticky dalam satu periode. Sim 2D tidak berubah (`Sim.policy` default = perilaku lama).
- Label 3D lebih kecil (9 px); penempatan anti-tumpuk memakai ukuran DOM nyata (bukan perkiraan, penyebab teks rusak di Safari) dan menghindari karakter, papan nama meja, plakat ruangan, dan gelembung; bila sempit disembunyikan, muncul saat hover. Papan nama meja memudar bila pemilik jalan/di Pantry.
- Kamera auto-fit: batas diproyeksikan dari seluruh denah (termasuk tinggi dinding + ruang label), margin 5-8%, dihitung ulang saat resize; kontainer dibatasi `max-height: 100vh-200px`.
- `?fps=1`: overlay fps/render ms di 3D. Di Chrome headless (WebGL software SwiftShader, demo `cycle=1`) terukur ~9-10 fps saat animasi; angka GPU Mac asli BELUM diukur (batas atas 30 fps karena cap). Minta owner membuka `?demo=1&cycle=1&view=3d&fps=1` dan melaporkan angkanya.
- Uji: typecheck, 15 test (termasuk layout 3D, rotasi Pantry), build, klik 3D (demo ATLAS -> `/MAH/issues/MAH-20`), klik 2D. Run agent tetap 79.

## v0.6.2 (3D saja)
- 3D 15 fps saat baterai (Battery API, Chromium) atau prefers-reduced-motion; 30 fps saat dicolok.
- Animasi 3D berhenti setelah 5 menit tanpa input, lanjut saat mouse/keyboard.
- Safari tidak punya Battery API: di sana hanya reduced-motion yang menurunkan fps.
- Laporan fps/suhu Safari dari owner: belum diterima (kolom kosong).
- v0.6.3: animasi dibatasi 15 fps selalu (2D dan 3D); logika 30 fps dan Battery API dihapus.
- v0.6.4: README 15 fps (2D+3D); 2D ikut berhenti setelah 5 menit tanpa input, lanjut saat mouse bergerak.
- SSH: Host github.com (github_kantor) diganti alias github-kantor; remote origin kantor-virtual = git@github-kantor:mahruswidip/kantor-virtual.git. Deploy key hanya untuk repo itu; github.com default tidak dipaksa key apa pun.
- SSH deploy key per repo: github_pos/github_badi-fe/github_badi-be, alias github-pos/github-badi-fe/github-badi-be di ~/.ssh/config (pos-saas, baditourweb/frontend, baditourweb/backend). Belum ditambahkan ke GitHub, remote belum diset, belum push. Cek .gitignore: tidak ada .env/*.jks/*.keystore/key.properties yang akan ikut.
- Repo plan: POS -> mahruswidip/pos-sass (alias github-pos, key github_pos); Badi Tour satu repo mahruswidip/baditour dari ~/baditourweb (alias github-badi, key github_badi_fe; github-badi-be dihapus). frontend/.git dipindah ke ~/baditourweb-frontend-git.bak, backend/.git (kosong, 0 commit) ke ~/baditourweb-backend-git.bak; git init -b main di root + .gitignore gabungan (assets/ diabaikan dulu). gitleaks 8.30.1: pos-sass riwayat 11 commit + 225 file bersih, baditour 139 file bersih. BELUM commit/push: deploy key pos-sass dan baditour belum ditambahkan di GitHub, footage 3.6 GB menunggu keputusan owner.
- 19 Nov: POS push ke mahruswidip/pos-saas (nama persis pos-saas; main + feat/ui-kasir-tahap1; commit .gitignore .paperclip-inbound/) via github-pos. Badi Tour push main ke mahruswidip/baditour (142 file) via github-badi. gitleaks bersih. BRIEF.md badi-tour & pos-kedai-aws diupdate. MAH-5 & MAH-6 ditutup (done) dengan komentar ringkasan.
- 19 Nov: prinsip baru (juga di OWNER.md): jangan menambah komentar pada issue yang sudah Done, karena Paperclip membukanya kembali dan membangunkan assignee; tulis ringkasan SEBELUM mengubah status ke Done. Pemicu: komentar di MAH-5/MAH-6 setelah Done membuka ulang keduanya. Commit .gitignore (.paperclip-inbound/) di-cherry-pick ke main pos-saas (0e65235) dan di-push.
- 19 Nov: persiapan migrasi ke PC kantor (Windows + WSL2). company-brain: `git init -b main` (belum ada commit/remote), .gitignore (.DS_Store, *.bak, .paperclip-inbound/), deploy key github_brain + alias github-brain dibuat, menunggu key dipasang di mahruswidip/company-brain. gitleaks: 1 temuan = false positive (kata biasa di rosana-travel/BRIEF.md:121), tidak ada secret. Inventaris lengkap di ops/migrasi-pc.md. Paperclip punya `company export/import` (+ export:preview/import:preview); belum dijalankan. Service Paperclip Mac tidak disentuh.
- 19 Nov (tugas gabungan): BRIEF rosana-travel dan rosana-collection diisi repo GitHub + branch (WEBSITE-ROSANA-GROUP-2026 main; WEBSITE-ROSANA-COLLECTION-2026 master); OPEN_QUESTIONS.md += kbihuinvitation/. Footage terkonfirmasi duplikat (1.077 file, nama+ukuran sama); agent tidak menghapus apa pun. brand.env: tidak ada secret (hanya setelan visual). `paperclipai company export:preview` dijalankan (hanya baca): 110 file, 11 agent, 6 project, 23 skill, 24 issue + 110 komentar; run/log, plugin, secret, path workspace tidak ikut. Paperclip pakai embedded-postgres (port 54329), bukan Postgres.app; routine tidak ada. migrasi-pc.md diperbarui dengan rencana PC. Tugas 2 (prinsip jangan-komentar-di-Done) dan tugas 3 (cherry-pick .gitignore ke main pos-saas, 0e65235) sudah selesai sebelumnya.
- 19 Nov: keputusan owner: (1) kbihuinvitation/ masuk repo WEBSITE-ROSANA-GROUP-2026 tanpa dist/ & node_modules/, jadi task Eng Rosana Travel setelah pindah ke PC (bukan di VPS); (2) pindah Paperclip lewat export/import, export sungguhan menunggu PC siap; setelah import: pasang ulang plugin kantor-virtual, buat ulang workspace Konten & Desain dan Badi Tour, login ulang Claude/Codex. Dicatat di OPEN_QUESTIONS.md dan ops/migrasi-pc.md.
- 21 Nov: PC siap (owner). `paperclipai company export` dijalankan: ~/paperclip-export-20261121 (113 file, 2,9 MB, include company,agents,projects,issues,tasks,skills; 2 warning workspace ai_video_maker_claude). gitleaks bersih, tanpa private key. Folder ini TIDAK di-commit ke GitHub (berisi komentar/dokumen task); pindah ke PC lewat Drive pribadi/USB. MAH-5 dan MAH-6 berstatus done.
