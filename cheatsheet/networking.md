<!-- markdownlint-disable -->
<!-- cSpell:disable -->

# Docker Networking Cheatsheet

Referensi cepat untuk mengelola jaringan (network) pada Docker Container.

---

## 🌐 Perintah Docker Network

| Perintah | Penjelasan | Contoh Penggunaan |
| :--- | :--- | :--- |
| `docker network ls` | Menampilkan daftar seluruh network yang ada di sistem Docker | `docker network ls` |
| `docker network create` | Membuat network baru dengan driver tertentu (default: bridge) | `docker network create my-network` |
| `docker network inspect` | Menampilkan informasi detail tentang suatu network (format JSON) | `docker network inspect my-network` |
| `docker network connect` | Menghubungkan container yang sedang aktif ke suatu network | `docker network connect my-network my-container` |
| `docker network disconnect` | Memutuskan hubungan container dari suatu network | `docker network disconnect my-network my-container` |
| `docker network rm` | Menghapus satu atau lebih network yang sedang tidak digunakan | `docker network rm my-network` |
| `docker network prune` | Menghapus seluruh network yang tidak digunakan oleh container manapun | `docker network prune` |

---

## 🛠️ Driver Network Populer

- **Bridge (Default)**: Digunakan ketika container membutuhkan komunikasi satu sama lain dalam satu host Docker yang sama.
- **Host**: Menghilangkan isolasi jaringan antara container dan host machine (container menggunakan IP dan port host secara langsung).
- **None**: Menonaktifkan seluruh jaringan untuk container (container tidak memiliki akses luar sama sekali).
- **Overlay**: Menghubungkan beberapa daemon Docker secara bersamaan agar container di host berbeda dapat berkomunikasi (digunakan pada Docker Swarm/Clustering).

---

## 💡 Port Mapping & DNS

- **Port Mapping (`-p <host_port>:<container_port>`)**: Menghubungkan port pada komputer host dengan port di dalam container.
  - *Contoh*: `docker run -d -p 8080:80 nginx` (mengakses nginx container via `http://localhost:8080`).
- **Container Name as DNS**: Container yang berada di dalam network custom buatan sendiri dapat saling menghubungi menggunakan nama containernya sebagai hostname.
  - *Contoh*: Container `app` menghubungi database melalui host `db` (bukan IP address).
