<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker Troubleshooting Cheatsheet

Referensi cepat perintah diagnostik, inspeksi, dan pemecahan masalah (troubleshooting) saat terjadi kendala pada container, jaringan, maupun penyimpanan Docker.

---

## Investigasi dan Debugging

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker logs -f` | Memantau log container secara real-time untuk melihat error runtime | `docker logs -f <container_name>` |
| `docker logs --tail N` | Menampilkan `N` baris log terakhir dari container | `docker logs --tail 50 <container_name>` |
| `docker inspect` | Meneliti konfigurasi lengkap, IP Address, volume mount, dan status container | `docker inspect <container_name>` |
| `docker exec -it` | Masuk ke terminal container untuk mengecek file internal atau koneksi | `docker exec -it <container_name> sh` |
| `docker exec -u 0 -it`| Masuk ke container sebagai user root (UID 0) untuk investigasi mendalam | `docker exec -u 0 -it <container_name> sh` |
| `docker top` | Melihat daftar proses aktif (PID) di dalam container | `docker top <container_name>` |
| `docker stats` | Memantau konsumsi resource (CPU, Memory, Network) secara real-time | `docker stats <container_name>` |
| `docker diff` | Melihat perubahan file pada layer container dibandingkan dengan image dasar | `docker diff <container_name>` |
| `docker events` | Mendapatkan log event system dari engine Docker secara real-time | `docker events --since 60m` |

---

## Transfer File dan Ekstraksi

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker cp` | Menyalin file dari host ke container atau sebaliknya (sangat berguna untuk memeriksa konfigurasi rusak) | **Dari Host ke Container**:<br>`docker cp nginx.conf container:/etc/nginx/nginx.conf`<br><br>**Dari Container ke Host**:<br>`docker cp container:/var/log/nginx/error.log ./error.log` |

---

## Pembersihan Penyimpanan dan Disk Cleanup

Saat Docker kehabisan ruang disk akibat cache build, dangling image, atau container lama yang menumpuk:

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker system df` | Melihat total ruang disk yang digunakan oleh semua resource Docker | `docker system df` |
| `docker container prune` | Menghapus semua container yang statusnya sudah berhenti (exited) | `docker container prune -f` |
| `docker image prune` | Menghapus semua dangling images (image tanpa tag / `<none>`) | `docker image prune -f` |
| `docker builder prune` | Menghapus cache build lama untuk membebaskan penyimpanan | `docker builder prune -a -f` |
| `docker system prune` | Sapu bersih: Menghapus container mati, network tak terpakai, dan dangling images | `docker system prune -f` |
| `docker system prune -a --volumes`| Reset Total: Menghapus seluruh container mati, network, volume tak terpakai, dan semua image lokal yang tidak aktif | `docker system prune -a --volumes -f` |

---

## Contoh Kombinasi Perintah Troubleshooting Praktis

### 1. Memeriksa Mengapa Container Langsung Mati (Exited)
Jika container gagal jalan dan langsung exit, periksa exit code dan 50 baris log terakhir:

```bash
# Cek exit code dan pesan error status
docker inspect <container_name> --format 'Status: {{.State.Status}} | ExitCode: {{.State.ExitCode}} | Error: {{.State.Error}}'

# Cek log error penyebab crash
docker logs --tail 50 <container_name>
```

---

### 2. Membuka Shell Darurat pada Image yang Mengalami Crash Loop (Override Entrypoint)
Jika container terus-menerus restart atau crash saat dinyalakan dengan `docker run`, timpa entrypoint dengan shell interaktif untuk mengecek konfigurasi di dalamnya:

```bash
docker run --rm -it --entrypoint sh <image_name>
```

---

### 3. Diagnostik Koneksi Jaringan Antar Container
Gunakan toolkit diagnostik lengkap (seperti `nicolaka/netshoot`) di dalam network container yang bermasalah:

```bash
docker run --rm -it --network <network_name> nicolaka/netshoot ping <target_container>
docker run --rm -it --network <network_name> nicolaka/netshoot nslookup <target_container>
docker run --rm -it --network <network_name> nicolaka/netshoot curl -Iv http://<target_container>:<port>
```

---

### 4. Menemukan Container Pemakan Resource (RAM / CPU) Terbesar
Perintah one-liner cepat tanpa streaming berkepanjangan:

```bash
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.MemPerc}}\t{{.NetIO}}"
```

---

## Masalah Umum dan Solusi

### 1. Port Conflict (Port Sudah Terpakai)
- **Gejala**: Muncul pesan error `Bind for 0.0.0.0:80 failed: port is already allocated`.
- **Solusi**: Ganti port host yang dipetakan pada perintah `docker run -p <host_port>:<container_port>` (misalnya `-p 8080:80`) atau matikan proses lokal yang sedang menggunakan port tersebut.

### 2. Container Berstatus "Exited (0)" Padahal Dijalankan dengan `-d`
- **Gejala**: Container langsung mati sesaat setelah dijalankan dengan `docker run -d`.
- **Solusi**: Container Docker akan otomatis berhenti jika proses utamanya (PID 1) selesai. Image seperti `ubuntu` atau `alpine` membutuhkan proses foreground yang aktif terus-menerus (contoh: web server daemon, atau perintah seperti `tail -f /dev/null`).
