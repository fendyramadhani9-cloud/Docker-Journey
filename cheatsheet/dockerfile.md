<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Dockerfile Cheatsheet

Referensi instruksi-instruksi dasar pembuatan file `Dockerfile`, urutan layer yang optimal, multi-stage build, serta contoh perintah kombinasi `docker build`.

---

## Instruksi Dockerfile

| Instruksi | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `FROM` | Menentukan base image yang digunakan untuk memulai proses build | `FROM node:20-alpine` |
| `WORKDIR` | Mengatur direktori kerja di dalam container untuk instruksi berikutnya | `WORKDIR /app` |
| `COPY` | Menyalin file atau folder dari komputer host ke dalam container | `COPY package*.json ./` |
| `ADD` | Mirip dengan COPY, tetapi mendukung download URL remote dan auto-extract tarball | `ADD archive.tar.gz /data/` |
| `RUN` | Menjalankan perintah shell saat image sedang dibuat (menghasilkan layer baru) | `RUN npm install --production` |
| `ENV` | Menetapkan environment variable permanen di dalam container | `ENV NODE_ENV=production PORT=3000` |
| `ARG` | Menentukan variabel yang hanya dilewatkan saat proses build berlangsung | `ARG APP_VERSION=1.0.0` |
| `EXPOSE` | Mendokumentasikan port yang digunakan oleh aplikasi di container | `EXPOSE 3000` |
| `CMD` | Perintah default yang dijalankan saat container menyala (dapat ditimpa oleh user) | `CMD ["node", "server.js"]` |
| `ENTRYPOINT` | Perintah utama yang akan selalu dieksekusi saat container menyala | `ENTRYPOINT ["docker-entrypoint.sh"]` |
| `VOLUME` | Menyiapkan mount point volume untuk direktori data persisten | `VOLUME ["/app/data"]` |
| `USER` | Mengalihkan user pengeksekusi ke non-root demi keamanan | `USER node` |
| `LABEL` | Menambahkan metadata berupa key-value pair ke image | `LABEL maintainer="fendy"` |
| `HEALTHCHECK` | Menentukan perintah berkala untuk memastikan container dalam kondisi sehat | `HEALTHCHECK CMD curl -f http://localhost:3000/health \|\| exit 1` |

---

## Contoh Kombinasi Perintah `docker build`

### 1. Build Standar dengan Tag Nama dan Versi
```bash
docker build -t myapp:1.0.0 -t myapp:latest .
```

### 2. Build Menggunakan Dockerfile Khusus (Misal Production)
```bash
docker build -f Dockerfile.prod -t myapp:prod .
```

### 3. Build Bersih Tanpa Memakai Cache
```bash
docker build --no-cache -t myapp:clean .
```

### 4. Build dengan Menyuntikkan Variabel Build-Time (`--build-arg`)
```bash
docker build --build-arg APP_VERSION=2.4.1 --build-arg ENVIRONMENT=staging -t myapp:staging .
```

### 5. Build Stage Tertentu pada Multi-stage Dockerfile (`--target`)
```bash
docker build --target development -t myapp:dev .
```

---

## Best Practices Pembuatan Dockerfile

- **Gunakan `.dockerignore`**: Selalu buat file `.dockerignore` untuk mengecualikan direktori besar seperti `node_modules`, `.git`, file logs, dan file `.env` rahasia.
- **Urutan Layer yang Efektif**: Letakkan perintah yang jarang berubah (seperti install dependencies / `package.json`) sebelum menyalin source code yang sering diedit (`COPY . .`). Ini mengoptimalkan pemanfaatan Docker build cache.
- **Gunakan Multi-Stage Build**: Pisahkan environment build (compiler, SDK berat) dari environment runtime final (image minimal) untuk menghasilkan image yang berukuran jauh lebih kecil dan aman.
- **Jalankan Aplikasi sebagai Non-Root**: Selalu gunakan instruksi `USER` (misal `USER node` atau `USER nonroot`) sebelum instruksi `CMD` untuk mencegah potensi privilege escalation.
- **Pilih Base Image Ringan**: Gunakan varian `alpine` atau `distroless` untuk mengurangi ukuran image dan menekan jumlah celah kerentanan (CVE).
