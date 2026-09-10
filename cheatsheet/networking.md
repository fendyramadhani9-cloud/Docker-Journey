<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker Networking Cheatsheet

Referensi lengkap untuk mengelola jaringan (network) pada Docker Container, menghubungkan antar-container menggunakan DNS internal, port forwarding, dan contoh kombinasi perintah riil dengan flag `-d`, `--network`, dan `--rm`.

---

## Perintah Docker Network

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker network ls` | Menampilkan daftar seluruh network yang ada di sistem Docker | `docker network ls` |
| `docker network create <nama>` | Membuat network baru (default menggunakan driver bridge) | `docker network create app-network` |
| `docker network create --subnet <cidr> <nama>` | Membuat network baru dengan rentang subnet IP tertentu | `docker network create --subnet 172.20.0.0/16 custom-net` |
| `docker network inspect <nama>` | Menampilkan konfigurasi detail dan daftar container yang terhubung | `docker network inspect app-network` |
| `docker network connect <net> <container>` | Menghubungkan container yang sedang aktif ke network tertentu | `docker network connect app-network my-container` |
| `docker network disconnect <net> <container>` | Memutuskan hubungan container dari suatu network | `docker network disconnect app-network my-container` |
| `docker network rm <nama>` | Menghapus satu atau lebih network yang sedang tidak digunakan | `docker network rm app-network` |
| `docker network prune` | Menghapus seluruh network yang tidak digunakan oleh container manapun | `docker network prune -f` |

---

## Driver Network Populer

- **Bridge (Default)**: Digunakan ketika container membutuhkan komunikasi satu sama lain dalam satu host Docker yang sama.
- **Host**: Menghilangkan isolasi jaringan antara container dan host machine (container menggunakan IP dan port host secara langsung).
- **None**: Menonaktifkan seluruh jaringan untuk container (container tidak memiliki akses luar sama sekali).
- **Overlay**: Menghubungkan beberapa daemon Docker secara bersamaan agar container di host berbeda dapat berkomunikasi (digunakan pada Docker Swarm atau Cluster).

---

## Port Mapping dan DNS Internal

- **Port Mapping (`-p <host_port>:<container_port>`)**: Menghubungkan port pada komputer host dengan port di dalam container.
  - *Contoh*: `docker run -d -p 8080:80 nginx` (mengakses container nginx via `http://localhost:8080` di browser).
- **Container Name as DNS**: Container yang berada di dalam network custom buatan sendiri dapat saling menghubungi menggunakan nama containernya sebagai hostname.
  - *Contoh*: Container `api` menghubungi database melalui host `db-server:5432` (bukan menggunakan IP address dinamis).

---

## Contoh Kombinasi Perintah Nyata (Multi-Container Communication)

Skenario: Membangun arsitektur terisolasi di mana database, backend, dan tool penguji berkomunikasi di dalam network khusus yang sama.

### 1. Buat Network Khusus
```bash
docker network create internal-net
```

### 2. Jalankan Database di Background (`-d`) Terhubung ke Network
```bash
docker run -d \
  --name db-postgres \
  --network internal-net \
  -e POSTGRES_DB=mydb \
  -e POSTGRES_PASSWORD=secret \
  postgres:16-alpine
```

### 3. Jalankan Aplikasi Backend di Background (`-d`) Terhubung ke Network
Aplikasi dapat memanggil database cukup dengan alamat `db-postgres:5432`:
```bash
docker run -d \
  --name api-server \
  --network internal-net \
  -p 3000:3000 \
  -e DATABASE_URL="postgres://postgres:secret@db-postgres:5432/mydb" \
  my-api-image:1.0
```

### 4. Uji Konektivitas Antar Container dengan Container Sementara (`--rm`)
Menguji apakah DNS name `api-server` dan port `3000` bisa diakses dari dalam network yang sama:
```bash
docker run --rm \
  --network internal-net \
  curlimages/curl:latest \
  curl -s http://api-server:3000/health
```

### 5. Menghubungkan Container yang Sudah Ada ke Network Kedua
Skenario: Container `api-server` perlu berkomunikasi dengan Redis yang berada di network berbeda (`cache-net`).
```bash
docker network connect cache-net api-server
```

### 6. Menampilkan Semua Nama Container dan IP Address di Suatu Network
Perintah one-liner ringkas untuk memeriksa pembagian IP di dalam network:
```bash
docker network inspect internal-net \
  --format '{{range $id, $cont := .Containers}}{{$cont.Name}} -> {{$cont.IPv4Address}}{{"\n"}}{{end}}'
```
