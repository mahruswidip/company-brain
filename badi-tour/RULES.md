# Badi Tour — RULES

> Aturan yang TIDAK BOLEH dilanggar agent. Berlaku untuk website, konten, dan desain Badi Tour.

## A. Data resmi
1. PPIU, nomor WA, alamat, tagline: pakai **hanya** data di bagian "Data resmi" BRIEF.
2. Paket & harga **berubah-ubah**. Sumber live = Filament admin/DB; flyer terbaru = acuan konten. Jangan pakai harga dari ingatan, file lama, atau brief lama.
3. Ikuti pola flyer final untuk identitas kantor (IG Surabaya + alamat Jombang + dua nomor WA).

## B. Dilarang mengarang
1. Tanggal berangkat, jadwal flight, jarak hotel, bintang hotel, fasilitas tambahan.
2. Airline di website sengaja kosong — jangan diisi.
3. Teks sejarah/agama (Jabal Tsur, dll) wajib dicek ke ustadz sebelum dipakai.

## C. Footage
1. Footage 2 Nov 2026 = **jamaah November**. Label wajib: "Dokumentasi jamaah November 2026".
2. Yang terlihat di footage itu (van Khandamah, kereta gantung Thaif, uang saku, nasi ayam bakar, zamzam) **jangan ditulis sebagai fasilitas paket Desember** sebelum dikonfirmasi klien.
3. Hotel di footage belum tentu hotel paket.
4. **Jangan pakai:** footage silaturahmi "Gus Menteri" (bisa terbaca seperti endorsement), klip editan vendor lain: 3403, 3534, 4125, 4167, 4256.
5. Izin wajah jamaah: **sudah disetujui semua** (sekarang & ke depan), termasuk klip menangis.
6. Tanggal keberangkatan hanya dari kabar Bu Berdi / data admin terbaru.

## D. Brand
1. Warna wajib: Emerald `#164B30` + Gold (kode ✂️ cek BRIEF). Gaya lain bebas.
2. Logo asli `logobadi.jpg`, jangan digambar ulang / di-recolor.
3. Dilarang: partikel emas, ornamen 3D palsu, glow, Kakbah hasil AI, hijau/kuning oversaturated, "AI slop".
4. Bahasa hangat, sopan, jelas; tanpa klaim berlebihan / jualan agresif.

## E. Sosial media
1. Tidak ada posting tanpa approval owner.
2. Tidak memakai otomasi browser / tool posting pihak ketiga yang melanggar ToS IG/TikTok.
3. Output konten di `clients/badi/`; jangan tukar footage, harga, brand dengan klien lain (Rosana).

## F. Website & server
1. Server Rumahweb: tanpa node/rsync, exec mati — **jangan `composer install` di server**. Deploy sesuai cara di BRIEF.
2. Frontend Next 16: baca `node_modules/next/dist/docs/` sebelum mengubah kode.
3. Commit dulu perubahan frontend & inisialisasi git backend **sebelum** agent mulai mengubah kode (supaya bisa rollback).
4. Deploy & migrasi produksi → wajib approval owner.
5. Kredensial (DB, SSH, admin) hanya ada di `.env` / `~/.ssh` — jangan disalin ke repo, brief, atau output.
6. Data calon jamaah tidak disimpan di server (form → WA). Kalau mau menyimpan lead, butuh keputusan owner + kebijakan privasi dulu.
