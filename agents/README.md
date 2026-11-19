# Agents — job description & prompt sistem untuk Paperclip

Setiap file = satu agent. Cara pakai di Paperclip:
1. Buat agent baru → isi **Nama**, **Jabatan**, **Lapor ke**, **Runtime/Model**, **Heartbeat**, dan **Budget** dari tabel di bawah.
2. Copy seluruh isi bagian **"Prompt sistem"** di file agent ke kolom instruksi/job description agent di Paperclip.
3. Pastikan agent bisa membaca folder `company-brain/` (taruh di path yang sama untuk semua agent, misal `~/company-brain`).

## Struktur organisasi
```
Owner (Mahrus)
└── Kepala Kantor
    ├── Eng Rosana Travel
    ├── Eng Rosana Collection
    ├── Eng Mobile            (Flutter Rosana Travel)
    ├── Eng POS               (POS Kedai AWS)
    ├── Eng Badi Tour
    ├── QA Reviewer
    └── Social Media Lead
        ├── Trend Scout
        ├── Video Editor
        └── Designer
```

## Ringkasan setting
| Agent | Runtime | Model | Heartbeat | Mulai aktif |
|---|---|---|---|---|
| Kepala Kantor | Claude Code (Claude Pro) | Sonnet; Opus hanya untuk ringkasan mingguan | Saat ada tugas + routine mingguan | Minggu 1 |
| Eng Rosana Travel | Codex (GPT Plus) | default Codex | Saat ada tugas | Minggu 1 |
| Eng Rosana Collection | Codex (GPT Plus) | default Codex | Saat ada tugas | Minggu 2 |
| Eng Mobile | Codex (GPT Plus) | default Codex | Saat ada tugas | Minggu 3 |
| Eng Badi Tour | Codex (GPT Plus) | default Codex | Saat ada tugas | Minggu 3 |
| Eng POS | Codex (GPT Plus) | default Codex | Saat ada tugas | Minggu 1 (untuk backup & repo) |
| QA Reviewer | Claude Code (Claude Pro) | Sonnet | Saat ada PR | Minggu 1 |
| Social Media Lead | Claude Code (Claude Pro) | Sonnet | Routine harian pagi | Minggu 2 |
| Trend Scout | Claude Code (Claude Pro) | Haiku | Routine 2× seminggu | Minggu 2 |
| Video Editor | Claude Code (Claude Pro) | Sonnet | Saat ada brief | Minggu 2 |
| Designer | Claude Code (Claude Pro) | Sonnet | Saat ada tugas | Minggu 3 |

**Kenapa begini:** Codex Plus dan Claude Pro sama-sama kuota kecil. Ngoding (paling boros) di Codex; otak, review, dan kreatif di Claude (skill Instagram adalah skill Claude Code). QA memakai model berbeda dari engineer supaya kode tidak dinilai oleh model yang menulisnya.

**Budget:** pasang hard stop per agent di Paperclip. Mulai kecil, naikkan berdasarkan data pemakaian minggu 1–2. Kalau satu langganan sering mentok, upgrade yang paling sering mentok dulu (kemungkinan besar Codex).

## Akses VPS & kredensial
- Akses VPS diberikan lewat **SSH key yang sudah ada di Mac** (agent menjalankan `ssh` dari Mac). Jangan pernah menempelkan password / private key ke chat, task Paperclip, brief, atau repo.
- Mulai dengan akses **baca/cek** saja. Perintah yang mengubah sesuatu di VPS produksi butuh approval owner per tugas.

## Tugas awal
Lihat `INITIAL_TASKS.md` — Kepala Kantor membuat task-task ini di Paperclip pada hari pertama.

## Di mana Paperclip dijalankan
**Di Mac owner** (bukan di VPS produksi). Alasan & cara menjaga Mac tetap menyala: lihat jawaban di chat 18 Nov 2026, ringkasnya:
- Repo Badi Tour, POS, skill Instagram, video maker, dan tools desain ada di Mac.
- Login langganan Claude Pro & Codex Plus berjalan di mesin yang sama dengan Paperclip.
- VPS produksi (`rosana-group`) tidak boleh dipakai untuk menjalankan agent: agent punya akses shell, dan render video membebani server.
- Mac harus menyala: colok charger, matikan auto-sleep saat layar mati (atau `caffeinate -s`). Saat Mac tidur, agent berhenti dan lanjut saat Mac menyala lagi.
