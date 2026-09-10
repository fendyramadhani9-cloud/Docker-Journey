<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker Compose Cheatsheet

Referensi cepat dan praktis untuk mengelola arsitektur multi-container menggunakan Docker Compose. Dilengkapi dengan opsi penting (seperti mode background `-d`), build ulang image, pembersihan volume, scaling service, dan eksekusi perintah di dalam container.

> [!NOTE]
> Semua perintah di bawah ini dijalankan di direktori tempat file `docker-compose.yml` berada, kecuali jika ditentukan path filenya secara eksplisit dengan flag `-f`.

---

## Perintah Dasar Lifecycle

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker compose up` | Menjalankan seluruh container di foreground (menampilkan log di terminal) | `docker compose up` |
| `docker compose up -d` | Menjalankan seluruh container di **background** (detached mode) | `docker compose up -d` |
| `docker compose down` | Menghentikan dan menghapus container serta network yang dibuat oleh `up` | `docker compose down` |
| `docker compose down -v` | Menghentikan container serta **menghapus volume** persisten terkait | `docker compose down -v` |
| `docker compose down -v --remove-orphans` | Pembersihan total termasuk menghapus service lama yang sudah dihapus dari config | `docker compose down -v --remove-orphans` |
| `docker compose start` | Menjalankan service yang sudah pernah dibuat tetapi sedang berhenti | `docker compose start` |
| `docker compose stop` | Menghentikan service yang sedang berjalan tanpa menghapus containernya | `docker compose stop` |
| `docker compose restart` | Memulai ulang seluruh service atau service tertentu | `docker compose restart web` |
| `docker compose pause` | Menangguhkan proses CPU di dalam container service | `docker compose pause` |
| `docker compose unpause` | Melanjutkan kembali service yang ditangguhkan | `docker compose unpause` |

---

## Monitoring dan Pemeriksaan

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker compose ps` | Menampilkan status seluruh container yang dikelola Compose | `docker compose ps` |
| `docker compose ps -a` | Menampilkan semua container termasuk yang sudah berhenti (exited) | `docker compose ps -a` |
| `docker compose logs` | Menampilkan output log dari seluruh container | `docker compose logs` |
| `docker compose logs -f` | Memantau log secara langsung / real-time (follow) | `docker compose logs -f` |
| `docker compose logs -f --tail 100 <service>` | Memantau 100 baris log terakhir dari suatu service spesifik | `docker compose logs -f --tail 100 api` |
| `docker compose top` | Menampilkan proses sistem yang sedang aktif di dalam container | `docker compose top` |
| `docker compose images` | Menampilkan daftar image yang digunakan oleh service saat ini | `docker compose images` |
| `docker compose config` | Validasi sintaks dan melihat hasil rendering akhir docker-compose.yml | `docker compose config` |

---

## Build dan Eksekusi

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker compose build` | Melakukan build atau compile image untuk service yang memiliki opsi `build` | `docker compose build` |
| `docker compose build --no-cache` | Melakukan build ulang image dari awal tanpa memakai layer cache | `docker compose build --no-cache` |
| `docker compose up -d --build` | Melakukan build ulang image lalu langsung menjalankannya di background | `docker compose up -d --build` |
| `docker compose exec -it <service> sh` | Masuk ke terminal interaktif service yang sedang berjalan | `docker compose exec -it web sh` |
| `docker compose run --rm <service> <cmd>` | Menjalankan perintah sekali jalan di container baru lalu langsung dihapus | `docker compose run --rm app npm test` |
| `docker compose pull` | Mengunduh image versi terbaru dari registry untuk seluruh service | `docker compose pull` |

---

## Contoh Kombinasi Perintah Nyata

### 1. Build Ulang dan Jalankan Service Tertentu di Background
Skenario: Anda baru saja mengubah kode di service `api` dan ingin mengompilasi ulang image service tersebut saja tanpa mematikan database atau redis.

```bash
docker compose up -d --build api
```

---

### 2. Scaling Instance Service
Skenario: Menjalankan beberapa replika container worker secara paralel untuk memproses antrian tugas.

```bash
docker compose up -d --scale worker=3
```

---

### 3. Menggunakan Multiple File Compose (Override Production / Development)
Skenario: Menggabungkan konfigurasi dasar `docker-compose.yml` dengan konfigurasi override `docker-compose.prod.yml`.

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

---

### 4. Menjalankan Database Migration Satu Kali Jalan (`--rm`)
Skenario: Menjalankan migration tool sekali jalan di network compose, lalu container otomatis terhapus saat selesai.

```bash
docker compose run --rm backend-api npm run db:migrate
```

---

### 5. Masuk ke Shell Service Tertentu Sebagai User Root
Skenario: Masuk ke dalam container service untuk investigasi paket atau permission.

```bash
docker compose exec -u 0 -it web bash
```
