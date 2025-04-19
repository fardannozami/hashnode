---
title: "Package strings dan strconv di Golang"
datePublished: Sat Apr 19 2025 15:02:44 GMT+0000 (Coordinated Universal Time)
cuid: cm9ocks0a000009judrfl25nb
slug: package-strings-dan-strconv-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745074939165/db4934ba-fcff-4436-b26a-2375cca2c097.png
tags: go

---

Setelah sebelumnya membahas `fmt`, `errors`, `os`, dan `flag`, kali ini kita akan membahas dua package penting lainnya dalam standard library Go, yaitu `strings` dan `strconv`. Keduanya sangat berguna untuk memproses dan memanipulasi **string dan konversi data** — kebutuhan yang sangat umum dalam hampir semua aplikasi.

---

## 1\. Package `strings` – Manipulasi String

Package `strings` berisi fungsi-fungsi untuk memanipulasi dan memeriksa string. Ini sangat membantu saat kamu bekerja dengan data teks, input dari user, atau saat parsing data.

### 📦 Import

```go
import "strings"
```

---

### ✂️ Fungsi-Fungsi Umum `strings`

| Fungsi | Deskripsi |
| --- | --- |
| `strings.Contains(s, substr)` | Cek apakah `s` mengandung `substr` |
| `strings.HasPrefix(s, prefix)` | Cek apakah `s` diawali dengan `prefix` |
| `strings.HasSuffix(s, suffix)` | Cek apakah `s` diakhiri dengan `suffix` |
| `strings.ToLower(s)` | Ubah ke huruf kecil semua |
| `strings.ToUpper(s)` | Ubah ke huruf besar semua |
| `strings.Split(s, sep)` | Pecah string berdasarkan separator |
| `strings.Join([]string, sep)` | Gabungkan slice string menjadi satu string |
| `strings.ReplaceAll(s, old, new)` | Ganti semua `old` dengan `new` |
| `strings.TrimSpace(s)` | Hapus spasi di awal dan akhir string |

---

### 🧪 Contoh Penggunaan

```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    teks := "  Hello Golang World!  "

    fmt.Println(strings.Contains(teks, "Golang"))         // true
    fmt.Println(strings.HasPrefix(teks, "Hello"))         // false (ada spasi di awal)
    fmt.Println(strings.ToLower(teks))                    // "  hello golang world!  "
    fmt.Println(strings.TrimSpace(teks))                  // "Hello Golang World!"
    fmt.Println(strings.ReplaceAll(teks, "Golang", "Go")) // ganti Golang → Go

    daftar := "apel,pisang,jeruk"
    buah := strings.Split(daftar, ",")
    fmt.Println(buah) // [apel pisang jeruk]

    gabung := strings.Join(buah, " | ")
    fmt.Println(gabung) // "apel | pisang | jeruk"
}
```

---

## 2\. Package `strconv` – Konversi Angka dan String

Package `strconv` sangat berguna untuk mengubah nilai dari **string ke angka**, dan sebaliknya. Ini sering dipakai saat membaca input, bekerja dengan API, atau menyimpan data.

### 📦 Import

```go
import "strconv"
```

---

### 🔁 Fungsi-Fungsi Umum `strconv`

| Fungsi | Deskripsi |
| --- | --- |
| `strconv.Itoa(int)` | Konversi int → string |
| `strconv.Atoi(string)` | Konversi string → int |
| `strconv.ParseBool(string)` | Konversi string → bool (`"true"`, `"false"`) |
| `strconv.FormatFloat(float64, fmt, prec, bitSize)` | Konversi float → string |
| `strconv.ParseFloat(string, bitSize)` | Konversi string → float64 |

---

### 🧪 Contoh Penggunaan

```go
package main

import (
    "fmt"
    "strconv"
)

func main() {
    // int ke string
    umur := 25
    s := strconv.Itoa(umur)
    fmt.Println("Umur saya adalah " + s)

    // string ke int
    angka, err := strconv.Atoi("123")
    if err == nil {
        fmt.Println("Hasil:", angka+10)
    }

    // string ke bool
    b, _ := strconv.ParseBool("true")
    fmt.Println("Boolean:", b)

    // float ke string
    f := 3.14159
    fs := strconv.FormatFloat(f, 'f', 2, 64)
    fmt.Println("Pi:", fs)

    // string ke float
    pi, _ := strconv.ParseFloat("3.14", 64)
    fmt.Println("Float:", pi*2)
}
```

---

## 📌 Tips Pemula

* Selalu cek error saat konversi string ke angka (`Atoi`, `ParseFloat`, dll).
    
* Gunakan `strings.TrimSpace()` sebelum parsing angka dari input user.
    
* `strings.Split()` dan `Join()` sangat berguna saat parsing CSV atau query.
    
* `strconv.Itoa()` = Integer to ASCII (alias string).
    

---

## Kesimpulan

Dua package ini adalah fondasi penting untuk manipulasi data teks dan angka:

| Package | Fungsi Utama |
| --- | --- |
| `strings` | Proses dan ubah string |
| `strconv` | Konversi antar string dan tipe data (int, float, bool) |

Dengan menguasai keduanya, kamu bisa menangani input/output, parsing data, dan memformat informasi dengan lebih fleksibel.