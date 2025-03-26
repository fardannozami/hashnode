---
title: "Panduan Instalasi Golang di WSL 2 Ubuntu"
datePublished: Fri Mar 21 2025 22:41:56 GMT+0000 (Coordinated Universal Time)
cuid: cm8jd7mim000a09joe76t97lx
slug: panduan-instalasi-golang-di-wsl-2-ubuntu
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1742596840651/c3bb983d-8292-4ad5-8c74-395659368bee.png
tags: go

---

Halo teman-teman! Pada tutorial ini, kita akan belajar cara menginstal Golang di WSL 2 Ubuntu dengan langkah-langkah yang mudah dipahami. Yuk, kita mulai!

## 1\. Mengatur WSL ke Versi 2

Sebelum menginstal Golang, pastikan WSL (Windows Subsystem for Linux) sudah diatur ke versi 2.

1. Buka **PowerShell** dengan mode **Administrator**.
    
2. Jalankan perintah berikut untuk mengatur WSL default ke versi 2:
    
    ```sh
    wsl.exe --set-default-version 2
    ```
    
3. Untuk melihat daftar distribusi Linux yang tersedia, gunakan perintah:
    
    ```sh
    wsl.exe -l -o
    ```
    

## 2\. Menginstal Ubuntu di WSL

1. Jalankan perintah berikut untuk menginstal Ubuntu di WSL:
    
    ```sh
    wsl.exe --install Ubuntu
    ```
    
2. Tunggu hingga instalasi selesai.
    
3. Setelah instalasi selesai, buka terminal Ubuntu di WSL dan pastikan sistem sudah siap digunakan.
    

## 3\. Menginstal Golang di WSL 2 Ubuntu

Sekarang kita akan menginstal Golang di Ubuntu yang ada di WSL.

1. **Unduh file Golang terbaru** dengan perintah berikut:
    
    ```sh
    wget https://go.dev/dl/go1.24.1.linux-amd64.tar.gz
    ```
    
2. **Ekstrak file Golang** ke direktori `/usr/local/`:
    
    ```sh
    sudo tar -C /usr/local -xzf go1.24.1.linux-amd64.tar.gz
    ```
    
3. **Menambahkan Golang ke PATH**:
    
    ```sh
    nano ~/.bashrc
    ```
    
    Tambahkan baris berikut di akhir file:
    
    ```sh
    export PATH=$PATH:/usr/local/go/bin
    ```
    
4. **Muat ulang konfigurasi shell** agar perubahan diterapkan:
    
    ```sh
    source ~/.bashrc
    ```
    

## 4\. Memverifikasi Instalasi Golang

Untuk memastikan Golang sudah terinstal dengan benar, jalankan perintah berikut:

```sh
 go version
```

Jika muncul output seperti berikut:

```sh
go version go1.24.1 linux/amd64
```

Berarti Golang sudah berhasil terinstal!

## Penutup

Selamat! 🎉 Sekarang Golang sudah siap digunakan di WSL 2 Ubuntu. Kamu bisa mulai belajar dan mengembangkan aplikasi dengan Golang. Semoga tutorial ini bermanfaat! Jika ada pertanyaan, silakan tinggalkan komentar.

Terima kasih! 🚀