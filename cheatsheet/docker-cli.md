<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker CLI Cheatsheet

Panduan referensi lengkap untuk Docker Command Line Interface (CLI). Dilengkapi dengan penjelasan mendalam tentang flag penting (seperti mode detached `-d`), kombinasi perintah riil di lingkungan development maupun production, manajemen lifecycle, pemeriksaan log, eksekusi container, dan perintah one-liner populer.

---

## Anatomi Perintah `docker run`

Sintaks dasar menjalankan container:
```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

> [!IMPORTANT]
> **Aturan Urutan Perintah:**
> - Semua `[OPTIONS]` (seperti `-d`, `-p`, `--name`, `-v`, `-e`) **wajib ditulis sebelum nama `IMAGE`**.
> - Perintah atau argumen setelah nama `IMAGE` dianggap sebagai `[COMMAND]` yang akan menimpa command default image tersebut.

---

## Bedah Mendalam: Flag `-d` (Detached Mode)

Flag `-d` (atau `--detach`) adalah salah satu flag paling krusial dalam Docker:

### Apa itu Detached Mode (`-d`)?
Secara default, saat Anda menjalankan `docker run`, container akan berjalan di **foreground mode**. Terminal Anda akan terikat ke proses container (menerima STDOUT dan STDERR). Jika Anda menutup terminal atau menekan `Ctrl + C`, container akan ikut berhenti.

Dengan menambahkan flag `-d`:
1. Container berjalan di **background** sebagai proses daemon.
2. Terminal langsung mencetak **Container ID (64-character hash)** dan langsung kembali ke prompt shell host.
3. Anda bebas menggunakan terminal untuk perintah lain tanpa mengganggu jalannya aplikasi di container.

### Kapan Menggunakan `-d` vs `-it`?
- **Gunakan `-d`** untuk layanan yang berjalan terus-menerus (long-running services): Web Server (Nginx/Apache), Database (PostgreSQL/MySQL/MongoDB), API Backend (Node.js/Go/Java), Cache (Redis), Message Broker (RabbitMQ/Kafka).
- **Gunakan `-it`** (tanpa `-d`) untuk sesi interaktif dan eksplorasi terminal: masuk ke shell Ubuntu/Alpine, menjalankan CLI installer, debugging sementara, atau REPL bahasa pemrograman.
- **Peringatan Penting**: Jangan gunakan `-d` pada image yang hanya menjalankan perintah sesaat (misal `ubuntu bash`), karena container akan langsung exit seketika jika tidak ada proses aktif di foreground.

### Cara Berinteraksi dengan Container yang Berjalan di Background (`-d`)
- **Melihat status:**
  ```bash
  docker ps
  ```
- **Membaca log output secara real-time:**
  ```bash
  docker logs -f <nama_atau_id_container>
  ```
- **Masuk ke dalam container yang sedang berjalan:**
  ```bash
  docker exec -it <nama_atau_id_container> sh
  ```
- **Mematikan container:**
  ```bash
  docker stop <nama_atau_id_container>
  ```
- **Menempelkan terminal kembali ke proses container:**
  ```bash
  docker attach <nama_atau_id_container>
  ```
  *(Gunakan pintasan `Ctrl + P`, lalu `Ctrl + Q` untuk keluar kembali ke host tanpa menghentikan container).*

---

## Kamus Flag dan Opsi Populer `docker run`

| Flag Pendek | Flag Panjang | Tipe / Format | Fungsi dan Penjelasan |
| :--- | :--- | :--- | :--- |
| `-d` | `--detach` | Boolean | Menjalankan container di background (daemon) dan mengembalikan kendali terminal ke host. |
| `-p` | `--publish` | `[host_ip:]<host_port>:<container_port>[/protocol]` | Port Mapping: Meneruskan traffic port komputer host ke port internal container. |
| `-P` | `--publish-all` | Boolean | Membuka seluruh port yang diekspos (EXPOSE) ke port acak di host. |
| *(tidak ada)* | `--name` | `<string>` | Memberikan nama unik kustom agar container mudah dipanggil dan dikelola. |
| `-v` | `--volume` | `[source:]<target>[:ro\|rw]` | Data Mounting (Ringkas): Menghubungkan Docker Volume atau direktori host (bind mount). |
| *(tidak ada)* | `--mount` | `type=<bind\|volume\|tmpfs>,src=...,dst=...[,ro]` | Data Mounting (Eksplisit): Standar modern yang lebih terstruktur dan direkomendasikan. |
| `-e` | `--env` | `<KEY>=<VALUE>` | Menetapkan environment variable ke dalam runtime container. |
| *(tidak ada)* | `--env-file` | `<path_to_file>` | Memuat banyak environment variables sekaligus dari sebuah file (seperti `.env`). |
| *(tidak ada)* | `--rm` | Boolean | Auto-remove: Menghapus container dari disk begitu prosesnya berhenti atau selesai. |
| `-i` | `--interactive` | Boolean | Menjaga STDIN tetap terbuka meskipun container tidak memiliki terminal terpasang. |
| `-t` | `--tty` | Boolean | Mengalokasikan pseudo-TTY (terminal semu). Sering digabung menjadi `-it`. |
| `-it` | *(gabungan)* | Boolean | Membuka sesi interaktif terminal shell (seperti bash atau sh). |
| *(tidak ada)* | `--network` | `<network_name>` | Menghubungkan container ke Docker Network tertentu untuk komunikasi antar-container. |
| *(tidak ada)* | `--network-alias`| `<alias_name>` | Menetapkan alias nama host internal di dalam network. |
| *(tidak ada)* | `--restart` | `<policy>` | Kebijakan auto-restart: `no`, `always`, `on-failure[:max]`, `unless-stopped`. |
| `-w` | `--workdir` | `<path>` | Mengatur direktori kerja aktif di dalam container saat perintah dieksekusi. |
| `-u` | `--user` | `<user\|uid>[:<group\|gid>]` | Menjalankan container dengan identitas pengguna tertentu (bukan default root). |
| *(tidak ada)* | `--cpus` | `<decimal>` | Membatasi alokasi CPU (contoh: `--cpus="1.5"` = maksimal 1.5 core). |
| `-m` | `--memory` | `<bytes\|k\|m\|g>` | Membatasi kuota penggunaan memori RAM (contoh: `-m 512m` atau `--memory="2g"`). |
| *(tidak ada)* | `--entrypoint`| `<command>` | Menimpa (override) instruksi default ENTRYPOINT dari image. |
| `-h` | `--hostname` | `<hostname>` | Mengatur nama host sistem operasi internal container. |
| *(tidak ada)* | `--log-opt` | `<key>=<value>` | Mengatur rotasi log (misal `max-size=10m` dan `max-file=3`) agar disk tidak penuh. |

---

## Cara Menulis Perintah Panjang (Multi-line)

Perintah Docker dengan banyak opsi sebaiknya dipecah menjadi beberapa baris agar mudah dibaca dan dikelola:

* **Linux / macOS / Git Bash**: Gunakan karakter backslash (`\`) di ujung setiap baris.
* **Windows PowerShell**: Gunakan karakter backtick (`` ` ``) di ujung setiap baris.
* **Windows Command Prompt (CMD)**: Gunakan karakter caret (`^`) di ujung setiap baris.

