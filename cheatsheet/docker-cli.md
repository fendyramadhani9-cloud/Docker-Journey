<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker CLI Cheatsheet

Referensi cepat untuk perintah-perintah dasar Docker Command Line Interface (CLI) beserta penjelasan singkatnya.

---

## 📦 Mengelola Container

Perintah untuk membuat, menjalankan, dan mengelola lifecycle container.

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker run` | Membuat dan menjalankan container baru dari sebuah image | `docker run -d -p 80:80 nginx` |
| `docker ps` | Menampilkan container yang sedang aktif/berjalan | `docker ps` |
| `docker ps -a` | Menampilkan semua container (baik yang aktif maupun mati) | `docker ps -a` |
| `docker start` | Menjalankan container yang sedang berhenti | `docker start <container_id>` |
| `docker stop` | Menghentikan container yang sedang berjalan | `docker stop <container_id>` |
| `docker restart` | Memulai ulang container yang sedang berjalan | `docker restart <container_id>` |
| `docker pause` | Menangguhkan (pause) semua proses di dalam container | `docker pause <container_id>` |
| `docker unpause` | Mengaktifkan kembali container yang ditangguhkan | `docker unpause <container_id>` |
| `docker rm` | Menghapus container yang sudah berhenti | `docker rm <container_id>` |
| `docker rm -f` | Menghapus container secara paksa (meskipun sedang berjalan) | `docker rm -f <container_id>` |
| `docker exec` | Menjalankan perintah di dalam container yang sedang aktif | `docker exec -it <container_id> bash` |
| `docker logs` | Melihat output log dari suatu container | `docker logs -f <container_id>` |
| `docker inspect` | Menampilkan informasi detail tentang container (format JSON) | `docker inspect <container_id>` |
| `docker stats` | Menampilkan penggunaan CPU, memori, dan jaringan container secara real-time | `docker stats` |
| `docker port` | Menampilkan port mapping dari container | `docker port <container_id>` |
| `docker rename` | Mengubah nama container yang ada | `docker rename <old_name> <new_name>` |

---

## 💾 Mengelola Image

Perintah untuk mengunduh, membuat, dan memanipulasi Docker Image.

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker images` | Menampilkan semua image yang tersimpan secara lokal | `docker images` |
| `docker pull` | Mengunduh image dari registry (seperti Docker Hub) | `docker pull node:alpine` |
| `docker push` | Mengunggah image ke registry | `docker push username/image-name:tag` |
| `docker build` | Membuat image dari sebuah Dockerfile | `docker build -t myapp:1.0 .` |
| `docker rmi` | Menghapus image dari penyimpanan lokal | `docker rmi <image_id>` |
| `docker tag` | Membuat alias/tag baru untuk image yang sudah ada | `docker tag myapp:1.0 username/myapp:latest` |
| `docker history` | Menampilkan riwayat layer pembuatan suatu image | `docker history <image_name>` |
| `docker save` | Menyimpan image ke dalam file archive tar | `docker save -o myimage.tar myimage:latest` |
| `docker load` | Memuat image dari file archive tar | `docker load -i myimage.tar` |
| `docker image prune` | Menghapus semua image yang tidak digunakan (dangling) | `docker image prune` |

---

## ⚙️ Informasi & Pembersihan Sistem

Perintah untuk memantau status Docker daemon dan membersihkan resource.

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker version` | Menampilkan versi Docker engine dan CLI yang terpasang | `docker version` |
| `docker info` | Menampilkan informasi menyeluruh tentang sistem Docker | `docker info` |
| `docker df` | Menampilkan penggunaan ruang disk oleh Docker | `docker df` |
| `docker system prune` | Menghapus container mati, network tidak terpakai, dan image dangling | `docker system prune` |
| `docker system prune -a` | Menghapus semua resource yang tidak digunakan (termasuk unused images) | `docker system prune -a --volumes` |
| `docker login` | Masuk ke Docker registry (seperti Docker Hub) | `docker login` |
| `docker logout` | Keluar dari Docker registry | `docker logout` |
