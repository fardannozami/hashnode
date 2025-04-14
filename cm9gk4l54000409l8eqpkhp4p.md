---
title: "Pointer di Golang"
datePublished: Mon Apr 14 2025 04:11:56 GMT+0000 (Coordinated Universal Time)
cuid: cm9gk4l54000409l8eqpkhp4p
slug: pointer-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744603855287/7f63168d-43a0-41ad-9123-81368115909e.png
tags: go

---

Halo, teman-teman!  
Di tutorial kali ini, kita akan bahas salah satu konsep penting dalam pemrograman Go (Golang), yaitu **pointer**. Pointer ini sangat berguna, apalagi kalau kamu udah mulai masuk ke dunia backend dan main-main dengan data yang besar atau manipulasi memory.

Sebagai developer backend, kamu pasti akan sering nemuin pointer ini, jadi yuk kita pelajari bareng-bareng!

---

## Apa itu Pointer?

Pointer adalah **variabel yang menyimpan alamat memori dari variabel lain**.

Daripada menyimpan nilai langsung, pointer menyimpan *lokasi* dari nilai tersebut di memori.

> Bayangin kamu punya alamat rumah. Daripada bawa rumahnya, kamu cukup bawa alamatnya aja kalau mau kasih tahu orang lain. Nah, pointer itu seperti alamat rumah tersebut.

---

## Kenapa Pointer Penting?

Pointer digunakan karena:

* **Efisiensi memory** (gak perlu copy data besar-besaran)
    
* **Modifikasi data secara langsung di fungsi lain**
    
* **Kerja bareng dengan data di heap (bukan stack)**
    

---

## Cara Kerja Pointer di Golang

### 1\. Mengambil Alamat Variabel (`&`)

Kita bisa dapat alamat dari suatu variabel dengan menggunakan simbol `&`.

```go
package main

import "fmt"

func main() {
    x := 10
    fmt.Println("Nilai x:", x)
    fmt.Println("Alamat x:", &x)
}
```

Output:

```plaintext
Nilai x: 10
Alamat x: 0xc000014088  // ini contoh alamat memory
```

---

### 2\. Menyimpan Alamat ke Pointer (`*`)

Kita bisa menyimpan alamat tersebut ke variabel pointer.

```go
package main

import "fmt"

func main() {
    x := 10
    var ptr *int = &x

    fmt.Println("Nilai pointer ptr:", ptr)
    fmt.Println("Nilai yang ditunjuk ptr:", *ptr)
}
```

Penjelasan:

* `*int` artinya `ptr` adalah pointer ke `int`
    
* `*ptr` digunakan untuk mengakses nilai yang ditunjuk pointer
    

### 🏠 Analogi Simpel

Bayangin:

* `x` = sebuah kotak berisi angka **10**
    
* `&x` = alamat kotak (misalnya: "Jl. Memory No. 80")
    
* `ptr` = catatan alamat tersebut
    
* `*ptr` = buka kotaknya dan lihat isinya → hasilnya 10
    

---

### 3\. Mengubah Nilai Lewat Pointer

Pointer bisa digunakan untuk **mengubah nilai asli** dari suatu variabel.

```go
package main

import "fmt"

func ubahNilai(val *int) {
    *val = 20
}

func main() {
    x := 10
    ubahNilai(&x)
    fmt.Println("Setelah diubah:", x)  // Output: 20
}
```

Perhatikan:

* Kita kirim alamat `x` ke fungsi `ubahNilai`
    
* Fungsi mengubah nilai di alamat itu, sehingga `x` ikut berubah
    

---

## Analoginya Simpel:

* `x` = 10 → kamu punya sebuah kotak berisi angka 10
    
* `&x` → kamu tahu alamat kotaknya
    
* `*ptr` → kamu buka kotak dari alamat itu dan ambil isinya
    

---

## Kesalahan Umum Saat Pakai Pointer

1. **Dereferencing null pointer**  
    Jangan pernah akses nilai dari pointer kosong (`nil`), nanti panik (panic!).
    
    ```go
    var ptr *int
    fmt.Println(*ptr)  // PANIC!
    ```
    
2. **Lupa pakai** `*` saat ingin ubah nilai  
    Kalau kamu cuma pakai pointer tanpa `*`, kamu nggak ubah isinya, cuma alamatnya aja.
    

---

## Kapan Harus Pakai Pointer?

* Saat mau **modifikasi variabel dari fungsi**
    
* Saat kerja dengan **struct besar** (biar hemat memori)
    
* Saat bikin **linked list, tree, atau data structure lainnya**
    
* Saat kamu pakai **interface yang butuh pointer receiver**
    

---

## Contoh Kasus Real-Life di Backend

Misal kamu punya struct `User` dan kamu mau update nama user dari dalam fungsi:

```go
type User struct {
    Name string
}

func GantiNama(u *User) {
    u.Name = "Fardan"
}

func main() {
    user := User{Name: "Anonim"}
    GantiNama(&user)
    fmt.Println(user.Name)  // Output: Fardan
}
```

---

## Kesimpulan

Pointer di Go itu:

* Menyimpan alamat variabel lain
    
* Berguna untuk efisiensi dan manipulasi langsung
    
* Sering dipakai di fungsi, struct, dan data besar
    

Kalau kamu paham pointer, kamu bisa bikin program Go yang lebih efisien dan powerful!

---

Kalau kamu masih bingung, gak apa-apa. Pointer itu memang butuh waktu untuk benar-benar nyantol di kepala. Latihan aja terus, dan jangan ragu nanya!