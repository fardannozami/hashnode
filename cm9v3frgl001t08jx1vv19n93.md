---
title: "Membuat Goroutine di Golang"
datePublished: Thu Apr 24 2025 08:21:16 GMT+0000 (Coordinated Universal Time)
cuid: cm9v3frgl001t08jx1vv19n93
slug: membuat-goroutine-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745482860455/11c4e54d-2165-49af-ae31-c2c58a7a92ac.png
tags: go

---

Halo teman-teman! Selamat datang di tutorial pertama tentang **Goroutine** di Golang. Jika kamu baru belajar pemrograman atau baru mengenal Go, jangan khawatir. Artikel ini akan menjelaskan konsep Goroutine dengan **bahasa yang sederhana dan mudah dipahami**. Kita akan mulai dari dasar:

* Apa itu Goroutine
    
* Kenapa penting
    
* Cara membuatnya
    
* Contoh penggunaan dengan unit test
    

---

## Apa Itu Goroutine?

**Goroutine** adalah fitur powerful di Golang yang memungkinkan kita menjalankan fungsi secara **konkuren** (bersamaan). Ini mirip seperti *thread* pada bahasa lain, tapi goroutine jauh lebih ringan dan efisien.

> Bayangkan kamu sedang memasak sambil menyeduh kopi. Kamu tidak menunggu kopi selesai baru mulai memasak, kan? Nah, begitu juga goroutine bekerja — melakukan banyak hal dalam waktu yang bersamaan.

Goroutine dikelola oleh **Go runtime**, bukan sistem operasi, jadi kamu bisa membuat ribuan goroutine tanpa takut membebani sistem.

---

## Kenapa Perlu Menggunakan Goroutine?

* ✅ **Ringan** – Hanya butuh beberapa KB memori
    
* ✅ **Konkuren** – Jalankan banyak tugas secara paralel
    
* ✅ **Mudah digunakan** – Hanya butuh kata kunci `go`
    

---

## Cara Membuat Goroutine

Untuk menjalankan fungsi secara konkuren, cukup tambahkan `go` di depan pemanggilan fungsi.

### 📦 Contoh 1: Menjalankan Satu Goroutine (Unit Test)

```go
package goroutine_test

import (
	"fmt"
	"testing"
	"time"
)

func sayHello() {
	fmt.Println("Hello from goroutine")
}

func TestSayHello(t *testing.T) {
	go sayHello()
	fmt.Println("Hello from unit test")
	time.Sleep(10 * time.Millisecond) // beri waktu goroutine berjalan
}
```

🧠 **Penjelasan:**

* `go sayHello()` akan menjalankan fungsi `sayHello()` sebagai goroutine.
    
* `fmt.Println(...)` tetap berjalan di goroutine utama.
    
* `time.Sleep` digunakan agar unit test tidak langsung selesai sebelum goroutine sempat dijalankan.
    

---

### 🔁 Contoh 2: Menjalankan Banyak Goroutine dalam Loop

```go
func displayNumber(number int) {
	fmt.Printf("Number: %d\n", number)
}

func TestManyGoroutines(t *testing.T) {
	for i := 0; i < 1000; i++ {
		go displayNumber(i)
	}
	time.Sleep(50 * time.Millisecond)
}
```

🧠 **Penjelasan:**

* Fungsi `displayNumber()` mencetak angka ke layar.
    
* Kita memanggilnya sebanyak 1000 kali sebagai goroutine.
    
* Karena semua goroutine berjalan paralel, urutannya **tidak akan berurutan**.
    

---

## ⚠️ Catatan Penting

1. **Goroutine Tidak Menjamin Urutan**  
    Karena dijalankan secara konkuren, kamu **tidak bisa prediksi** urutan output-nya.
    
2. **Unit Test Bisa Selesai Duluan**  
    Jika `Test...()` selesai duluan, goroutine bisa tidak sempat jalan.  
    Untuk sementara, kita pakai `time.Sleep()` sebagai solusi praktis.  
    Nanti, kita akan belajar teknik yang lebih baik pakai `sync.WaitGroup`.
    

---

## Kesimpulan

Dengan Goroutine, kamu bisa membuat program Go menjalankan banyak tugas secara **bersamaan** hanya dengan satu keyword: `go`. Ini sangat berguna untuk membuat program yang lebih cepat dan responsif, terutama saat menangani banyak proses atau request.

---

Kalau kamu punya pertanyaan atau ide eksperimen lain, langsung aja tulis di komentar atau diskusi.  
Selamat mencoba, dan sampai jumpa di tutorial selanjutnya! 🚀