<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker Compose Cheatsheet

Referensi cepat untuk mengelola multi-container menggunakan Docker Compose.

> [!NOTE]
> Semua perintah di bawah ini harus dijalankan di direktori tempat file `docker-compose.yml` berada, kecuali jika ditentukan path filenya secara eksplisit dengan flag `-f`.

---

## 🚀 Perintah Dasar Lifecycle

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker compose up` | Membuat, membuat ulang, dan menjalankan seluruh container yang ada di config | `docker compose up` |
| `docker compose up -d` | Menjalankan seluruh container di background (detached mode) | `docker compose up -d` |
| `docker compose down` | Menghentikan dan menghapus container, network, volume, dan image yang dibuat `up` | `docker compose down` |
| `docker compose down -v` | Menghentikan container dan menghapus volume yang terkait sekaligus | `docker compose down -v` |
| `docker compose start` | Menjalankan container yang sudah pernah dibuat tetapi sedang berhenti | `docker compose start` |
| `docker compose stop` | Menghentikan container yang sedang berjalan tanpa menghapusnya | `docker compose stop` |
| `docker compose restart` | Memulai ulang container yang terdefinisi di docker-compose | `docker compose restart` |
| `docker compose pause` | Menangguhkan proses di dalam container | `docker compose pause` |
| `docker compose unpause` | Mengaktifkan kembali container yang ditangguhkan | `docker compose unpause` |

---

## 🔍 Monitoring & Pemeriksaan

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker compose ps` | Menampilkan status container yang dikelola oleh Docker Compose | `docker compose ps` |
| `docker compose logs` | Menampilkan log output dari seluruh container | `docker compose logs` |
| `docker compose logs -f` | Menampilkan log output secara real-time (follow) | `docker compose logs -f <service_name>` |
| `docker compose top` | Menampilkan proses aktif di dalam container | `docker compose top` |
| `docker compose images` | Menampilkan daftar image yang digunakan oleh container saat ini | `docker compose images` |
| `docker compose config` | Validasi dan melihat hasil akhir konfigurasi docker-compose.yml | `docker compose config` |

---

## 🛠️ Build & Eksekusi

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker compose build` | Melakukan build atau rebuild image yang ditentukan di docker-compose | `docker compose build` |
| `docker compose build --no-cache` | Melakukan build ulang image dari awal tanpa cache | `docker compose build --no-cache` |
| `docker compose up --build` | Melakukan build image lalu langsung menjalankannya | `docker compose up -d --build` |
| `docker compose exec` | Menjalankan perintah di dalam container service yang aktif | `docker compose exec web sh` |
| `docker compose run` | Menjalankan perintah satu kali (one-off) di dalam container baru | `docker compose run app npm run migrate` |
| `docker compose pull` | Mengunduh versi terbaru image dari service yang ditentukan | `docker compose pull` |
