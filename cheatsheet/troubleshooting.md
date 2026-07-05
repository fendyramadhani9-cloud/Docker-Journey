<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker Troubleshooting Cheatsheet

Referensi cepat perintah diagnostik dan pemecahan masalah (troubleshooting) saat terjadi kendala pada container atau service Docker.

---

## 🔍 Investigasi & Debugging

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker logs -f` | Memantau log container secara real-time untuk melihat error runtime | `docker logs -f <container_name>` |
| `docker logs --tail N` | Menampilkan `N` baris log terakhir dari container | `docker logs --tail 50 <container_name>` |
| `docker inspect` | Meneliti konfigurasi, IP Address, volume mount, dan status container | `docker inspect <container_name>` |
| `docker exec -it` | Masuk ke terminal container untuk mengecek file internal atau test koneksi | `docker exec -it <container_name> sh` |
| `docker top` | Melihat proses yang sedang berjalan di dalam container | `docker top <container_name>` |
| `docker stats` | Memantau konsumsi resource (CPU, Memory, Network) container secara real-time | `docker stats <container_name>` |
| `docker diff` | Melihat perubahan file pada container layer dibandingkan dengan image aslinya | `docker diff <container_name>` |
| `docker events` | Mendapatkan log event system dari engine Docker secara real-time | `docker events --since 60m` |

---

## 📂 Transfer File & Ekstraksi

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker cp` | Menyalin file dari host ke container atau sebaliknya (sangat berguna untuk backup file konfigurasi) | **Dari Host ke Container**:<br>`docker cp nginx.conf container:/etc/nginx/nginx.conf`<br><br>**Dari Container ke Host**:<br>`docker cp container:/var/log/nginx/error.log ./error.log` |

---

## 🧹 Mengatasi Kehabisan Penyimpanan (Disk Full)

Terkadang Docker meninggalkan cache build, dangling images, dan container mati yang memakan ruang disk yang sangat besar.

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker system df` | Melihat total ruang disk yang digunakan oleh semua resource Docker | `docker system df` |
| `docker container prune` | Menghapus semua container yang statusnya sudah berhenti (exit) | `docker container prune` |
| `docker image prune` | Menghapus semua dangling images (image tanpa tag name) | `docker image prune` |
| `docker builder prune` | Menghapus cache build lama untuk membebaskan penyimpanan | `docker builder prune -a` |
| `docker system prune` | **Sapu bersih**: Menghapus container mati, network tak terpakai, dan dangling images | `docker system prune` |
| `docker system prune -a --volumes` | **Hard Reset**: Menghapus container mati, network, volume tak terpakai, dan seluruh image yang tidak terpakai container aktif | `docker system prune -a --volumes` |

---

## 💡 Masalah Umum & Solusi

### 1. Port Conflict (Port Sudah Terpakai)
*   **Gejala**: Error `Bind for 0.0.0.0:80 failed: port is already allocated`.
*   **Solusi**: Cari proses host yang menggunakan port tersebut atau ganti port binding host di command `docker run` atau `docker-compose.yml` (misal dari `-p 80:80` menjadi `-p 8080:80`).

### 2. Container Langsung Mati (Exited) Setelah Dijalankan
*   **Gejala**: `docker ps` kosong, dan `docker ps -a` menampilkan status `Exited (0)` atau `Exited (1)`.
*   **Solusi**: Cek log menggunakan `docker logs <container_id>`. Biasanya terjadi karena error konfigurasi aplikasi, runtime crash, atau command utama container selesai berjalan (container butuh proses di foreground agar tetap hidup).
