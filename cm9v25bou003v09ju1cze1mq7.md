---
title: "Package testing di Golang"
datePublished: Thu Apr 24 2025 07:45:10 GMT+0000 (Coordinated Universal Time)
cuid: cm9v25bou003v09ju1cze1mq7
slug: package-testing-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745480661994/67d46685-7858-4abe-9c82-0ed91fb6f33b.png
tags: go

---

## 🧠 Pendahuluan

Testing adalah bagian penting dari pengembangan software. Dengan menulis *test*, kita bisa memastikan kode berjalan sesuai harapan, menghindari bug, dan memudahkan refactoring.

Golang punya `package testing` bawaan yang powerful, mudah digunakan, dan terintegrasi langsung dengan perintah `go test`. Dalam artikel ini, kita akan belajar:

* Struktur dasar fungsi test
    
* Cara menjalankan test
    
* Penggunaan `t.Error`, `t.Fatal`
    
* Contoh sub-test dan table-driven test
    

---

## 1\. ✍️ Struktur Dasar File Test

* File test harus diakhiri dengan `_test.go`, contoh: `math_test.go`
    
* Fungsi test harus diawali dengan `Test`, contoh: `func TestTambah(t *testing.T)`
    

---

### 📄 Contoh: File `math.go`

```go
package mathutil

func Tambah(a, b int) int {
    return a + b
}
```

---

### 🧪 File Test: `math_test.go`

```go
package mathutil

import "testing"

func TestTambah(t *testing.T) {
    hasil := Tambah(2, 3)
    if hasil != 5 {
        t.Errorf("Expected 5, got %d", hasil)
    }
}
```

---

## 2\. ▶️ Menjalankan Test

Jalankan perintah:

```bash
go test
```

Output:

```plaintext
PASS
ok  	mathutil	0.001s
```

Untuk lihat hasil lengkap:

```bash
go test -v
```

---

## 3\. 🛑 `t.Error` vs `t.Fatal`

| Fungsi | Kegunaan |
| --- | --- |
| `t.Error(...)` | Melaporkan error, tapi test tetap lanjut |
| `t.Fatal(...)` | Melaporkan error dan langsung hentikan test |

Contoh:

```go
if hasil != 5 {
    t.Fatal("Fatal: hasil tidak sesuai")
}
```

---

## 4\. 🧪 Sub-Test

Cocok untuk mengelompokkan skenario test:

```go
func TestTambah(t *testing.T) {
    t.Run("Positif", func(t *testing.T) {
        if Tambah(2, 3) != 5 {
            t.Error("Gagal tambah positif")
        }
    })

    t.Run("Negatif", func(t *testing.T) {
        if Tambah(-2, -3) != -5 {
            t.Error("Gagal tambah negatif")
        }
    })
}
```

---

## 5\. 📋 Table-Driven Test

Efisien untuk banyak kasus uji:

```go
func TestTambah(t *testing.T) {
    tes := []struct {
        nama      string
        a, b, exp int
    }{
        {"Positif", 2, 3, 5},
        {"Negatif", -2, -3, -5},
        {"Campur", -2, 3, 1},
    }

    for _, tc := range tes {
        t.Run(tc.nama, func(t *testing.T) {
            if hasil := Tambah(tc.a, tc.b); hasil != tc.exp {
                t.Errorf("Expected %d, got %d", tc.exp, hasil)
            }
        })
    }
}
```

---

## 6\. 🧪 Coverage Test

Untuk melihat seberapa banyak kode kita yang sudah di-*test*:

```bash
go test -cover
```

Untuk generate laporan HTML:

```bash
go test -coverprofile=cover.out
go tool cover -html=cover.out
```

---

## 🔚 Kesimpulan

* `package testing` membuat test di Go jadi simple dan terstruktur.
    
* Kamu cukup buat file `_test.go` dan fungsi `TestXxx`.
    
* Gunakan [`t.Run`](http://t.Run) untuk sub-test, dan table-driven untuk test banyak kasus.
    
* Jangan lupa pakai `go test -v` dan `-cover` untuk feedback yang lebih jelas.