---

## Contoh Perintah Kombinasi Lengkap (Real-World Use Cases)

Kumpulan contoh nyata penggabungan flag (`-d`, `-p`, `--name`, `-v`, `-e`, `--restart`, `--network`, dan resource limit) untuk kebutuhan praktis:

### 1. Web App Development (Node.js / Express / Vite) dengan Live Reload
Skenario: Menjalankan aplikasi Node.js di background (`-d`), menyinkronkan source code host ke container (`-v`), mengisolasi `node_modules` container via anonymous volume, membuka port 3000, dan auto-restart.

**Bash (Linux / macOS / Git Bash):**
```bash
docker run -d \
  --name app-dev \
  -p 3000:3000 \
  -v $(pwd):/app \
  -v /app/node_modules \
  -w /app \
  -e NODE_ENV=development \
  -e PORT=3000 \
  --restart unless-stopped \
  node:20-alpine \
  npm run dev
```

**PowerShell (Windows):**
```powershell
docker run -d `
  --name app-dev `
  -p 3000:3000 `
  -v ${PWD}:/app `
  -v /app/node_modules `
  -w /app `
  -e NODE_ENV=development `
  -e PORT=3000 `
  --restart unless-stopped `
  node:20-alpine `
  npm run dev
```

---

### 2. Database PostgreSQL Production-Ready
Skenario: Menjalankan PostgreSQL di background (`-d`), memberi nama unik, port mapping 5432, menghubungkan ke network internal, menyimpan data ke Named Volume persisten, menyuplai kredensial via environment variable, dan auto-restart jika host menyala ulang.

