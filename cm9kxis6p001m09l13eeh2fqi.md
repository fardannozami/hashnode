---
title: "Package fmt di Golang"
datePublished: Thu Apr 17 2025 05:37:58 GMT+0000 (Coordinated Universal Time)
cuid: cm9kxis6p001m09l13eeh2fqi
slug: package-fmt-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744868173618/f621ffc0-43b5-42fe-98e2-932629d85781.png
tags: go

---

Jika kamu baru mulai belajar Golang (Go), salah satu paket (package) pertama yang akan sering kamu temui adalah `fmt`. Paket ini merupakan bagian dari **standard library** Golang dan sangat penting untuk melakukan **formatting** dan **output**, seperti mencetak ke terminal atau membaca input dari pengguna.

Dalam artikel ini, kita akan membahas:

* Apa itu package `fmt`
    
* Fungsi-fungsi yang umum digunakan
    
* Format kode dalam `Printf`
    
* Contoh penggunaannya
    
* Tips penting untuk pemula
    

---

## Apa Itu Package `fmt`?

`fmt` adalah singkatan dari *format*. Paket ini menyediakan fungsi untuk:

* Mencetak ke layar
    
* Menulis ke string
    
* Membaca input dari user (secara terbatas)
    
* Format data dengan gaya `printf` seperti di bahasa C
    

---

## Fungsi-Fungsi Umum di Package `fmt`

Berikut beberapa fungsi `fmt` yang paling sering digunakan:

| Fungsi | Keterangan |
| --- | --- |
| `fmt.Print()` | Mencetak ke layar tanpa newline |
| `fmt.Println()` | Mencetak ke layar dengan newline |
| `fmt.Printf()` | Mencetak dengan format tertentu |
| `fmt.Sprint()` | Mengembalikan string hasil formatting (tanpa mencetak) |
| `fmt.Sprintf()` | Seperti `Printf`, tapi hasilnya dikembalikan sebagai string |
| `fmt.Scan()` | Membaca input dari user |
| `fmt.Scanln()` | Membaca input sampai newline |

---

## Format Kode dalam `Printf`

Saat menggunakan `fmt.Printf`, kamu bisa menggunakan *format verbs* untuk mengatur bagaimana nilai ditampilkan. Berikut daftar yang umum digunakan:

| Format | Keterangan |
| --- | --- |
| `%s` | String |
| `%d` | Integer (angka bulat) |
| `%f` | Float (angka desimal) |
| `%t` | Boolean |
| `%v` | Format default dari suatu nilai |
| `%+v` | Sama seperti `%v`, tapi untuk struct akan menampilkan nama field-nya juga |
| `%#v` | Menampilkan representasi Go-syntax dari nilai |
| `%T` | Menampilkan tipe data dari nilai |
| `%%` | Menampilkan karakter `%` literal |

### Contoh:

```go
package main

import "fmt"

type User struct {
    Name string
    Age  int
}

func main() {
    u := User{Name: "Fardan", Age: 25}

    fmt.Printf("%v\n", u)    // {Fardan 25}
    fmt.Printf("%+v\n", u)   // {Name:Fardan Age:25}
    fmt.Printf("%#v\n", u)   // main.User{Name:"Fardan", Age:25}
    fmt.Printf("%T\n", u)    // main.User
    fmt.Printf("Nilai: 100%%\n") // Nilai: 100%
}
```

---

## Contoh Penggunaan Dasar

### 1\. `fmt.Print()` dan `fmt.Println()`

```go
package main

import "fmt"

func main() {
    fmt.Print("Halo")
    fmt.Print(" Dunia\n")
    
    fmt.Println("Hello")
    fmt.Println("World")
}
```

---

### 2\. `fmt.Printf()` - Format Output

```go
package main

import "fmt"

func main() {
    nama := "Fardan"
    umur := 25
    fmt.Printf("Nama saya %s dan umur saya %d tahun\n", nama, umur)
}
```

---

### 3\. `fmt.Sprintf()` - Simpan Hasil Format ke String

```go
package main

import "fmt"

func main() {
    pesan := fmt.Sprintf("1 + 1 = %d", 1+1)
    fmt.Println(pesan)
}
```

---

### 4\. `fmt.Scan()` dan `fmt.Scanln()` - Baca Input

```go
package main

import "fmt"

func main() {
    var nama string
    fmt.Print("Masukkan nama kamu: ")
    fmt.Scanln(&nama)
    fmt.Println("Halo,", nama)
}
```

---

## Tips Penting untuk Pemula

* Gunakan `Println` untuk mencetak sederhana.
    
* Gunakan `Printf` untuk format yang lebih fleksibel.
    
* Gunakan `%v`, `%+v`, dan `%#v` saat debugging data atau struct.
    
* Jangan lupa: `fmt.Scan()` butuh pointer (`&variable`) untuk menyimpan hasil input.
    
* Gunakan `%%` jika kamu ingin mencetak simbol `%`.
    

---

## Kesimpulan

Package `fmt` adalah salah satu alat utama untuk bekerja dengan output dan input dasar di Golang. Mudah dipakai, tapi juga sangat powerful. Dengan memahami `fmt`, kamu bisa membuat aplikasi terminal yang interaktif dan informatif.

---