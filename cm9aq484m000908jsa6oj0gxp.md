---
title: "Panic, defer dan recovery di Golang"
datePublished: Thu Apr 10 2025 02:13:00 GMT+0000 (Coordinated Universal Time)
cuid: cm9aq484m000908jsa6oj0gxp
slug: panic-defer-dan-recovery-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744251160661/e18a02d5-4d73-4a6e-8710-a47e14ad1e93.png
tags: go

---

Halo teman-teman! 👋  
Di artikel kali ini, kita akan bahas salah satu topik penting di Golang yang sering bikin bingung pemula, yaitu **panic**, **defer**, dan **recover**. Ini adalah bagian dari error handling di Go, dan sangat penting dipahami supaya program kita bisa tetap stabil meskipun ada error yang nggak terduga.

## 1\. Apa Itu `panic`?

Bayangin kamu lagi bikin aplikasi dan tiba-tiba ada kejadian yang “fatal banget”, misalnya file penting nggak ketemu atau database nggak bisa diakses. Nah, dalam kasus seperti ini, Go punya fungsi bawaan yang namanya `panic()`.

Kalau kita manggil `panic()`, program bakal langsung *panic* dan berhenti jalan... **kecuali** kita tangani dengan `recover()` (nanti kita bahas).

Contoh:

```go
func main() {
    panic("Aduh, ada yang error!")
}
```

Kalau kamu jalanin kode di atas, hasilnya:

```plaintext
panic: Aduh, ada yang error!
```

Dan program bakal langsung berhenti. Jadi `panic` itu kayak tombol darurat buat berhentiin program.

---

## 2\. Apa Itu `defer`?

`defer` itu dipakai buat *menunda* eksekusi suatu fungsi sampai fungsi di mana dia dipanggil selesai. Biasanya dipakai buat *cleanup*, misalnya nutup file, nutup koneksi database, dll.

Contoh sederhana:

```go
func main() {
    defer fmt.Println("Ini dicetak terakhir")
    fmt.Println("Halo!")
}
```

Output:

```plaintext
Halo!
Ini dicetak terakhir
```

Jadi walaupun `defer` ditulis lebih dulu, dia dieksekusi paling terakhir.

Defer itu **sangat penting** saat kita dealing sama `panic`, karena dia tetap akan jalan walaupun program lagi panic.

---

## 3\. Menangani Panic dengan `recover`

Nah, sekarang bagian menariknya.

Kalau kita mau *menangkap* `panic` supaya program nggak langsung berhenti, kita bisa pakai `recover()`.

Tapi `recover()` **hanya bisa jalan dari dalam fungsi yang dipanggil lewat** `defer`.

Contoh:

```go
func main() {
    defer handlePanic()

    fmt.Println("Sebelum panic")
    panic("Oops! Sesuatu terjadi")
    fmt.Println("Setelah panic (ini nggak akan jalan)")
}

func handlePanic() {
    if r := recover(); r != nil {
        fmt.Println("Panic tertangkap:", r)
    }
}
```

Output:

```plaintext
Sebelum panic
Panic tertangkap: Oops! Sesuatu terjadi
```

Keren, kan? Programnya tetap jalan meskipun sempat panic.

---

## 4\. Ilustrasi Lengkap: Panic + Defer + Recover

```go
package main

import "fmt"

func main() {
    fmt.Println("Program dimulai")

    safeDivision(10, 2)
    safeDivision(10, 0)

    fmt.Println("Program selesai dengan selamat")
}

func safeDivision(a, b int) {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Error tertangkap:", r)
        }
    }()

    if b == 0 {
        panic("Pembagian dengan nol tidak diperbolehkan")
    }

    result := a / b
    fmt.Println("Hasil pembagian:", result)
}
```

Output:

```plaintext
Program dimulai
Hasil pembagian: 5
Error tertangkap: Pembagian dengan nol tidak diperbolehkan
Program selesai dengan selamat
```

Lihat? Meski ada pembagian dengan nol, program tetap lanjut karena panic-nya ditangani.

---

## Kesimpulan

* `panic()` = untuk menghentikan eksekusi saat terjadi error fatal.
    
* `defer` = untuk menunda eksekusi sampai fungsi selesai (berguna untuk cleanup dan recover).
    
* `recover()` = untuk menangkap panic supaya program nggak langsung berhenti.
    

---

## Tips dari Senior 😎

1. **Jangan sering-sering pakai panic!** Biasanya cukup pakai `error` biasa, dan panic hanya untuk kondisi yang benar-benar darurat.
    
2. Taruh `recover()` di fungsi yang di-*defer*, biar bisa tangani panic dengan aman.
    
3. Gunakan `defer` untuk *clean up* resource (file, koneksi, dll), bahkan kalau panic terjadi sekalipun.
    

---

Kalau kamu masih bingung atau mau tanya-tanya soal implementasi di project kamu, feel free buat nanya, ya! 🔥