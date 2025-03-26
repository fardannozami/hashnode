---
title: "Menginisiasi Project Golang"
datePublished: Sun Mar 23 2025 15:28:52 GMT+0000 (Coordinated Universal Time)
cuid: cm8lsme8r00040ajra5bv1lmp
slug: menginisiasi-project-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1742743707383/ecc1b463-9b58-4b11-828d-a11617d75ebd.png
tags: go

---

Pada seri **Golang Basic**, kita akan membahas cara menginisiasi atau membuat project Golang dari awal. Dalam ekosistem Golang, project sering disebut dengan **module**. Artikel ini akan membimbing Anda langkah demi langkah untuk memulai project Golang dengan benar.

## 1\. Membuat Folder Project

Langkah pertama adalah membuat folder khusus untuk project yang akan kita bangun. Untuk itu, gunakan perintah berikut:

```sh
mkdir golang-basic
cd golang-basic
```

Dengan perintah ini, kita telah membuat direktori **golang-basic** dan masuk ke dalamnya.

## 2\. Menginisiasi Module Golang

Selanjutnya, kita perlu menginisiasi module Golang di dalam folder project yang telah dibuat. Jalankan perintah berikut:

```sh
go mod init golang-basic
```

Setelah menjalankan perintah ini, akan tercipta file `go.mod`. File ini berfungsi untuk menyimpan informasi terkait module yang sedang dibuat, termasuk nama module dan dependensi yang digunakan dalam project.

## 3\. Mengecek File `go.mod`

Untuk memastikan module telah berhasil dibuat, kita bisa melihat isi file `go.mod` dengan perintah:

```sh
cat go.mod
```

Contoh outputnya akan terlihat seperti ini:

```txt
module golang-basic

go 1.20
```

Di sini, `golang-basic` adalah nama module yang kita buat, dan `go 1.20` menunjukkan versi Golang yang digunakan.

## 4\. Siap untuk Pengembangan

Setelah langkah-langkah di atas selesai, project Golang kita sudah siap untuk dikembangkan lebih lanjut. Selanjutnya, kita bisa mulai menulis kode program dalam bahasa Golang!

Dengan memahami cara menginisiasi project Golang, Anda telah mengambil langkah pertama dalam pengembangan aplikasi berbasis Golang. Nantikan artikel berikutnya untuk belajar lebih dalam mengenai coding dalam Golang!