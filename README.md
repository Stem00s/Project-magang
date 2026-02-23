Gitea dan MySQL Docker Setup

Project ini menjalankan Gitea menggunakan Docker dan MySQL sebagai database.


Apa itu Gitea?

Gitea adalah Git server self-hosted yang ringan dan open-source, alternatif dari GitHub atau GitLab

Fitur-Fitur:

   - Repository Git
   - Pull request
   - Issue tracker
   - User management
   - SSH & HTTP clone

Quick Start

1. Clone Repository

   - git clone 
   - cd Magang-stacks

2. Buat File .env
   - cp .env
   # edit nama, password dan ports sesuai dengan keinginan (namun disarankan tetap menggunakan default)

3. Jalankan Docker Compose
   
   - docker compose up -d

   # Cek Container
   - docker ps

4. Akses Gitea

   Web Dashboard
   - http://localhost:3000
   
   SSH
   - ssh://git@localhost:2222

   # Gunakan PORT
   - HTTP: 3000
   - SSH: 2222
   - MySQL: 3306

# Ringkasan Command

   - docker compose up -d 
   (Digunakan untuk menjalankan docker container)
   
   - docker compose down
   (Digunakan untuk menghapus seluruh container yang berada di dalam direktori)

   - docker start <continer_id>
   (Digunakan untuk memulai container)

   - docker restart <conatiner_id>
   (Digunakan untuk memulai ulang container)

   - docker logs gitea-db
   - docker logs gitea
   (Digunakan untuk Troubleshooting jika Gitea error)