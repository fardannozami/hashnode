---
title: "Tipe Data String, Boolean, dan Number di Golang"
datePublished: Wed Mar 26 2025 21:09:01 GMT+0000 (Coordinated Universal Time)
cuid: cm8qf3dvf000l0al8ceh5dhar
slug: tipe-data-string-boolean-dan-number-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1743023297501/b7d24ffc-3b52-49cc-9459-86e79289e82d.png
tags: go

---

Golang (Go) adalah bahasa pemrograman yang dikembangkan oleh Google dan dikenal dengan kesederhanaan serta kecepatannya. Dalam pemrograman, tipe data sangat penting karena menentukan bagaimana sebuah nilai disimpan dan diproses dalam memori komputer. Pada tutorial ini, kita akan membahas tiga tipe data dasar dalam Golang: **string, boolean, dan number**.

## 1\. Tipe Data String

Tipe data **string** digunakan untuk menyimpan teks atau kumpulan karakter. String di Golang harus menggunakan tanda kutip ganda (`" "`).

### Contoh penggunaan string:

```go
package main

import "fmt"

func main() {
    var nama string = "Golang"
    pesan := "Selamat belajar Golang!"
    fmt.Println(nama)
    fmt.Println(pesan)
}
```

### Operasi String:

* **Menggabungkan String**
    
    ```go
    teks1 := "Hello, "
    teks2 := "World!"
    hasil := teks1 + teks2
    fmt.Println(hasil) // Output: Hello, World!
    ```
    
* **Menghitung panjang string**
    
    ```go
    fmt.Println(len("Golang")) // Output: 6
    ```
    
* **Mengakses karakter dalam string**
    
    ```go
    fmt.Println("Golang"[0]) // Output: 71 (ASCII dari 'G')
    ```
    

## 2\. Tipe Data Boolean

Tipe data **boolean** hanya memiliki dua nilai: `true` atau `false`. Boolean sering digunakan dalam percabangan atau kondisi.

### Contoh penggunaan boolean:

```go
package main

import "fmt"

func main() {
    var isGolangMudah bool = true
    fmt.Println(isGolangMudah) // Output: true
}
```

### Operasi Boolean:

* **Logika AND (**`&&`)
    
    ```go
    fmt.Println(true && false) // Output: false
    ```
    
* **Logika OR (**`||`)
    
    ```go
    fmt.Println(true || false) // Output: true
    ```
    
* **Logika NOT (**`!`)
    
    ```go
    fmt.Println(!true) // Output: false
    ```
    

## 3\. Tipe Data Number

Golang memiliki dua jenis utama tipe data angka: **integer (bilangan bulat)** dan **floating-point (bilangan desimal)**.

### a. Integer

Integer digunakan untuk menyimpan bilangan bulat dan terbagi menjadi beberapa jenis berdasarkan ukuran memori:

* `int8` : -128 hingga 127
    
* `int16` : -32,768 hingga 32,767
    
* `int32` : -2,147,483,648 hingga 2,147,483,647
    
* `int64` : -9,223,372,036,854,775,808 hingga 9,223,372,036,854,775,807
    
* `uint8` : 0 hingga 255
    
* `uint16` : 0 hingga 65,535
    
* `uint32` : 0 hingga 4,294,967,295
    
* `uint64` : 0 hingga 18,446,744,073,709,551,615
    

**Contoh penggunaan integer:**

```go
package main

import "fmt"

func main() {
    var angka1 int = 10
    angka2 := 20
    fmt.Println(angka1 + angka2) // Output: 30
}
```

### b. Floating-Point

Untuk bilangan desimal, Golang menggunakan tipe `float32` dan `float64`.

**Contoh penggunaan float:**

```go
package main

import "fmt"

func main() {
    var pi float64 = 3.14
    fmt.Println(pi) // Output: 3.14
}
```

### Operasi Matematika pada Number

* **Penjumlahan:** `+`
    
* **Pengurangan:** `-`
    
* **Perkalian:** `*`
    
* **Pembagian:** `/`
    
* **Modulus (sisa bagi):** `%` (hanya untuk integer)
    

**Contoh operasi matematis:**

```go
package main

import "fmt"

func main() {
    a := 10
    b := 3
    fmt.Println(a + b)  // Output: 13
    fmt.Println(a - b)  // Output: 7
    fmt.Println(a * b)  // Output: 30
    fmt.Println(a / b)  // Output: 3
    fmt.Println(a % b)  // Output: 1
}
```

## Kesimpulan

Pada tutorial ini, kita telah mempelajari tiga tipe data utama di Golang:

1. **String** untuk menyimpan teks.
    
2. **Boolean** untuk menyimpan nilai `true` atau `false`.
    
3. **Number** untuk menyimpan bilangan bulat (`int`) dan bilangan desimal (`float`).
    

Semoga tutorial ini membantu pemahaman Anda tentang tipe data di Golang. Selamat belajar dan terus berlatih! 🚀