---
title: "Golang Todolist CLI #1 – Menginisiasi Proyek"
datePublished: Thu May 08 2025 11:08:25 GMT+0000 (Coordinated Universal Time)
cuid: cmaf9kn4f000b09l2ervd5qya
slug: golang-todolist-cli-1-menginisiasi-proyek
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746702474311/d42b68f8-235c-498d-a59e-b788b066175b.png
tags: go

---

Selamat datang di seri **Golang Todolist CLI** bersama saya, Ajitama! 🎉  
Di seri ini, kita akan membangun aplikasi to-do list berbasis command line (CLI) menggunakan bahasa Go. Proyek ini cocok banget buat kamu yang ingin belajar membuat aplikasi nyata dengan Go, sekaligus memahami struktur proyek, best practice, dan penerapan konsep seperti modularisasi, testing, hingga CI/CD.

Di seri pertama ini, kita akan mulai dari langkah paling dasar: **menginisiasi proyek**.

---

## Langkah 1 – Membuat Folder Proyek

Pertama, mari kita buat folder khusus untuk proyek ini. Buka terminal dan jalankan perintah berikut:

```bash
mkdir golang-todolist-cli
cd golang-todolist-cli
```

Folder `golang-todolist-cli` akan menjadi direktori utama proyek kita.

---

## Langkah 2 – Inisialisasi Modul Go

Setelah berada di dalam folder proyek, kita perlu menginisialisasi modul Go. Modul ini akan membantu Go mengenali dependensi dan struktur proyek kita.

Jalankan perintah berikut:

```bash
go mod init github.com/fardannozami/golang-todolist-cli
```

Perintah ini akan membuat file `go.mod` yang berisi informasi nama modul dan versi Go yang digunakan.

---

## Hasil

Setelah menjalankan perintah di atas, struktur awal proyek kita akan terlihat seperti ini:

```plaintext
golang-todolist-cli/
└── go.mod
```

Proyek berhasil diinisiasi, dan kita siap untuk mulai ngoding! 🚀

---

## Penutup

Itu dia langkah awal untuk memulai proyek **Golang Todolist CLI**. Di seri berikutnya, kita akan mulai menyusun struktur folder dan membuat command CLI pertamanya.

Sampai jumpa di seri selanjutnya! 👋  
Jangan lupa follow atau bookmark agar kamu tidak ketinggalan update!

---

Ingin dilanjutkan ke seri berikutnya sekarang?