## 1.Docker Run

`docker run` digunakan untuk membuat container baru dari sebuah Docker Image sekaligus langsung menjalankannya.

```bash
docker run -it --name ubuntu1 ubuntu
```

### Penjelasan Parameter

| Parameter | Fungsi |
|-----------|--------|
| `docker run` | Membuat sekaligus menjalankan container baru. |
| `-it` | Menjalankan container dalam mode interaktif agar dapat menggunakan terminal Linux. |
| `--name ubuntu1` | Memberikan nama `ubuntu1` pada container. |
| `ubuntu` | Docker Image yang digunakan sebagai template pembuatan container. |

### Logic

Saat command dijalankan, Docker akan mengecek apakah Image `ubuntu` sudah tersedia di komputer.

- Jika Image sudah ada, Docker langsung membuat dan menjalankan container.
- Jika belum ada, Docker akan mengunduh Image terlebih dahulu dari Docker Hub, kemudian membuat dan menjalankan container.

Karena menggunakan opsi `-it`, terminal akan langsung terhubung ke dalam container sehingga kita bisa menjalankan perintah Linux.

### Hasil Praktik

<p align="center">
  <img src="../assets/screenshots/module-02/docker-run.png" alt="Docker Run" width="900">
</p>

### kesimpulan

Dari praktik ini saya memahami bahwa `docker run` bukan hanya digunakan untuk menjalankan container, tetapi juga dapat membuat container baru. Oleh karena itu, saya lebih mudah mengingat `docker run` sebagai **Create + Start** dalam satu command.

## 2. Docker Start

`docker start` digunakan untuk menjalankan kembali container yang sebelumnya sudah berhenti.

```bash
docker start ubuntu1
```

### Penjelasan Parameter

| Parameter | Fungsi |
|-----------|--------|
| `docker start` | Menjalankan kembali container yang sudah ada. |
| `ubuntu1` | Nama container yang akan dijalankan. |

### Logic

Berbeda dengan `docker run`, command ini **tidak membuat container baru**.

Docker hanya mencari container bernama `ubuntu1` yang sudah ada, kemudian menjalankannya kembali menggunakan data dan konfigurasi yang sama seperti sebelumnya.

### Hasil Praktik

<p align="center">
  <img src="../assets/screenshots/module-02/docker-start.png" alt="Docker Start" width="900">
</p>

### Kesimpulan

Saya memahami bahwa `docker start` digunakan ketika container sudah pernah dibuat sebelumnya. Jika container belum ada, maka command ini tidak dapat digunakan.

## 3. Docker Stop

`docker stop` digunakan untuk menghentikan container yang sedang berjalan.

```bash
docker stop ubuntu1
```

### Penjelasan Parameter

| Parameter | Fungsi |
|-----------|--------|
| `docker stop` | Menghentikan container yang sedang berjalan. |
| `ubuntu1` | Nama container yang akan dihentikan. |

### Logic

Docker akan mencari container bernama `ubuntu1`, kemudian mengirimkan sinyal untuk menghentikan proses yang sedang berjalan di dalam container.

Container hanya **berhenti**, bukan **terhapus**. Semua data dan konfigurasi container tetap tersimpan sehingga masih bisa dijalankan kembali menggunakan `docker start`.

### Hasil Praktik

<p align="center">
  <img src="../assets/screenshots/module-02/docker-stop.png" alt="Docker Stop" width="900">
</p>

### Remember

- `docker stop` hanya menghentikan container.
- Container masih bisa dijalankan kembali menggunakan `docker start`.

## 4.Docker PS

`docker ps` digunakan untuk menampilkan daftar container yang sedang berjalan.

```bash
docker ps
```

### Penjelasan Parameter

| Parameter | Fungsi |
|-----------|--------|
| `docker ps` | Menampilkan container yang sedang berjalan. |

### Logic

Saat command dijalankan, Docker akan mengambil informasi seluruh container yang berstatus **Running**, kemudian menampilkannya dalam bentuk tabel.

Informasi yang ditampilkan meliputi Container ID, Image, Command, Status, Port, dan Nama Container.

### Screenshot

<p align="center">
  <img src="../assets/screenshots/module-02/docker-ps.png" alt="Docker PS" width="900">
</p>

### Remember

- `docker ps` hanya menampilkan container yang sedang berjalan.
- Untuk melihat semua container, termasuk yang sudah berhenti, gunakan `docker ps -a`.

## 5. Docker Exec

`docker exec` digunakan untuk menjalankan command atau membuka terminal di dalam container yang sedang berjalan.

```bash
docker exec -it ubuntu1 bash
```

### Penjelasan Parameter

| Parameter | Fungsi |
|-----------|--------|
| `docker exec` | Menjalankan command di dalam container yang sedang berjalan. |
| `-it` | Membuka terminal interaktif. |
| `ubuntu1` | Nama container tujuan. |
| `bash` | Shell Linux yang akan dijalankan di dalam container. |

### Logic

Berbeda dengan `docker run`, command ini **tidak membuat container baru**.

Docker hanya mencari container yang sedang berjalan, kemudian membuka terminal (`bash`) di dalam container tersebut sehingga kita bisa menjalankan perintah Linux.

Jika container belum berjalan, `docker exec` tidak dapat digunakan.

### Screenshot

<p align="center">
  <img src="../assets/screenshots/module-02/docker-exec.png" alt="Docker Exec" width="900">
</p>

### Remember

- `docker exec` tidak membuat container baru.
- `docker exec` digunakan untuk masuk ke container yang sudah berjalan.
- Jika container berhenti, jalankan `docker start` terlebih dahulu sebelum menggunakan `docker exec`.