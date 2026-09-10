<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker Volumes Cheatsheet

Referensi lengkap untuk mengelola penyimpanan data persisten pada Docker, meliputi Named Volumes, Bind Mounts, Tmpfs, parameter hak akses, backup/restore data volume, dan contoh kombinasi perintah riil dengan flag `-d`, `-p`, `--name`, `-v`, dan `-e`.

---

## Perintah Manajemen Docker Volume

Perintah CLI untuk membuat dan mengelola daur hidup (lifecycle) Docker Volume:

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker volume ls` | Menampilkan seluruh volume lokal yang terdaftar di sistem Docker | `docker volume ls` |
| `docker volume create <nama>` | Membuat volume baru yang dikelola langsung oleh Docker daemon | `docker volume create db_data` |
| `docker volume inspect <nama>`| Menampilkan metadata dan path absolut lokasi penyimpanan volume di host | `docker volume inspect db_data` |
| `docker volume rm <nama>` | Menghapus volume tertentu (hanya bisa jika tidak sedang digunakan container) | `docker volume rm db_data` |
| `docker volume prune` | Menghapus semua volume lokal yang tidak terhubung dengan container manapun | `docker volume prune -f` |

---

## Perbandingan Jenis Mounting

| Tipe | Lokasi Penyimpanan di Host | Dikelola Oleh | Kapan Digunakan? |
| :--- | :--- | :--- | :--- |
| **Named Volume** | Direktori internal Docker (`/var/lib/docker/volumes/...`) | Docker Daemon | **Database & Data Produksi**. Sangat aman, terisolasi dari modifikasi host, dan berkinerja tinggi. |
| **Bind Mount** | Sembarang folder di host (misal `d:/project` atau `/home/user/app`) | User / Host OS | **Development (Source Code)**. Memungkinkan edit file di host langsung berpengaruh di container. |
| **Tmpfs Mount** | Hanya di memori RAM host (tidak ditulis ke disk fisik) | Host OS RAM | Menyimpan data sensitif, cache sementara, atau credential tanpa meninggalkan jejak di harddisk. |

---

## Sintaks Mounting: `-v` vs `--mount`

Docker menyediakan dua cara penulisan mount:

### 1. Sintaks Ringkas (`-v` atau `--volume`)
Format: `-v <source>:<destination>[:mode]`
- Jika `source` adalah nama biasa tanpa tanda slash/titik: dianggap sebagai **Named Volume**.
- Jika `source` adalah path absolut atau diawali `./` atau `/`: dianggap sebagai **Bind Mount**.
- Opsi `mode`: `:ro` (read-only) atau `:rw` (read-write, default).

### 2. Sintaks Eksplisit (`--mount`)
Format: `--mount type=<bind|volume|tmpfs>,source=<src>,target=<dest>[,readonly]`
- Lebih panjang namun lebih eksplisit, aman dari salah tafsir path, dan menjadi standar rekomendasi Docker modern.

---

## Contoh Kombinasi Perintah Nyata (Mounting + Flags Lengkap)

### 1. Database MySQL dengan Named Volume Persisten & Detached Mode (`-d`)
Skenario: Menjalankan database di background (`-d`), memberi nama container, port mapping, menghubungkan ke volume persisten, dan set password root. Data tetap utuh meskipun container dihapus atau diperbarui.

```bash
docker run -d \
  --name mysql-server \
  -p 3306:3306 \
  -v db_storage:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=supersecret \
  --restart unless-stopped \
  mysql:8.0
```

> [!TIP]
> Menggunakan format modern `--mount`:
> ```bash
> docker run -d \
>   --name mysql-server \
>   -p 3306:3306 \
>   --mount type=volume,source=db_storage,target=/var/lib/mysql \
>   -e MYSQL_ROOT_PASSWORD=supersecret \
>   --restart unless-stopped \
>   mysql:8.0
> ```

---

### 2. Web App Development dengan Bind Mount (Live Code Reloading)
Skenario: Menghubungkan folder source code di laptop ke dalam container agar setiap kali file disimpan, aplikasi langsung me-reload di background (`-d`).

**Bash (Linux / macOS / Git Bash):**
```bash
docker run -d \
  --name frontend-dev \
  -p 5173:5173 \
  -v $(pwd):/app \
  -v /app/node_modules \
  -w /app \
  node:20-alpine \
  npm run dev -- --host
```

**PowerShell (Windows):**
```powershell
docker run -d `
  --name frontend-dev `
  -p 5173:5173 `
  -v ${PWD}:/app `
  -v /app/node_modules `
  -w /app `
  node:20-alpine `
  npm run dev -- --host
```

> [!IMPORTANT]
> **Trik Anonymous Volume `-v /app/node_modules`:**
> Trik ini penting agar folder `node_modules` di dalam container Linux tidak tertimpa oleh folder `node_modules` lokal dari sistem operasi host saat melakukan bind mount direktori utama.

---

### 3. Web Server Nginx dengan Mount Read-Only (`:ro`)
Skenario: Memasang file HTML dan file konfigurasi `nginx.conf` ke container di background (`-d`) dengan hak akses hanya-baca (*read-only*) agar container tidak dapat mengubah file di komputer host.

```bash
docker run -d \
  --name web-portal \
  -p 80:80 \
  -v ./public:/usr/share/nginx/html:ro \
  -v ./nginx.conf:/etc/nginx/conf.d/default.conf:ro \
  nginx:alpine
```

---

### 4. Backup Data dari Named Volume ke File Archive (.tar.gz) di Host
Skenario: Menjalankan container Alpine sementara (`--rm`) yang menghubungkan named volume `db_storage` dan folder host lokal, lalu mengompres seluruh isi volume menjadi file tarball.

```bash
docker run --rm \
  -v db_storage:/volume_data \
  -v $(pwd):/backup \
  alpine \
  tar czf /backup/db_storage_backup.tar.gz -C /volume_data .
```

---

### 5. Restore Data dari File Backup (.tar.gz) ke Named Volume Baru
Skenario: Mengembalikan data arsip backup ke dalam volume bernama `db_storage_restored`.

```bash
# 1. Buat volume baru
docker volume create db_storage_restored

# 2. Ekstrak data dari file backup ke dalam volume
docker run --rm \
  -v db_storage_restored:/volume_data \
  -v $(pwd):/backup \
  alpine \
  tar xzf /backup/db_storage_backup.tar.gz -C /volume_data
```

---

### 6. Berbagi Data Bersama Antara Dua Container (Shared Volume)
Skenario: Container pertama (backend logger) menulis log ke volume bersama di background (`-d`), dan container kedua (web dashboard / file viewer) membaca isi volume tersebut.

```bash
# Buat volume bersama
docker volume create shared_logs

# Container 1: Writer service
docker run -d \
  --name log-writer \
  -v shared_logs:/var/log/app \
  alpine sh -c "while true; do date >> /var/log/app/access.log; sleep 2; done"

# Container 2: Reader service
docker run -d \
  --name log-viewer \
  -p 8080:80 \
  -v shared_logs:/usr/share/nginx/html:ro \
  nginx:alpine
```
