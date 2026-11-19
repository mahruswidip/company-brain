# VPS hardening log: rosana-group (Vultr)

> Baca file ini dulu kalau sesi di-clear. Update setiap selesai satu langkah.
> Tanpa password, key, atau isi rahasia. Semua perubahan di VPS dijalankan owner sendiri.
> Terakhir diperbarui: 2026-11-18

## Status ringkas

| Tahap | Status |
|---|---|
| Fase 0 | Selesai (ringkasan inventaris di bagian Fase 0 di bawah) |
| Fase 1.5 (7 langkah) | Selesai, semua lolos; web sama dengan baseline, errs 0 semua |
| Fase 1 Langkah 1: user `agent-ro` + SSH | Selesai, uji login lolos |
| Fase 2: ACL (deny folder sensitif) | Selesai, uji lolos (18 Nov 2026) |

## Fase 0: inventaris VPS (selesai)

- VPS `rosana-group`, 139.180.222.53, Ubuntu 24.04. Host yang sama dengan `pos-vps`. Menjalankan Travel, Collection, POS, staging, wakro, trading-sentinel, quant, dan lain-lain.
- Lokasi app:
  - Travel: `/var/www/rosana/backend_rosanagroup`
  - Collection: `/var/www/collection/backend_collection`
  - POS: `/var/www/pos-backend/backend`
- PHP-FPM 8.3 dan 8.5 (user `www-data`). PostgreSQL 16 (peer/scram, tanpa trust).
- Backup: restic mingguan ke Google Drive (Minggu 03:00; mencakup `/etc`, `/root`, `/var/backups`, `/var/www`). `pg_dump` POS harian, disimpan 14 hari.
- Root SSH masih memakai password. ACL didukung. `ssh.socket` tidak aktif.

## Fase 1.5 (selesai)

- Langkah 1-3: Travel.
- Langkah 4-6: Collection, termasuk salinan dan dump POS.
- Langkah 7: staging dan wakro.
- Hasil: database Travel sekarang mode 660 (sebelumnya 664).

## Fase 1 Langkah 1: agent-ro (selesai)

Rencana awal yang disetujui:
- Key memakai `~/.ssh/agent_ro_rosana` di Mac (tidak membuat key baru).
- `agent-ro` TIDAK masuk grup tambahan (bukan `adm`, bukan `systemd-journal`, bukan `www-data`; grup `users` sudah dihapus). Bukan sudo.
- `authorized_keys` milik root di `/etc/ssh/agent_ro/`, dengan opsi `no-port-forwarding,no-agent-forwarding,no-X11-forwarding,no-pty`.
- Drop-in `/etc/ssh/sshd_config.d/99-agent-ro.conf` (blok `Match`).
- Jangan pakai `set -e` di sesi root interaktif.

Hasil uji login dari Mac (semua sesuai harapan):
- Login OK, uid 1002, tanpa grup tambahan.
- `.env` Travel/Collection/POS/wakro dan sqlite Travel/Collection: Permission denied.
- `sudo` ditolak; `touch` di `/var/www` dan `/etc` ditolak; nginx `access.log` ditolak.
- Forwarding `-W 127.0.0.1:5432`: administratively prohibited, exit 255.
- Reload ssh OK, service active.

## Fase 2: ACL (selesai)

Hasil (dilaporkan owner):
- 16 folder deny untuk `agent-ro`. ACL `dashboard_ro` utuh. Web normal, errs 0.
- Uji dari Mac: semua ditolak, kode app tetap terbaca, `bukti-transfer` Permission denied.
- Backup ACL sebelum perubahan: `/root/acl-before-fase2.txt` (pulihkan: `cd / && setfacl --restore=/root/acl-before-fase2.txt`).
- Temuan: `bukti-transfer` Travel world-readable untuk user lokal lain. Masuk follow-up #1 (perbaikan lewat `deploy.sh`).

Rencana awal (untuk arsip):

Rencana (blok ada di chat 18 Nov 2026; tulis ulang di sini saat dijalankan):
1. Blok baca-saja: `getfacl` di `/`, `/var/www`, `/var/www/trading-sentinel-v2`; mode dan isi `storage/` dan `.git` di rosana, collection, pos-backend; uji `runuser -u agent-ro -- ls` dan `test -r` di `storage/app` tanpa menampilkan isi.
2. Deny total (`---`) untuk `agent-ro` di: quant, sniperhood, trading-sentinel, trading-sentinel-v2, wakro, collection-dev, rosana-staging, html.
3. Di rosana, collection, pos-backend: deny `storage/` dan `.git`; default deny di `database/` dan `storage/`.
4. Jangan merusak ACL lama: cek mask dan entry lama dulu; pakai `setfacl -n`; backup ACL ke `/root/acl-before-fase2.txt` sebelum mengubah.
5. Verifikasi dari Mac (fungsi zsh `s`, key `agent_ro_rosana`) dan audit LEAK dari root (`runuser -u agent-ro -- find ... -readable`).

## Keputusan yang sudah disetujui

- Akses agent ke VPS read-only lewat user `agent-ro`; perubahan di VPS produksi tetap butuh approval owner per tugas.
- Agent berjalan di Mac owner, bukan di VPS produksi.
- Kredensial tidak masuk repo, brief, task, atau laporan.
- Rencana `agent-ro` di atas (key lama, tanpa grup tambahan, authorized_keys milik root, drop-in Match) menggantikan draf awal yang menyimpang.
- Fase 2 hanya ditulis dulu; dijalankan owner setelah ditinjau.

## Follow-up (jangan dikerjakan tanpa perintah)

1. `deploy.sh` backend Travel baris 70 mengembalikan `database.sqlite` ke 664. Ubah ke 660 dan tambah `chmod 640 .env` (contoh: `deploy.sh` POS). Tambahan: `deploy.sh` Travel menjalankan `chmod -R 755 storage`; periksa juga. Temuan Fase 2: `bukti-transfer` Travel world-readable untuk user lokal lain; perbaiki lewat `deploy.sh` (mode folder/file, bukan `755`/`644` terbuka). Deploy yang menghapus dan membuat ulang folder bisa menghapus ACL Fase 2, cek setelah deploy berikutnya.
2. Password restic hanya ada di VPS. Owner simpan salinan di luar VPS.
3. SQLite Travel dan Collection di-backup mentah oleh restic. Tambah snapshot konsisten (`sqlite3 .backup`) di tahap "Preparing database snapshots".
4. Backup hanya mingguan. Pertimbangkan harian untuk Travel dan Collection.
5. Root SSH pakai password. Pindah ke key dan matikan password login.
6. `wakro.rosanatravel.com` mengembalikan 526 (sudah ada sebelum perubahan kita).
7. Keputusan rotasi key Xendit.

## Riwayat update

- 2026-11-18: file dibuat. Fase 1.5 selesai; Langkah 1 agent-ro selesai dan lolos uji; Fase 2 ditulis, belum jalan.
- 2026-11-18: Fase 2 (ACL) selesai dan lolos uji; temuan `bukti-transfer` Travel dicatat di follow-up #1.
