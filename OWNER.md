# OWNER — aturan dari owner untuk semua agent

> Owner: Mahrus Widi Pratama — developer tunggal untuk Rosana Travel, Rosana Collection, Badi Tour, dan POS Kedai AWS.
> Catatan: "owner" di file ini = Mahrus (pemilik kantor AI). Pemilik bisnis klien: Rosana Travel = Ibu Hj. Rosana Hayati, Rosana Collection = Bu Ika, Badi Tour = Bu Berdiyani & Dr Berdi, Kedai AWS = milik Mahrus.

## Wajib minta approval owner
1. Apa pun yang menyentuh **pembayaran, uang, stok, laporan keuangan** — setelah lolos QA Reviewer.
2. **Deploy ke produksi.**
3. **Migrasi database produksi** (dan backup harus terverifikasi dulu).
4. **Menghapus data** apa pun.
5. **Posting konten** ke sosial media.
6. Mengubah aturan bisnis yang bertanda ⚠️ atau ⚡ di brief.
7. Menambah package / mengganti stack.
8. Login / menjalankan perintah yang mengubah sesuatu di VPS produksi (membaca & mengecek boleh setelah akses diberikan).

## Prioritas kalau tugas bentrok
1. Rosana Travel
2. Rosana Collection
3. Badi Tour
4. POS Kedai AWS

Pengecualian: masalah keselamatan produksi (backup hilang, celah keamanan, data rusak) di project mana pun didahulukan.

## Cara melapor
- **Lewat:** dashboard Paperclip.
- **Frekuensi:** ringkasan mingguan dari Kepala Kantor + pesan langsung untuk hal yang butuh approval.
- **Gaya:** singkat, tapi semua poin penting tetap ada (apa yang selesai, apa yang tertahan & kenapa, apa yang butuh keputusan owner, risiko).

## Jam kerja owner
- Owner bisa dihubungi kapan saja, **kecuali saat tidur** (zona GMT+7 / WIB).
- Jam tidur tidak tetap: mulai antara **21:00–23:00**, bangun antara **04:00–06:00**.
- Anggap **21:00–06:00 WIB** sebagai jam tenang: jangan kirim pesan yang tidak mendesak.
- Kalau butuh approval dan owner belum merespons: **tunggu**. Kerjakan tugas lain yang tidak butuh approval. Jangan mengambil keputusan sendiri.

## Prinsip umum
- Brief bertanda ⚠️ = pertanyaan, bukan izin.
- Kode di repo = sumber kebenaran; laporkan kalau brief beda dengan kode.
- Hemat kuota: kerjakan tugas kecil yang jelas, jangan scan seluruh repo tanpa alasan, pakai `GRAPH_REPORT.md` dulu sebelum membaca kode.
- Data pribadi jamaah & customer tidak boleh masuk log, commit, atau output agent.
- Kredensial (password, token, SSH key, `.env`) tidak boleh disalin ke repo, brief, task, atau laporan.
- Jangan menambah komentar pada issue yang sudah Done — Paperclip akan membukanya kembali dan membangunkan assignee. Ringkasan ditulis SEBELUM mengubah status ke Done.
