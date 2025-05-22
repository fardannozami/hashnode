---
title: "Doctor Booking App - Part 1"
datePublished: Thu May 22 2025 04:01:50 GMT+0000 (Coordinated Universal Time)
cuid: cmayuhyt5000009le7m9x6lfj
slug: doctor-booking-app-part-1

---

Halo! Pada kesempatan kali ini, saya akan membagikan proses awal pembuatan project **Doctor Booking App** menggunakan Laravel. Kita akan menggunakan beberapa tools modern seperti **Livewire**, **Filament Admin**, dan **Pest** untuk testing. Yuk langsung mulai!

## 1\. Membuat Project Laravel

Pertama-tama, pastikan kamu sudah menginstall Laravel Installer versi `^5.14`. Kemudian buat project baru dengan perintah berikut:

```bash
laravel new doctor-booking-app
```

## 2\. Setup Starter Kit Laravel

Untuk starter kit, kita akan menggunakan:

* **Livewire** untuk antarmuka dinamis.
    
* **Default authentication scaffolding** (tidak menggunakan Laravel Breeze/Jetstream).
    
* **Tanpa Laravel Volt**.
    
* **Testing menggunakan Pest** (tanpa PHPUnit).
    

Jika diminta opsi saat setup, kamu bisa jawab seperti ini:

* Livewire: ✅
    
* Authentication: ✅
    
* Laravel Volt: ❌
    
* Unit Testing: Pest ✅
    

Setelah project selesai di-generate, jalankan perintah berikut untuk menginstall dependencies frontend:

```bash
npm install && npm run build
```

## 3\. Masuk ke Direktori Project

Pindah ke direktori project dan buka dengan code editor favoritmu (misalnya VS Code):

```bash
cd doctor-booking-app
code .
```

## 4\. Install Filament Admin Panel

Selanjutnya kita akan menambahkan Filament Admin Panel untuk manajemen dashboard admin:

```bash
composer require filament/filament:"^3.3" -W
```

> **Catatan:** Pastikan ekstensi **ZIP** aktif di PHP-mu. Jika belum aktif, silakan aktifkan terlebih dahulu (bisa dicek lewat `php -m | grep zip`).

Lalu install panel adminnya:

```bash
php artisan filament:install --panels
```

## 5\. Cek Dashboard Admin

Setelah proses instalasi selesai, kamu sekarang sudah bisa mengakses **dashboard admin** di:

```plaintext
http://localhost:8000/admin
```

Kamu akan diminta login, dan jika belum punya user, silakan buat user baru terlebih dahulu melalui fitur register.

---

## Penutup

Setup awal sudah selesai! 🎉  
Kita sekarang punya project Laravel yang siap dikembangkan dengan struktur modern, authentication, dan admin panel.

Di **Part 2**, kita akan mulai mendesain model untuk dokter, jadwal, dan booking. Stay tuned!

> Jangan lupa simpan progresmu di Git, dan commit secara berkala ya!

---