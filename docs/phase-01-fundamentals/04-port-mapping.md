# Port Mapping

## 1. Port Mapping

Pernah kepikiran nggak, gimana caranya aplikasi yang berjalan di dalam container bisa diakses dari browser di komputer kita?

Di sinilah fungsi **Port Mapping**.

Port Mapping digunakan untuk menghubungkan port yang ada di komputer (Host) dengan port yang ada di dalam Docker Container.

Dengan begitu, aplikasi yang berjalan di dalam container dapat diakses dari luar menggunakan browser atau aplikasi lain.

### Analogi

Saat belajar, saya menganggap Port Mapping seperti **nomor rumah**.

Bayangkan sebuah rumah berada di dalam komplek.

Agar kurir bisa mengirim paket ke rumah yang benar, rumah tersebut harus memiliki nomor yang jelas.

Begitu juga dengan Docker. Port Mapping menjadi "alamat" yang digunakan agar komputer mengetahui ke mana permintaan harus dikirim.

Tanpa Port Mapping, aplikasi tetap berjalan di dalam container, tetapi tidak bisa diakses dari luar.

## 2. Host Port

Host Port adalah port yang berada di komputer kita (Host).

Port inilah yang nantinya diakses oleh browser atau aplikasi lain untuk menghubungi Docker Container.

Saat kita membuka alamat seperti:

```text
http://localhost:8080
```

Browser sebenarnya sedang mengirimkan permintaan ke **Host Port 8080**.

### Analogi

Saat belajar, saya menganggap **Host Port** seperti **loket pelayanan**.

Bayangkan sebuah kantor memiliki banyak loket.

Pengunjung hanya bisa berbicara dengan petugas melalui loket yang tersedia, bukan langsung masuk ke ruang kerja di belakang.

Begitu juga dengan Docker. Aplikasi dari luar hanya bisa masuk melalui Host Port terlebih dahulu sebelum diteruskan ke dalam container.

### Kesimpulan

- Host Port berada di komputer kita (Host).
- Browser mengakses aplikasi melalui Host Port.
- Docker akan meneruskan permintaan tersebut ke Container Port.

## 3.Container Port

Container Port adalah port yang digunakan oleh aplikasi yang berjalan di dalam Docker Container.

Port ini hanya bisa diakses dari dalam container atau melalui Docker Network. Agar dapat diakses dari luar, Container Port harus dihubungkan dengan Host Port menggunakan **Port Mapping**.

Sebagai contoh, web server **Nginx** secara default berjalan pada **port 80** di dalam container.

### Analogi

Saat belajar, saya menganggap **Container Port** seperti **ruang kerja petugas** di dalam sebuah kantor.

Pengunjung tidak bisa langsung masuk ke ruang kerja tersebut.

Mereka harus melewati loket pelayanan terlebih dahulu, kemudian petugas akan menerima permintaan yang dikirim dari loket.

Begitu juga dengan Docker. Aplikasi sebenarnya berjalan di dalam Container Port, sedangkan Host Port hanya menjadi jalur masuk dari luar.

### Kesimpulan

- Container Port berada di dalam Docker Container.
- Aplikasi berjalan menggunakan Container Port.
- Agar bisa diakses dari luar, Container Port harus dihubungkan dengan Host Port menggunakan Port Mapping.

## 4.Cara Kerja Port Mapping

Port Mapping bekerja dengan cara menghubungkan **Host Port** dengan **Container Port**.

Misalnya kita menjalankan container menggunakan command berikut:

```bash
docker run -d --name nginx-web -p 8080:80 nginx
```

Artinya:

- **8080** adalah Host Port (port yang diakses dari komputer kita).
- **80** adalah Container Port (port tempat aplikasi Nginx berjalan di dalam container).

Saat browser mengakses:

```text
http://localhost:8080
```

Permintaan tersebut tidak langsung menuju ke Nginx.

Docker akan menerima permintaan pada **Host Port 8080**, kemudian meneruskannya ke **Container Port 80** tempat Nginx berjalan.

### Ilustrasi

```text
Browser
      │
localhost:8080
      │
      ▼
Host Port (8080)
      │
Docker Port Mapping
      │
      ▼
Container Port (80)
      │
      ▼
Nginx
```

### Logic

Docker bertindak sebagai penghubung antara Host dan Container.

Tanpa Port Mapping, aplikasi tetap berjalan di dalam container, tetapi tidak dapat diakses dari luar.

Dengan Port Mapping, permintaan dari Host dapat diteruskan ke aplikasi yang berjalan di dalam Container.

### Kesimpulan

- Format Port Mapping adalah `HostPort:ContainerPort`.
- Browser selalu mengakses Host Port.
- Docker meneruskan permintaan ke Container Port yang sesuai.

## 5. Praktik Port Mapping

Pada praktik ini saya akan menjalankan **Nginx** di dalam Docker Container, kemudian menghubungkannya ke komputer menggunakan **Port Mapping**.

Dengan begitu, halaman web Nginx dapat diakses langsung melalui browser.

```bash
docker run -d --name nginx-web -p 8080:80 nginx
```

### Penjelasan Parameter

| Parameter | Fungsi |
|-----------|--------|
| `docker run` | Membuat sekaligus menjalankan container baru. |
| `-d` | Menjalankan container di background (Detached Mode). |
| `--name nginx-web` | Memberikan nama `nginx-web` pada container. |
| `-p 8080:80` | Menghubungkan Host Port **8080** ke Container Port **80**. |
| `nginx` | Docker Image yang digunakan untuk membuat container. |

### Logic

Saat command dijalankan, Docker akan mengecek apakah Image `nginx` sudah tersedia.

- Jika Image sudah ada, Docker langsung membuat dan menjalankan container.
- Jika belum ada, Docker akan mengunduh Image terlebih dahulu dari Docker Hub.

Selanjutnya Docker membuat Port Mapping antara **Host Port 8080** dan **Container Port 80**.
Pada hasil praktik terlihat bahwa Docker membuat Port Mapping dari **Host Port 8080** menuju **Container Port 80**, sehingga aplikasi Nginx dapat diakses melalui browser.
<p align="center">
  <img src="../assets/screenshots/module-04/docker-ps-port-mapping.png" alt="Docker Port Mapping" width="900">
</p>
Saat browser mengakses `http://localhost:8080`, Docker akan meneruskan permintaan tersebut ke aplikasi Nginx yang berjalan pada **port 80** di dalam container.

### Hasil Praktik

<p align="center">
  <img src="../assets/screenshots/module-04/nginx-port-mapping.png" alt="Nginx Port Mapping" width="900">
</p>

### Kesimpulan

- Port Mapping menghubungkan Host dengan Docker Container.
- Browser hanya mengakses Host Port.
- Docker meneruskan permintaan ke Container Port tempat aplikasi berjalan.