```bash
docker run -d \
  --name postgres-prod \
  -p 5432:5432 \
  --network internal-net \
  -v pg_data:/var/lib/postgresql/data \
  -e POSTGRES_DB=company_db \
  -e POSTGRES_USER=db_admin \
  -e POSTGRES_PASSWORD=SuperSecurePassword123! \
  --restart always \
  postgres:16-alpine
```

---

### 3. Database MySQL Server dengan Named Volume
Skenario: Menjalankan MySQL 8.0 di background (`-d`) dengan volume terpisah agar data tidak musnah saat container di-upgrade atau dihentikan.

```bash
docker run -d \
  --name mysql-server \
  -p 3306:3306 \
  --network app-network \
  -v mysql_storage:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=rootpassword \
  -e MYSQL_DATABASE=production_db \
  -e MYSQL_USER=appuser \
  -e MYSQL_PASSWORD=appsecret \
  --restart unless-stopped \
  mysql:8.0
```

---

### 4. Web Gateway Nginx (Multi-Port & Read-Only Config)
Skenario: Menjalankan Nginx di background (`-d`), membuka port 80 (HTTP) dan 443 (HTTPS), memasang file konfigurasi dan asset HTML dengan flag `:ro` (Read-Only) demi keamanan.

```bash
docker run -d \
  --name web-gateway \
  -p 80:80 \
  -p 443:443 \
  -v ./dist:/usr/share/nginx/html:ro \
  -v ./nginx.conf:/etc/nginx/nginx.conf:ro \
  --restart unless-stopped \
  nginx:alpine
```

---

### 5. In-Memory Cache (Redis) dengan Password & Limit RAM
Skenario: Menjalankan Redis cache di background (`-d`), membatasi pemakaian RAM maksimal 256MB, memetakan port 6379, dan mengaktifkan proteksi password.

```bash
docker run -d \
  --name redis-cache \
  -p 6379:6379 \
  --network internal-net \
  --memory="256m" \
  --restart unless-stopped \
  redis:alpine \
  redis-server --requirepass "mypassword123" --appendonly yes
```

---

### 6. Production Microservice dengan Resource Limit & Log Rotation
Skenario: Menjalankan aplikasi backend di background (`-d`) dengan pembatasan CPU, RAM, membaca variabel dari file `.env`, dan membatasi ukuran file log agar harddisk server tidak penuh.

```bash
docker run -d \
  --name payment-api \
  -p 8080:8080 \
  --network internal-net \
  --env-file .env.production \
  --memory="512m" \
  --cpus="1.0" \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  --restart unless-stopped \
  myregistry.io/payment-api:v2.1.0
```

---

### 7. Container Sementara untuk Eksekusi Task / Migration (`--rm`)
Skenario: Menjalankan perintah satu kali jalan (one-off) seperti database migration atau build asset tanpa meninggalkan container bekas. Begitu proses selesai, container langsung otomatis terhapus bersih dari sistem.

```bash
docker run --rm \
  --network internal-net \
  -v $(pwd)/migrations:/migrations \
  migrate/migrate:v4.15.2 \
  -path=/migrations \
  -database "postgres://db_admin:SuperSecurePassword123!@postgres-prod:5432/company_db?sslmode=disable" \
  up
```

Contoh menjalankan package installer di folder host:
```bash
docker run --rm \
  -v $(pwd):/app \
  -w /app \
  node:20-alpine \
  npm install
```

---

### 8. Container Debugging Jaringan Interaktif Sekali Pakai
Skenario: Menjalankan curl atau network tool di dalam network internal untuk mengetes koneksi ke container lain, lalu otomatis terhapus saat selesai.

