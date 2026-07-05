<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker Volumes Cheatsheet

Referensi cepat untuk mengelola data persisten menggunakan Docker Volumes dan Bind Mounts.

---

## 💾 Perintah Docker Volume

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker volume ls` | Menampilkan seluruh volume lokal yang tersimpan di sistem Docker | `docker volume ls` |
| `docker volume create` | Membuat volume baru yang dikelola oleh Docker | `docker volume create db-data` |
| `docker volume inspect` | Menampilkan informasi detail tentang lokasi penyimpanan dan metadata volume | `docker volume inspect db-data` |
| `docker volume rm` | Menghapus volume yang sedang tidak digunakan oleh container | `docker volume rm db-data` |
| `docker volume prune` | Menghapus semua volume lokal yang tidak terhubung dengan container manapun | `docker volume prune` |

---

## 🔄 Cara Menghubungkan Penyimpanan

### 1. Menggunakan Docker Volume (Rekomendasi)
Docker membuat direktori penyimpanan khusus di dalam filesystem host yang dikelola sepenuhnya oleh Docker daemon.

*   **Sintaks Lama (`-v`)**: `-v <volume_name>:<container_path>`
    ```bash
    docker run -d -v db-data:/var/lib/mysql mysql
    ```
*   **Sintaks Baru (`--mount`)**: `--mount source=<volume_name>,target=<container_path>`
    ```bash
    docker run -d --mount source=db-data,target=/var/lib/mysql mysql
    ```

### 2. Menggunakan Bind Mount
Menghubungkan folder/file spesifik dari host machine secara langsung ke dalam container (sangat berguna untuk development).

*   **Sintaks Lama (`-v`)**: `-v <host_path>:<container_path>`
    ```bash
    docker run -d -v d:/myproject:/app node
    ```
*   **Sintaks Baru (`--mount`)**: `--mount type=bind,source=<host_path>,target=<container_path>`
    ```bash
    docker run -d --mount type=bind,source=d:/myproject,target=/app node
    ```
