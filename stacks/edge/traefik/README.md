# Panduan Konfigurasi Traefik

## 📚 Apa itu Traefik?

Traefik adalah **reverse proxy** dan **API gateway**. Bayangkan seperti seorang pengarah lalu lintas:
- Mendengarkan permintaan web masuk pada port 80 (HTTP) dan 443 (HTTPS)
- Mengarahkan permintaan tersebut ke layanan yang tepat di Docker
- Dapat secara otomatis menemukan layanan tanpa konfigurasi manual

---

## 🗂️ File di Folder Ini

### 1. **docker-compose.yaml**
File ini mengatur container Traefik menggunakan Docker Compose.

### 2. **traefik.yaml**
File ini mengonfigurasi bagaimana Traefik berperilaku.

---

## 📖 Memahami docker-compose.yaml

```yaml
services:
  traefik:
    image: traefik:v3.6.8
```
- **image**: Menggunakan image Traefik resmi, versi 3.6.8

```yaml
    container_name: traefik
    restart: unless-stopped
```
- **container_name**: Memberi nama container "traefik"
- **restart**: Otomatis memulai ulang Traefik jika crash (kecuali dihentikan manual)

### Bagian Ports
```yaml
    ports:
      - "80:80"      # Lalu lintas HTTP
      - "443:443"    # Lalu lintas HTTPS (aman)
      - "8080:8080"  # Akses dashboard
```
- **80:80** → Port 80 komputer Anda terhubung ke port 80 container
- **443:443** → Port 443 komputer Anda terhubung ke port 443 container
- **8080:8080** → Akses dashboard Traefik di `http://localhost:8080`

### Bagian Volumes
```yaml
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "./traefik.yaml:/traefik.yaml:ro"
```
- **docker.sock**: Memungkinkan Traefik melihat dan berinteraksi dengan container Docker lainnya
- **traefik.yaml**: Memuat file konfigurasi (`:ro` = read-only/hanya baca)

### Bagian Networks
```yaml
    networks:
      - frontend
networks:
  frontend:
    external: true
```
- **frontend**: Jaringan Docker yang dapat diikuti oleh layanan lain
- **external: true**: Jaringan sudah ada (dibuat terpisah)

---

## 📖 Memahami traefik.yaml

### Pengaturan Global
```yaml
global:
  checkNewVersion: true
  sendAnonymousUsage: false
```
- **checkNewVersion**: Traefik memeriksa pembaruan (hanya informasi)
- **sendAnonymousUsage**: Tidak mengirim data penggunaan ke pengembang Traefik

### Logging
```yaml
logs:
  level: DEBUG
```
- **DEBUG**: Menampilkan log detail untuk membantu pemecahan masalah

### Dashboard
```yaml
api:
  dashboard: true
  insecure: true
```
- **dashboard: true**: Mengaktifkan dashboard web di `:8080`
- **insecure: true**: Dashboard dapat diakses tanpa password (tidak disarankan untuk produksi!)

### Entry Points
```yaml
entryPoints:
  web:
    address: ":80"
  websecure:
    address: ":443"
```
- **entryPoints**: Menentukan tempat Traefik mendengarkan lalu lintas
- **web**: Mendengarkan port 80 (HTTP)
- **websecure**: Mendengarkan port 443 (HTTPS)

### Providers
```yaml
providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false
```
- **docker provider**: Traefik secara otomatis menemukan container Docker lainnya
- **endpoint**: Terhubung ke Docker melalui file socket
- **exposedByDefault: false**: Hanya layanan yang ditandai secara eksplisit yang akan dijalankan (lebih aman)

---

## 🚀 Memulai Cepat

### Mulai Traefik
```bash
docker-compose up -d
```

### Akses Dashboard
Buka browser Anda: `http://localhost:8080`

### Hentikan Traefik
```bash
docker-compose down
```

---

## 🔗 Menghubungkan Layanan Lain

Untuk mengarahkan lalu lintas ke container Docker lain, tambahkan label ini di docker-compose.yaml-nya:

```yaml
services:
  myapp:
    image: my-app:latest
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.myapp.rule=Host(`myapp.localhost`)"
      - "traefik.http.services.myapp.loadbalancer.server.port=3000"
    networks:
      - frontend
```

Ini memberi tahu Traefik:
- ✅ Arahkan lalu lintas untuk `myapp.localhost` ke layanan ini
- ✅ Teruskan permintaan ke port 3000 aplikasi

---

## ⚠️ Catatan Penting

- **Keamanan Dashboard**: Pengaturan `insecure: true` memungkinkan siapa pun mengakses dashboard. Gunakan password di produksi.
- **Jaringan**: Jaringan `frontend` harus ada sebelum memulai. Buat dengan:
  ```bash
  docker network create frontend
  ```
- **SSL/HTTPS**: Pengaturan saat ini tidak memiliki sertifikat SSL. Anda memerlukan konfigurasi tambahan agar `websecure` berfungsi dengan baik.

---

## 📚 Pelajari Lebih Lanjut
- [Dokumentasi Traefik](https://doc.traefik.io/)
- [Dokumentasi Docker Compose](https://docs.docker.com/compose/)