```bash
docker run --rm -it \
  --network internal-net \
  curlimages/curl:latest \
  curl -v http://payment-api:8080/health
```

---

## Perintah `docker exec` (Lengkap dengan Opsi)

Digunakan untuk masuk ke dalam container yang **sedang berjalan** atau mengeksekusi satu perintah langsung dari host:

| Perintah dan Opsi | Penjelasan |
| :--- | :--- |
| `docker exec -it <container> bash` | Masuk ke sesi terminal Bash interaktif di dalam container. |
| `docker exec -it <container> sh` | Masuk ke terminal `sh` (umumnya pada image Alpine). |
| `docker exec -u 0 -it <container> bash` | Masuk sebagai user root (UID 0) untuk keperluan administrasi darurat. |
| `docker exec -w /var/log <container> ls -la` | Menjalankan perintah pada direktori kerja tertentu (`-w`). |
| `docker exec -e DEBUG=true <container> node script.js` | Menjalankan script dengan menyuntikkan environment variable tambahan. |
| `docker exec <db_container> mysqldump -u root -p<pwd> db > backup.sql` | Melakukan backup dump database MySQL langsung ke file di host. |
| `docker exec -t <pg_container> pg_dump -U user db > backup.sql` | Melakukan backup database PostgreSQL ke file di host. |
| `docker exec -i <pg_container> psql -U user db < backup.sql` | Melakukan restore database PostgreSQL dari file di host. |

---

## Perintah `docker logs` (Lengkap dengan Opsi)

Digunakan untuk melihat log output (STDOUT dan STDERR) dari container yang berjalan di background (`-d`):

| Perintah dan Opsi | Penjelasan |
| :--- | :--- |
| `docker logs <container>` | Menampilkan seluruh riwayat log dari awal container dibuat. |
| `docker logs -f <container>` | Follow / Real-time: Memantau log secara langsung layaknya perintah `tail -f`. |
| `docker logs --tail 100 <container>` | Menampilkan hanya 100 baris log terakhir. |
| `docker logs -t <container>` | Menampilkan log lengkap beserta timestamp waktu output tercetak. |
| `docker logs --since 15m <container>` | Menampilkan log yang keluar dalam kurun waktu 15 menit terakhir. |
| `docker logs --since "2024-01-01T00:00:00" <container>` | Menampilkan log sejak tanggal atau jam tertentu. |
| `docker logs -f --tail 50 -t <container>` | Kombinasi Populer: Pantau 50 baris terakhir secara real-time disertai timestamp. |

---

