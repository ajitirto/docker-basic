
---
marp: true
theme: default
class:
  - invert
paginate: true
backgroundColor: '#1e1e2e'
color: '#cdd6f4'
size: 16:9
style: |
  section {
    font-family: "Segoe UI", sans-serif;
    padding: 1.2em;
  }
  h1 { color: #89b4fa; font-size: 1.8em; margin-bottom: 0.3em; }
  h2 { color: #b4befe; font-size: 1.3em; margin-bottom: 0.2em; }
  h3 { color: #cba6f7; font-size: 1em; }
  code {
    background-color: #313244;
    color: #a6e3a1;
    padding: 1px 4px;
    border-radius: 3px;
    font-size: 0.8em;
    font-family: "Fira Code", monospace;
  }
  pre {
    background-color: #181825;
    padding: 8px;
    border-radius: 4px;
    font-size: 0.7em;
    overflow-x: auto;
  }
  ul { line-height: 1.5; font-size: 0.85em; }
  li { margin-bottom: 0.2em; }
  blockquote {
    border-left: 3px solid #89b4fa;
    padding-left: 0.6em;
    color: #bac2de;
    font-size: 0.8em;
  }
  table { width: 100%; font-size: 0.75em; }
  th { background: #313244; color: #f9e2af; padding: 4px; }
  td { padding: 3px 5px; border-bottom: 1px solid #45475a; }
  .center { text-align: center; }
  .two-col { display: grid; grid-template-columns: 1fr 1fr; gap: 1em; }
  footer { color: #6c7086; font-size: 0.6em; }
---

<!-- _class: lead -->

# Docker Fundamental

### From Zero to Container Hero

---

# Agenda

<div class="two-col" style="font-size: 0.75em;">

1. Apa itu Docker?
2. Instalasi
3. Konsep Dasar
4. Docker Images
5. Docker Container
6. Dockerfile
7. ...

</div>

---

<div class="two-col" style="font-size: 0.75em;">

7. Docker Volumes
8. Docker Network
9. Docker Compose
10. Docker Registry
11. Resource Management
12. Logging & Monitoring
13. Best Practices

</div>

---

<!-- _class: lead -->

# 1. Apa itu Docker?

---

# Docker — Containerization Platform

> Docker adalah platform untuk **build, ship, dan run** aplikasi di dalam container yang terisolasi

```text
Tanpa Docker          Dengan Docker
─────────────         ─────────────
"Works on my          App berjalan sama
machine!" 😅          di mana saja ✅
```

---

# VM vs Container

| Aspek | Virtual Machine | Docker Container |
|-------|----------------|-----------------|
| Boot time | Menit | Detik |
| Ukuran | GB | MB |
| Isolasi | Penuh (OS) | Proses |
| Resource | Berat | Ringan |
| Portabilitas | Terbatas | Tinggi |

> Container berbagi kernel OS host — lebih efisien dari VM

---

# Arsitektur Docker

```text
┌─────────────────────────────────┐
│         Docker Client           │  ← docker CLI
└────────────────┬────────────────┘
                 │ REST API
┌────────────────▼────────────────┐
│         Docker Daemon           │  ← dockerd
│  ┌──────────┐  ┌─────────────┐  │
│  │  Images  │  │  Containers │  │
│  └──────────┘  └─────────────┘  │
└─────────────────────────────────┘
```

---

<!-- _class: lead -->

# 2. Instalasi

---

# Install Docker — Linux (Ubuntu/Debian)

```bash
# Update & install dependencies
sudo apt update
sudo apt install ca-certificates curl gnupg

# Add Docker GPG key & repo
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Jalankan tanpa sudo
sudo usermod -aG docker $USER
newgrp docker
```

---

# Verifikasi Instalasi

```bash
docker --version
# Docker version 27.x.x

docker info
# Shows daemon info

docker run hello-world
# Pulls image & runs test container
```

> Jika `hello-world` berhasil, Docker siap digunakan!

---

<!-- _class: lead -->

# 3. Konsep Dasar

---

# Komponen Utama

| Komponen | Penjelasan |
|----------|-----------|
| **Image** | Template read-only untuk container |
| **Container** | Instance image yang sedang berjalan |
| **Dockerfile** | Script untuk build image |
| **Registry** | Tempat menyimpan image (Docker Hub) |
| **Volume** | Penyimpanan data persisten |
| **Network** | Komunikasi antar container |

---

# Lifecycle Container

```text
Dockerfile  →  docker build  →  Image
                                  │
                             docker run
                                  │
                               Container
                              ┌───┴───┐
                           Running  Stopped
                              │        │
                           docker   docker
                            stop    start
```

---

<!-- _class: lead -->

# 4. Docker Images

---

# docker pull — Unduh Image

**Mengunduh image dari registry**

```bash
docker pull ubuntu
docker pull ubuntu:22.04        # Tag spesifik
docker pull nginx:alpine        # Versi alpine (kecil)
docker pull python:3.11-slim
```

> Tanpa tag = latest secara default

---

# docker images — Lihat Image

**Menampilkan image yang tersedia lokal**

```bash
docker images
docker images -a               # Termasuk intermediate
docker images nginx            # Filter by name
```

| Kolom | Arti |
|-------|------|
| REPOSITORY | Nama image |
| TAG | Versi |
| IMAGE ID | Hash unik |
| SIZE | Ukuran di disk |

---

# docker rmi — Hapus Image

**Menghapus image dari lokal**

```bash
docker rmi nginx
docker rmi nginx:1.25
docker rmi $(docker images -q)   # Hapus semua image
docker image prune               # Hapus image tidak terpakai
```

> ⚠️ Image tidak bisa dihapus jika masih digunakan container

---

# docker inspect — Detail Image

**Melihat metadata lengkap image**

```bash
docker inspect nginx
docker inspect --format='{{.Config.Cmd}}' nginx
docker history nginx            # Layer-layer image
```

---

<!-- _class: lead -->

# 5. Docker Container

---

# docker run — Jalankan Container

**Membuat & menjalankan container baru**

```bash
docker run nginx
docker run -d nginx                    # Detached (background)
docker run -it ubuntu bash             # Interactive terminal
docker run --name webserver nginx      # Beri nama
docker run -p 8080:80 nginx            # Port mapping
docker run --rm ubuntu echo "hello"    # Auto-remove setelah selesai
```

---

# docker run — Opsi Lengkap

| Opsi | Arti |
|------|------|
| `-d` | Background (detached) |
| `-it` | Interactive terminal |
| `--name` | Nama container |
| `-p host:container` | Port mapping |
| `-v host:container` | Volume mount |
| `-e KEY=VALUE` | Environment variable |
| `--rm` | Hapus otomatis setelah stop |
| `--restart` | Restart policy |

---

# docker ps — Lihat Container

**Menampilkan container yang berjalan**

```bash
docker ps                      # Hanya yang running
docker ps -a                   # Semua container
docker ps -q                   # Hanya ID
docker ps --filter status=exited
```

---

# Kelola Container

**Start, stop, restart, remove**

```bash
docker stop webserver          # Graceful stop
docker start webserver         # Start container
docker restart webserver       # Restart
docker kill webserver          # Force stop

docker rm webserver            # Hapus container
docker rm -f webserver         # Force remove (running)
docker container prune         # Hapus semua yang stopped
```

---

# docker exec — Masuk Container

**Menjalankan command di container yang berjalan**

```bash
docker exec -it webserver bash          # Masuk shell
docker exec webserver ls /etc/nginx     # Jalankan command
docker exec -it db mysql -u root -p     # Login MySQL
```

> Berbeda dengan `docker run` — `exec` masuk ke container yang **sudah berjalan**

---

# docker logs — Lihat Log

**Membaca output container**

```bash
docker logs webserver
docker logs -f webserver          # Follow (real-time)
docker logs --tail 50 webserver   # 50 baris terakhir
docker logs --since 1h webserver  # 1 jam terakhir
```

---

# docker cp — Copy File

**Transfer file antara host dan container**

```bash
docker cp file.txt webserver:/usr/share/nginx/html/
docker cp webserver:/etc/nginx/nginx.conf ./nginx.conf
```

---

<!-- _class: lead -->

# 6. Dockerfile

---

# Dockerfile — Instruksi Build

**File teks berisi instruksi untuk build image**

```dockerfile
# Contoh Dockerfile sederhana
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y nginx

COPY index.html /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

# Instruksi Dockerfile

| Instruksi | Fungsi |
|-----------|--------|
| `FROM` | Base image |
| `RUN` | Jalankan command saat build |
| `COPY` | Salin file dari host |
| `ADD` | Seperti COPY + support URL & tar |
| `WORKDIR` | Set working directory |
| `ENV` | Set environment variable |
| `EXPOSE` | Dokumentasi port |
| `CMD` | Command default saat run |
| `ENTRYPOINT` | Command yang selalu dijalankan |
| `ARG` | Build-time variable |

---

# CMD vs ENTRYPOINT

```dockerfile
# CMD — bisa di-override saat docker run
CMD ["nginx", "-g", "daemon off;"]
# docker run myimage           → nginx
# docker run myimage bash      → bash (override)

# ENTRYPOINT — selalu dijalankan, CMD jadi argumen
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
# docker run myimage           → nginx -g daemon off;
# docker run myimage -t        → nginx -t
```

---

# Contoh Dockerfile — Node.js App

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

USER node

CMD ["node", "server.js"]
```

> `USER node` — jangan jalankan sebagai root!

---

# docker build — Build Image

**Membuat image dari Dockerfile**

```bash
docker build .                        # Build di direktori ini
docker build -t myapp:1.0 .           # Beri nama & tag
docker build -f Dockerfile.prod .     # Dockerfile custom
docker build --no-cache .             # Build tanpa cache
docker build --build-arg ENV=prod .   # Pass build arg
```

---

# Multi-stage Build

**Kurangi ukuran image final**

```dockerfile
# Stage 1: Build
FROM node:20 AS builder
WORKDIR /app
COPY . .
RUN npm ci && npm run build

# Stage 2: Production
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --only=production
CMD ["node", "dist/server.js"]
```

> Image final tidak mengandung source code & dev tools!

---

# .dockerignore

**File yang diabaikan saat build**

```text
# .dockerignore
node_modules/
.git/
.env
*.log
dist/
coverage/
.DS_Store
```

> Seperti `.gitignore` tapi untuk Docker build context

---

<!-- _class: lead -->

# 7. Docker Volumes

---

# Mengapa Volume?

> Data dalam container **hilang** ketika container dihapus — gunakan Volume untuk data persisten

```text
Container ──── tanpa volume ──→ data hilang saat rm
Container ──── dengan volume ─→ data tetap ada ✓
```

---

# Jenis Storage

| Tipe | Penjelasan |
|------|-----------|
| **Volume** | Dikelola Docker, disimpan di `/var/lib/docker/volumes` |
| **Bind Mount** | Mount direktori dari host |
| **tmpfs** | Disimpan di memory saja |

---

# Volume Management

```bash
docker volume create mydata           # Buat volume
docker volume ls                      # Lihat semua volume
docker volume inspect mydata          # Detail volume
docker volume rm mydata               # Hapus volume
docker volume prune                   # Hapus volume tidak terpakai
```

---

# Menggunakan Volume

**Mount volume ke container**

```bash
# Named volume (dikelola Docker)
docker run -v mydata:/app/data nginx

# Bind mount (path absolut dari host)
docker run -v /home/user/data:/app/data nginx
docker run -v $(pwd):/app nginx

# Read-only mount
docker run -v mydata:/app/data:ro nginx
```

---

<!-- _class: lead -->

# 8. Docker Network

---

# Docker Network

**Komunikasi antar container**

```bash
docker network ls                     # Lihat semua network
docker network create mynet           # Buat network
docker network inspect mynet          # Detail
docker network rm mynet               # Hapus
```

---

# Tipe Network

| Tipe | Penjelasan |
|------|-----------|
| `bridge` | Default, container bisa saling komunikasi |
| `host` | Gunakan network host langsung |
| `none` | Tidak ada network |
| `overlay` | Multi-host (Docker Swarm) |
| `macvlan` | Assign MAC address langsung |

---

# Menghubungkan Container

```bash
# Buat network
docker network create appnet

# Jalankan container di network yang sama
docker run -d --name db --network appnet postgres
docker run -d --name app --network appnet myapp

# Dari container 'app', bisa ping 'db' by name
docker exec app ping db
```

> Container dalam network yang sama bisa communicate via nama!

---

# Port Mapping

**Expose port container ke host**

```bash
docker run -p 8080:80 nginx          # host:container
docker run -p 127.0.0.1:8080:80 nginx  # Bind ke IP spesifik
docker run -P nginx                  # Auto-assign semua exposed port

docker port webserver                # Lihat port mapping
```

---

<!-- _class: lead -->

# 9. Docker Compose

---

# Docker Compose

> Tool untuk mendefinisikan dan menjalankan **multi-container** aplikasi menggunakan file YAML

```bash
docker compose up                    # Jalankan semua service
docker compose up -d                 # Background
docker compose down                  # Stop & remove
docker compose ps                    # Status service
docker compose logs -f               # Follow logs
```

---

# docker-compose.yml — Struktur Dasar

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    depends_on:
      - app

  app:
    build: .
    environment:
      - DATABASE_URL=postgres://db/mydb
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

---

# Contoh — LAMP Stack

```yaml
services:
  web:
    image: php:8.2-apache
    ports:
      - "80:80"
    volumes:
      - ./src:/var/www/html
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: myapp
    volumes:
      - dbdata:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin
    ports:
      - "8080:80"
    environment:
      PMA_HOST: db

volumes:
  dbdata:
```

---

# Compose Commands

```bash
docker compose up --build            # Build ulang sebelum up
docker compose up --scale app=3      # Scale service
docker compose exec app bash         # Masuk ke container
docker compose run app npm test      # Jalankan one-off command
docker compose config                # Validasi & lihat config
docker compose pull                  # Pull semua image terbaru
```

---

<!-- _class: lead -->

# 10. Docker Registry

---

# Docker Hub

**Registry publik default Docker**

```bash
# Login
docker login

# Tag image untuk push
docker tag myapp:1.0 username/myapp:1.0

# Push ke Docker Hub
docker push username/myapp:1.0

# Pull dari Docker Hub
docker pull username/myapp:1.0
```

---

# Private Registry

**Jalankan registry sendiri**

```bash
# Jalankan local registry
docker run -d -p 5000:5000 --name registry registry:2

# Tag & push ke local registry
docker tag myapp:1.0 localhost:5000/myapp:1.0
docker push localhost:5000/myapp:1.0

# Pull dari local registry
docker pull localhost:5000/myapp:1.0
```

---

# Useful Image Commands

```bash
docker save myapp:1.0 -o myapp.tar        # Export ke file
docker load -i myapp.tar                  # Import dari file

docker tag myapp:1.0 myapp:latest         # Tambah tag
docker image prune -a                     # Hapus semua unused image
```

---

<!-- _class: lead -->

# 11. Resource Management

---

# Batasi CPU & Memory

**Cegah container menghabiskan resource host**

```bash
# Batasi memory
docker run --memory 512m nginx

# Batasi CPU
docker run --cpus 1.5 nginx           # Max 1.5 CPU cores
docker run --cpu-shares 512 nginx     # Relative weight

# Batasi keduanya
docker run --memory 1g --cpus 2 nginx
```

---

# docker stats — Monitor Resource

**Melihat resource usage real-time**

```bash
docker stats
docker stats webserver db app         # Container tertentu
docker stats --no-stream              # Snapshot sekali
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

| Kolom | Arti |
|-------|------|
| CPU % | Penggunaan CPU |
| MEM USAGE | RAM terpakai / limit |
| NET I/O | Traffic network |
| BLOCK I/O | Disk read/write |

---

# Restart Policy

**Otomatis restart container**

```bash
docker run --restart no nginx               # Default
docker run --restart always nginx           # Selalu restart
docker run --restart on-failure nginx       # Restart jika exit code != 0
docker run --restart on-failure:3 nginx     # Max 3x retry
docker run --restart unless-stopped nginx   # Restart kecuali distop manual
```

---

<!-- _class: lead -->

# 12. Logging & Monitoring

---

# Logging Drivers

**Cara Docker menyimpan log**

```bash
docker run --log-driver json-file nginx          # Default
docker run --log-driver syslog nginx             # System log
docker run --log-driver none nginx               # No logging

# Limit ukuran log
docker run \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  nginx
```

---

# docker events — Event Stream

**Melihat event Docker secara real-time**

```bash
docker events
docker events --filter container=webserver
docker events --filter event=start
docker events --since 1h
```

---

# docker system — Informasi & Cleanup

**Melihat penggunaan disk & membersihkan**

```bash
docker system df                    # Penggunaan disk Docker
docker system info                  # Info daemon
docker system prune                 # Hapus semua unused resource
docker system prune -a              # Termasuk semua unused image
docker system prune --volumes       # Termasuk volumes
```

---

<!-- _class: lead -->

# 13. Best Practices

---

# Image Best Practices

- **Gunakan official base image** — lebih aman & terpelihara
- **Pilih alpine/slim variant** — ukuran lebih kecil
- **Pin versi tag** — jangan `latest` di production
- **Multi-stage build** — pisahkan build dan runtime
- **Satu proses per container** — lebih mudah di-debug

```dockerfile
# ✅ Good
FROM node:20-alpine
FROM python:3.11-slim

# ❌ Avoid in production
FROM ubuntu:latest
```

---

# Dockerfile Best Practices

```dockerfile
# ✅ Gabungkan RUN untuk kurangi layer
RUN apt-get update && \
    apt-get install -y nginx curl && \
    rm -rf /var/lib/apt/lists/*

# ✅ Copy dependency dulu (cache optimization)
COPY package*.json ./
RUN npm ci

# Baru copy source code
COPY . .

# ✅ Jangan simpan secret di Dockerfile
# ❌ RUN export API_KEY=abc123
```

---

# Security Best Practices

```dockerfile
# Jangan jalankan sebagai root
RUN useradd -m -u 1001 appuser
USER appuser

# Read-only filesystem
docker run --read-only nginx

# Drop capabilities
docker run --cap-drop ALL --cap-add NET_BIND_SERVICE nginx

# Scan image untuk vulnerability
docker scout cves myapp:1.0
```

---

# Quick Reference

<div style="font-size: 0.7em;">

| Kategori | Command |
|----------|---------|
| **Image** | pull, push, build, rmi, images, tag |
| **Container** | run, start, stop, rm, ps, exec |
| **Log** | logs -f, events |
| **Info** | inspect, stats, top |
| **Volume** | volume create/ls/rm/prune |
| **Network** | network create/ls/rm |
| **Compose** | up, down, ps, logs, exec |
| **Registry** | login, push, pull, save, load |
| **Cleanup** | system prune, image prune |

</div>

---

<!-- _class: lead -->

# Terima Kasih

**Containerize semua hal!**

```bash
docker run --rm hello-world
```
