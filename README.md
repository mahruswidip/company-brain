# Company Brain — Kantor AI

Sumber konteks untuk semua agent di Paperclip. Kepala Kantor membaca semuanya; engineer membaca folder project-nya saja.

## Struktur
```
company-brain/
├── README.md            ← file ini
├── OWNER.md             ← prioritas & aturan dari owner (WAJIB dibaca semua agent)
├── OPEN_QUESTIONS.md    ← semua hal yang belum pasti, menunggu jawaban owner
├── rosana-travel/
│   ├── BRIEF.md         ← bisnis, stack, alur, status
│   ├── RULES.md         ← aturan bisnis & teknis yang tidak boleh dilanggar
│   └── DECISIONS.md     ← keputusan yang sudah diambil + alasannya
├── rosana-collection/   (sama)
├── badi-tour/           (sama)
└── pos-kedai-aws/       (sama) — repo asli ~/Documents/pos-saas
```

Tanda ✂️ = teks sumber terpotong saat dicopas dari terminal. Cek ulang ke Claude di Mac, jangan dilengkapi dengan tebakan.

## Aturan membaca brief (untuk semua agent)
1. Tanda **⚠️ PERLU DIKONFIRMASI** = belum pasti. Itu pertanyaan terbuka, **bukan izin** untuk menentukan aturan sendiri. Tanyakan ke Kepala Kantor → owner.
2. Tanda **⚡ KONFLIK** = dua sumber berbeda. Jangan pilih salah satu; verifikasi di repo/data atau tanya owner.
3. Brief disusun dari ingatan percakapan (GPT), **bukan inspeksi repo**. Kode di repository adalah sumber kebenaran. Kalau brief beda dengan kode, laporkan, jangan diam-diam ikut salah satu.
4. Setiap project adalah **sistem terpisah**. Aturan satu project tidak berlaku untuk yang lain; footage, harga, dan brand antar klien tidak boleh tertukar.
5. Setelah menyelesaikan tugas besar, usulkan update ke `DECISIONS.md` atau `BRIEF.md` (bagian Status).

## Sumber
- Rangkuman GPT owner, 18 Nov 2026 (dua sesi: fokus Travel dan fokus Collection).
- Claude di Mac owner, 18 Nov 2026 — cek langsung repo Badi Tour & POS.
- Tahap berikutnya: jalankan Graphify di tiap repo → simpan `GRAPH_REPORT.md` di `<project>/graph/`.