## Mengelola Lifecycle Container

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker ps` | Menampilkan daftar container yang sedang aktif berjalan | `docker ps` |
| `docker ps -a` | Menampilkan semua container (aktif, error, maupun sudah berhenti) | `docker ps -a` |
| `docker ps -q` | Menampilkan hanya ID dari container yang sedang aktif | `docker ps -q` |
| `docker ps -a --filter "status=exited"` | Menampilkan hanya container yang statusnya sudah berhenti (exited) | `docker ps -a --filter "status=exited"` |
| `docker start <container>` | Menjalankan kembali container yang sedang berhenti | `docker start my-app` |
| `docker stop <container>` | Menghentikan container secara halus (sinyal SIGTERM lalu SIGKILL) | `docker stop my-app` |
| `docker restart <container>` | Memulai ulang container yang sedang berjalan | `docker restart my-app` |
| `docker pause <container>` | Membekukan (suspend) seluruh proses CPU container | `docker pause my-app` |
| `docker unpause <container>` | Melanjutkan proses container yang sedang dibekukan | `docker unpause my-app` |
| `docker rm <container>` | Menghapus container yang sudah dalam kondisi berhenti | `docker rm my-app` |
| `docker rm -f <container>` | Menghapus container secara paksa (meskipun sedang berjalan) | `docker rm -f my-app` |
| `docker inspect <container>` | Menampilkan konfigurasi lengkap, IP address, mount, dan metadata format JSON | `docker inspect my-app` |
| `docker stats` | Menampilkan dashboard monitoring penggunaan CPU, RAM, Network I/O real-time | `docker stats` |
| `docker port <container>` | Melihat mapping port publik ke port internal container | `docker port my-app` |
| `docker rename <old> <new>` | Mengganti nama container yang sudah ada | `docker rename old-app new-app` |
| `docker top <container>` | Menampilkan proses aktif (PID) yang sedang berjalan di dalam container | `docker top my-app` |

---

## Mengelola Docker Image

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker images` | Menampilkan seluruh image yang tersimpan di disk lokal | `docker images` |
| `docker pull <image>:<tag>` | Mengunduh image dari registry (Docker Hub) | `docker pull redis:7-alpine` |
| `docker push <user>/<img:tag>` | Mengunggah image lokal ke registry | `docker push fendy/app:1.0` |
| `docker build -t <tag> <path>` | Membuat image dari Dockerfile di path tertentu | `docker build -t myapp:1.0 .` |
| `docker build --no-cache -t <tag> .` | Build image tanpa menggunakan layer cache | `docker build --no-cache -t myapp:1.0 .` |
| `docker build -f <file> -t <tag> .` | Build image dengan menentukan nama Dockerfile spesifik | `docker build -f Dockerfile.prod -t myapp:prod .` |
| `docker build --build-arg KEY=VAL .`| Menyuplai argumen build time ke instruksi ARG di Dockerfile | `docker build --build-arg VERSION=2.0 -t myapp:2.0 .` |
| `docker rmi <image>` | Menghapus image dari lokal (hanya jika tidak dipakai container manapun) | `docker rmi node:20-alpine` |
| `docker rmi -f <image>` | Menghapus image secara paksa dari penyimpanan lokal | `docker rmi -f myapp:1.0` |
| `docker tag <source> <target>` | Membuat alias atau tag baru untuk image | `docker tag app:1.0 fendy/app:latest` |
| `docker history <image>` | Melihat daftar layer dan instruksi pembuatan image | `docker history nginx:alpine` |
| `docker save -o <file.tar> <image>` | Mengekspor image menjadi file tar archive | `docker save -o app.tar myapp:1.0` |
| `docker load -i <file.tar>` | Mengimpor image dari file tar archive | `docker load -i app.tar` |
| `docker image prune` | Menghapus image tak bertuan / dangling (`<none>:<none>`) | `docker image prune` |

---

## Pembersihan Sistem dan Disk Cleanup

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker system df` | Menampilkan rincian ruang disk yang dipakai oleh Images, Containers, Volumes, dan Cache | `docker system df` |
| `docker system prune` | Menghapus semua container berhenti, network tak terpakai, dan image dangling | `docker system prune` |
| `docker system prune -a --volumes` | Pembersihan Total: Menghapus container mati, semua unused images, dan unused volumes | `docker system prune -a --volumes` |
| `docker volume prune` | Menghapus seluruh volume yang tidak terikat ke container manapun | `docker volume prune` |
| `docker network prune` | Menghapus seluruh network kustom yang tidak memiliki container terhubung | `docker network prune` |

---

## Pro-Tips: Perintah One-Liner Populer

Kumpulan perintah kombinasi serba guna yang sering dipakai DevOps dan Software Engineer:

```bash
# 1. Hentikan SEMUA container yang sedang berjalan sekaligus
docker stop $(docker ps -q)

# 2. Hapus PAKSA semua container (baik yang aktif maupun berhenti)
docker rm -f $(docker ps -aq)

# 3. Hapus SEMUA container yang berstatus 'exited'
docker rm $(docker ps -a -q -f status=exited)

# 4. Hapus SEMUA Docker Image lokal sekaligus
docker rmi -f $(docker images -q)

# 5. Hapus SEMUA image berstatus dangling (<none>)
docker rmi $(docker images -f "dangling=true" -q)

# 6. Hapus SEMUA volume lokal yang tidak terikat ke container manapun
docker volume rm $(docker volume ls -q -f dangling=true)

# 7. Cari tahu IP Address internal dari sebuah container
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <nama_container>

# 8. Format tabel docker ps agar ringkas, rapi, dan mudah dibaca
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"

# 9. Pantau konsumsi resource (RAM & CPU) per container tanpa streaming terus-menerus
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}"
```
