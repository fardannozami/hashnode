---
title: "Package, Import, dan Access Modifier di Golang"
datePublished: Wed Apr 16 2025 06:33:46 GMT+0000 (Coordinated Universal Time)
cuid: cm9jk2ow4001e09l7gw6rhqgh
slug: package-import-dan-access-modifier-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744783869206/20e0ca40-6fe5-4444-8372-15d14805503b.png
tags: go

---

Halo teman-teman developer 👋  
Hari ini kita akan bahas salah satu fondasi penting dalam bahasa Go: **package**, **import**, dan **access modifier**. Ini adalah konsep dasar yang *wajib banget* kamu kuasai sebelum masuk ke hal-hal yang lebih kompleks seperti concurrency atau microservices.

Langsung aja, kita bahas satu per satu, pelan-pelan tapi pasti.

---

## 1\. Apa Itu Package di Golang?

Di Go, **package** itu seperti folder atau modul. Fungsinya untuk mengelompokkan kode agar rapi dan reusable.

* Semua file Go harus deklarasi `package` di baris paling atas.
    
* File yang termasuk dalam satu package akan bisa saling akses fungsi atau struct di dalamnya (selama aturan access modifier terpenuhi).
    

### Contoh:

```go
// file: mathutil/math.go
package mathutil

func Add(a, b int) int {
    return a + b
}
```

```go
// file: main.go
package main

import (
    "fmt"
    "golang-basic/mathutil"
)

func main() {
    result := mathutil.Add(2, 3)
    fmt.Println("Hasil:", result)
}
```

> Catatan: Struktur folder sangat penting. Go sangat bergantung pada struktur direktori yang sesuai dengan package-nya.

---

## 2\. Cara Menggunakan Import

Keyword `import` digunakan untuk memakai kode dari package lain.

Ada dua jenis import:

### a. Import dari Standard Library

```go
import "fmt"
```

### b. Import dari Package Sendiri atau Pihak Ketiga

```go
import "golang-basic/utils"
import "github.com/gin-gonic/gin"
```

### Tips:

* Kamu bisa kasih alias import kalau namanya panjang.
    

```go
import u "golang-basic/utils"
```

* Kalau kamu import tapi tidak digunakan, akan error.
    

```go
// Ini akan error
import "fmt"
```

* Kalau ingin import tapi tidak dipakai langsung (misalnya hanya untuk init function), pakai `_`
    

```go
import _ "github.com/lib/pq"
```

---

## 3\. Access Modifier di Golang

Ini penting banget! Di Go, tidak ada keyword `public`, `private`, atau `protected` seperti di bahasa lain.

**Penentu visibility cuma satu: huruf kapital.**

| Nama Function/Variable | Bisa Diakses dari Package Lain? |
| --- | --- |
| `Add()` | ✅ Yes (Public) |
| `subtract()` | ❌ No (Private) |

### Contoh:

```go
// file: pricing/pricing.go
package pricing

// calculateDiscount adalah fungsi private yang tidak boleh diakses dari luar package
func calculateDiscount(basePrice float64) float64 {
    if basePrice >= 1_000_000 {
        return basePrice * 0.2 // diskon 20%
    } else if basePrice >= 500_000 {
        return basePrice * 0.1 // diskon 10%
    }
    return 0
}

// CalculateFinalPrice adalah fungsi public yang digunakan dari luar package
func CalculateFinalPrice(basePrice float64) float64 {
    discount := calculateDiscount(basePrice)
    return basePrice - discount
}

```

```go
// file: main.go
package main

import (
    "fmt"
    "golang-basic/pricing"
)

func main() {
    price := 1200000.0
    final := pricing.CalculateFinalPrice(price)

    fmt.Printf("Harga awal: Rp%.0f\n", price)
    fmt.Printf("Harga setelah diskon: Rp%.0f\n", final)

    // fmt.Println(pricing.calculateDiscount(price)) // ❌ ERROR: cannot refer to unexported name
}
```

---

### 📝 Output:

```plaintext
Harga awal: Rp1200000
Harga setelah diskon: Rp960000
```

---

### 💡 Penjelasan Real-World:

* `calculateDiscount` sengaja dibuat **private** karena kita nggak mau orang luar package manggil langsung.
    
* Fungsi itu bisa berubah logika internalnya kapan aja (misal diskon berdasarkan musim, event, dsb).
    
* Dengan **membungkus** akses melalui `CalculateFinalPrice`, kita menjaga **abstraksi** dan **konsistensi bisnis logic**.
    

## Penutup

Untuk merangkum:

* Gunakan **package** untuk mengorganisir kode.
    
* Gunakan **import** untuk memakai kode dari package lain.
    
* **Huruf kapital** di awal nama menentukan apakah fungsi/variabel bisa diakses dari luar.
    

Ini adalah pondasi dari *code organization* di Golang. Kalau kamu ngerti ini, kamu udah siap buat ngelangkah ke design pattern, dependency injection, atau bahkan bikin REST API pake Go.

---

Kalau kamu masih bingung, jangan ragu buat tanya ya. Kita di sini buat belajar bareng-bareng. Happy coding