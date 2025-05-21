---
title: "Cara Install Laragon Terbaru 2025 + Konfigurasi Quick Add Lengkap"
datePublished: Wed May 21 2025 05:01:57 GMT+0000 (Coordinated Universal Time)
cuid: cmaxh7fxo000k09jvck372z8p
slug: cara-install-laragon-terbaru-2025-konfigurasi-quick-add-lengkap
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1747803704396/2bbf8f98-f4b3-442d-b1f4-be3674c95a46.png

---

Laragon adalah salah satu WAMP stack favorit bagi developer PHP dan web modern karena ringan, cepat, dan mudah dikustomisasi. Di artikel ini, kita akan **install Laragon versi terbaru (2025)** dan **menambahkan konfigurasi Quick Add** untuk mempermudah setup PHP, Node.js, database, dan tools populer lainnya.

---

## 🧩 1. Download Laragon Terbaru

### ✅ Opsi 1: Website Resmi

Langsung kunjungi situs resmi Laragon untuk mendapatkan versi terbaru:  
👉 [https://laragon.org/download/](https://laragon.org/download/)

### ✅ Opsi 2: GitHub Releases

Unduh langsung dari GitHub:

* **Laragon 8.1.0 (Terbaru)**  
    [https://github.com/leokhoa/laragon/releases/download/8.1.0/laragon-wamp.exe](https://github.com/leokhoa/laragon/releases/download/8.1.0/laragon-wamp.exe)
    
* **Laragon 6.0.0 (Stabil dan ringan)**  
    [https://github.com/leokhoa/laragon/releases/download/6.0.0/laragon-wamp.exe](https://github.com/leokhoa/laragon/releases/download/6.0.0/laragon-wamp.exe)
    

---

## ⚙️ 2. Install Laragon

1. Jalankan file `.exe` hasil unduhan.
    
2. Pilih lokasi instalasi (misalnya: `C:\Laragon`).
    
3. Centang opsi yang kamu butuhkan (misalnya Apache, MySQL, PHP).
    
4. Selesaikan instalasi.
    

---

## 🧪 3. Menambahkan Quick Add Configuration

Fitur **Quick Add** di Laragon memungkinkan kamu menambahkan versi PHP, Node.js, Database, dan tool lain secara cepat dari menu.

### 🔹 Langkah-langkah:

1. Buka Laragon.
    
2. Klik **Menu &gt; Tools &gt; Quick add &gt; Configuration**.
    
3. File teks akan terbuka (biasanya `QuickAdd.ini`).
    
4. Ganti atau tambahkan kode berikut ini:
    

```ini
# PHP
# https://windows.php.net/download/
*PHP-8.4=https://windows.php.net/downloads/releases/archives/php-8.4.5-nts-Win32-vs17-x64.zip
*PHP-8.3=https://windows.php.net/downloads/releases/archives/php-8.3.18-nts-Win32-vs16-x64.zip
*PHP-8.2=https://windows.php.net/downloads/releases/archives/php-8.2.27-nts-Win32-vs16-x64.zip
*PHP-8.1=https://windows.php.net/downloads/releases/archives/php-8.1.30-nts-Win32-vs16-x64.zip

php8.4.7-ts=https://windows.php.net/downloads/releases/php-8.4.7-Win32-vs17-x64.zip
php8.3.21-ts=https://windows.php.net/downloads/releases/php-8.3.21-Win32-vs16-x64.zip

---

# Web Servers
Apache-2.4.63=https://www.apachelounge.com/download/VS17/binaries/httpd-2.4.63-250122-win64-VS17.zip
Apache-2.4.57=https://www.apachelounge.com/download/VS16/binaries/httpd-2.4.57-win64-VS16.zip
Nginx-1.27.4=https://nginx.org/download/nginx-1.27.4.zip

---

# Node.js
node-23.11=https://nodejs.org/dist/v23.11.0/node-v23.11.0-win-x64.zip
node-22.14=https://nodejs.org/dist/v22.14.0/node-v22.14.0-win-x64.zip

---

# phpMyAdmin
phpmyadmin-6.0snapshot=https://files.phpmyadmin.net/snapshots/phpMyAdmin-6.0+snapshot-english.tar.xz
phpmyadmin=https://files.phpmyadmin.net/phpMyAdmin/5.2.2/phpMyAdmin-5.2.2-english.zip
pgadmin=https://ftp.postgresql.org/pub/pgadmin/pgadmin4/v9.3/source/pgadmin4-9.3.tar.gz

# DB Tools
DBeaver=https://dbeaver.io/files/dbeaver-ce-latest-win32.win32.x86_64.zip

---

# MySQL
mysql-9.1=https://dev.mysql.com/get/Downloads/MySQL-9.1/mysql-9.1.0-winx64.zip
mysql-8.4=https://dev.mysql.com/get/Downloads/MySQL-8.4/mysql-8.4.3-winx64.zip
mysql-8.0=https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.40-winx64.zip
mysql-5.7=https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.39-winx64.zip

---

# PostgreSQL
postgresql-17.2=https://sbp.enterprisedb.com/getfile.jsp?fileid=1259294
postgresql-16.6=https://sbp.enterprisedb.com/getfile.jsp?fileid=1259297
postgresql-15.10=https://sbp.enterprisedb.com/getfile.jsp?fileid=1259300

---

# MongoDB
mongodb-8=https://fastdl.mongodb.org/windows/mongodb-windows-x86_64-8.0.4.zip
mongodb-7=https://fastdl.mongodb.org/windows/mongodb-windows-x86_64-7.0.14.zip

---

# VS Code
code=https://go.microsoft.com/fwlink/?Linkid=850641

---

# Golang
go-1.24=https://go.dev/dl/go1.24.1.windows-amd64.zip
go-1.23=https://go.dev/dl/go1.23.4.windows-amd64.zip
```

5. Simpan dan tutup file tersebut.
    

---

## 🚀 4. Menggunakan Quick Add

Setelah konfigurasi selesai:

1. Buka menu **Tools &gt; Quick add**.
    
2. Pilih item yang ingin kamu tambahkan, misalnya `PHP-8.3` atau `mysql-8.0`.
    
3. Laragon akan otomatis mengunduh dan mengatur semuanya untukmu.
    

---

## 🎉 Selesai! Laragon Siap Pakai

Sekarang kamu sudah punya Laragon yang siap tempur:

* PHP multi versi
    
* Apache / Nginx
    
* MySQL / PostgreSQL / MongoDB
    
* Node.js terbaru
    
* phpMyAdmin & DBeaver
    
* Go + VS Code ready
    

---

## 💡 Tips Tambahan

* Laragon sangat cocok untuk development Laravel, WordPress, atau Node.js.
    
* Gunakan fitur **Auto Virtual Hosts** untuk akses cepat via domain lokal seperti `myproject.test`.
    
* Kamu bisa simpan proyek di `C:\Laragon\www\` agar langsung dikenali.