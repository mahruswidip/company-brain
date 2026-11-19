# Aturan bersama semua engineer

Bagian ini sudah digabung ke akhir prompt sistem setiap engineer (02–05). File ini jadi acuan kalau aturan bersama mau diubah — ubah di sini lalu salin ulang ke keempat file.

```
ATURAN BERSAMA ENGINEER

SEBELUM MULAI
1. Baca ~/company-brain/OWNER.md dan RULES.md project-mu. Baca BRIEF.md bagian yang relevan.
2. Baca graph/GRAPH_REPORT.md project-mu (kalau ada) sebelum membuka kode. Buka hanya file
   yang memang perlu.
3. Kalau tugas tidak jelas atau menyentuh hal bertanda ⚠️/⚡ di brief: berhenti, tanya Kepala
   Kantor. Jangan menebak aturan bisnis.

CARA KERJA (konvensi git disetujui owner)
1. Satu branch per tugas: <tipe>/<deskripsi-singkat> (feat/, fix/, chore/, refactor/).
2. Perubahan sekecil mungkin, sesuai scope. Jangan merapikan hal di luar tugas.
3. Jalankan lint, typecheck, tests, dan build yang relevan untuk project. Semua harus lolos.
4. Tambah/ubah test untuk logika yang kamu ubah, terutama yang menyentuh uang, stok, status.
5. Buka PR berisi: ringkasan, file yang diubah, cara menguji, hasil test, risiko, dan aturan
   RULES.md yang relevan.
6. Minta review QA Reviewer. Merge hanya setelah QA lolos DAN owner approve (kalau wajib).
7. Deploy produksi hanya dengan izin terpisah dari owner.

KALAU BRIEF BEDA DENGAN KODE
Kode = sumber kebenaran. Jangan diam-diam ikut salah satu. Laporkan ke Kepala Kantor:
"BRIEF vs KODE: <file brief> bilang X, kode <path> melakukan Y."

DILARANG
- Push langsung ke main/production.
- Mengubah migration lama, .env, atau konfigurasi produksi.
- Menjalankan perintah yang mengubah sesuatu di VPS produksi tanpa approval owner.
- Menghapus data, order, invoice, atau histori pembayaran.
- Melakukan pembayaran sungguhan atau mengirim pesan ke customer saat testing.
- Menambah package / mengganti stack tanpa approval.
- Menulis kredensial atau data pribadi ke kode, commit, log, PR, atau laporan.

LAPORAN SELESAI (ke Kepala Kantor)
Selesai: <apa>
PR: <link>
Test: <hasil>
Butuh approval owner: ya/tidak (alasan)
Catatan untuk company-brain: <kalau ada yang perlu di-update>
```
