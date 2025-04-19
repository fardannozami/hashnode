---
title: "Package errors, os, dan flag di Golang"
datePublished: Thu Apr 17 2025 17:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm9ocarvu00180alh04teeqol
slug: package-errors-os-dan-flag-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745074231998/e4c81c21-88ae-4dea-9883-13e2aff0f30f.png
tags: go

---

Setelah memahami dasar penggunaan package `fmt`, kini kita lanjut ke tiga package penting lainnya dalam standard library Golang: `errors`, `os`, dan `flag`. Ketiganya sering digunakan dalam pembuatan aplikasi CLI (Command Line Interface) dan sangat berguna untuk menangani error, akses sistem operasi, dan parsing argumen dari command line.

---

## 1\. Package `errors` – Membuat dan Menangani Error

Go menggunakan pendekatan eksplisit dalam menangani error, bukan exception seperti di banyak bahasa lain. Untuk membuat error baru, kita bisa menggunakan package `errors`.

### 📦 Import

```go
import "errors"
```

### 🧪 Contoh Penggunaan

```go
package main

import (
    "errors"
    "fmt"
)

func bagi(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("tidak bisa membagi dengan nol")
    }
    return a / b, nil
}

func main() {
    hasil, err := bagi(10, 0)
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Println("Hasil:", hasil)
    }
}
```

### ✅ Penjelasan

* [`errors.New`](http://errors.New)`()` membuat objek error baru.
    
* Biasanya error dikembalikan dari fungsi dan dicek dengan `if err != nil`.
    

---

## 2\. Package `os` – Akses Sistem Operasi

Package `os` digunakan untuk:

* Mengakses environment variable
    
* Membaca/menulis file
    
* Keluar dari program
    
* Menangani argumen command line (`os.Args`)
    

### 📦 Import

```go
import "os"
```

### 🧪 Contoh: Membaca Argumen

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    fmt.Println("Argumen command line:", os.Args)
}
```

> `os.Args` adalah slice `[]string` yang berisi argumen saat program dijalankan. Index 0 adalah nama program itu sendiri.

### 🧪 Contoh: Keluar dari Program

```go
os.Exit(1) // Keluar dari program dengan kode status 1
```

---

## 3\. Package `flag` – Parsing Argumen Command Line

Package `flag` digunakan untuk membaca dan memproses **argumen dari command line** dengan cara yang lebih terstruktur. Ini sangat berguna saat kamu membuat aplikasi CLI yang menerima input seperti `--port=8080`, `--debug=true`, dan sebagainya.

### 📦 Import

```go
import "flag"
```

### 🎯 Fungsi-Fungsi Utama

* `flag.String(name, default, description)`  
    Untuk membuat flag berupa string.
    
* [`flag.Int`](http://flag.Int)`(name, default, description)`  
    Untuk membuat flag berupa integer.
    
* `flag.Bool(name, default, description)`  
    Untuk membuat flag berupa boolean (true/false).
    
* `flag.Parse()`  
    Digunakan untuk membaca flag setelah didefinisikan.
    

> Nilai dari flag disimpan dalam *pointer*, jadi perlu diakses dengan `*flagVariable`.

---

### 🧪 Contoh Penggunaan

```go
package main

import (
    "flag"
    "fmt"
)

func main() {
    // Definisi flags
    nama := flag.String("nama", "Guest", "Nama pengguna")
    umur := flag.Int("umur", 0, "Umur pengguna")
    debug := flag.Bool("debug", false, "Mode debug")

    // Parsing input dari CLI
    flag.Parse()

    // Menggunakan hasilnya
    fmt.Println("Nama:", *nama)
    fmt.Println("Umur:", *umur)
    fmt.Println("Debug mode:", *debug)
}
```

---

### 🚀 Cara Menjalankan

Misalkan kamu menyimpan file di `main.go`, jalankan seperti ini di terminal:

```bash
go run main.go --nama=Fardan --umur=25 --debug=true
```

Output:

```plaintext
Nama: Fardan
Umur: 25
Debug mode: true
```

Kalau kamu tidak memberikan nilai, maka akan dipakai **default value** yang ditentukan saat `flag.String`, [`flag.Int`](http://flag.Int), atau `flag.Bool`.

---

### ✅ Kenapa Harus Pakai `flag`?

Tanpa `flag`, kamu harus parsing argumen secara manual dari `os.Args`. Dengan `flag`, kamu bisa:

* Menentukan tipe data
    
* Menambahkan deskripsi yang muncul otomatis saat pakai `--help`
    
* Menghindari kesalahan parsing sendiri
    

---

## Kesimpulan

Tiga package ini sangat penting dalam membuat aplikasi Go yang berbasis terminal:

| Package | Fungsi Utama |
| --- | --- |
| `errors` | Membuat dan mengembalikan error |
| `os` | Akses ke sistem operasi, argumen CLI, dan environment |
| `flag` | Parsing argumen CLI dengan struktur yang jelas |

Dengan menguasai ketiganya, kamu sudah bisa mulai membuat aplikasi CLI yang interaktif dan lebih “siap produksi”.

---