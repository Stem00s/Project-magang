#Nginx Docker Deployment
Project ini dibuat untuk kerja praktek Cloud Engineer.

Deploy web server Nginx menggunakan Docker dan Docker Compose.

#Struktur
- Dockerfile → build image nginx custom
- index.html → halaman web custom
- docker-compose.yml → menjalankan container nginx

#Run
```bash
docker compose up -d --build
