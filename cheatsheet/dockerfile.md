<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Dockerfile Cheatsheet

Referensi cepat instruksi-instruksi yang digunakan dalam pembuatan file `Dockerfile`.

---

## 🛠️ Instruksi Dockerfile

| Instruksi | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `FROM` | Menentukan base image yang digunakan untuk memulai proses build | `FROM node:18-alpine` |
| `WORKDIR` | Mengatur direktori kerja di dalam container untuk instruksi berikutnya | `WORKDIR /app` |
| `COPY` | Menyalin file/direktori dari host machine ke dalam container | `COPY package*.json ./` |
| `ADD` | Serupa dengan COPY, tapi bisa menyalin dari URL remote atau mengekstrak file tar | `ADD archive.tar.gz /data/` |
| `RUN` | Menjalankan perintah shell saat proses build image (membuat layer baru) | `RUN npm install` |
| `ENV` | Menetapkan environment variable yang akan tersedia di dalam container | `ENV PORT=3000` |
| `ARG` | Menentukan variable yang bisa dilewatkan user saat proses build saja | `ARG VERSION=latest` |
| `EXPOSE` | Menginformasikan port yang digunakan container saat berjalan (dokumentasi) | `EXPOSE 8080` |
| `CMD` | Menyediakan default command yang dijalankan saat container dimulai (bisa dioverwrite) | `CMD ["node", "index.js"]` |
| `ENTRYPOINT` | Menentukan executable utama yang selalu dijalankan saat container dimulai | `ENTRYPOINT ["docker-entrypoint.sh"]` |
| `VOLUME` | Membuat mount point untuk volume agar data tetap persisten di host | `VOLUME ["/data"]` |
| `USER` | Mengubah user yang menjalankan instruksi RUN, CMD, atau ENTRYPOINT | `USER node` |
| `LABEL` | Menambahkan metadata berupa key-value pair ke dalam image | `LABEL maintainer="fendy"` |
| `HEALTHCHECK` | Menentukan cara memeriksa apakah container masih berjalan sehat | `HEALTHCHECK CMD curl -f http://localhost/ \|\| exit 1` |

---

## 💡 Best Practices Singkat

- **Gunakan `.dockerignore`**: Kecualikan file yang tidak perlu (seperti `node_modules`, `.git`, file logs) agar ukuran image lebih kecil.
- **Urutan Layer yang Efektif**: Letakkan perintah yang jarang berubah (seperti menginstal dependencies) di atas, dan perintah yang sering berubah (seperti menyalin source code) di bawah. Ini memaksimalkan penggunaan Docker build cache.
- **Gunakan Multi-stage Build**: Memisahkan tahap build dependencies dan runtime untuk menghasilkan image final yang sangat ringan.
- **Pilih Base Image Minimal**: Gunakan image berbasis `alpine` jika memungkinkan untuk meminimalkan kerentanan keamanan dan ukuran penyimpanan.
