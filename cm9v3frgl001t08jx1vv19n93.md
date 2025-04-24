---
title: "Membuat Goroutine di Golang"
datePublished: Thu Apr 24 2025 08:21:16 GMT+0000 (Coordinated Universal Time)
cuid: cm9v3frgl001t08jx1vv19n93
slug: membuat-goroutine-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745482860455/11c4e54d-2165-49af-ae31-c2c58a7a92ac.png
tags: go

---

Halo teman-teman! Selamat datang di tutorial pertama tentang **Goroutine** di Golang. Jika kamu baru belajar pemrograman atau baru mengenal Go (Golang), jangan khawatir. Artikel ini akan menjelaskan konsep Goroutine dengan bahasa yang sederhana dan mudah dipahami. Kita akan mulai dari dasar, apa itu Goroutine, mengapa penting, dan bagaimana cara membuatnya.

## Apa Itu Goroutine?

Goroutine adalah salah satu fitur unggulan di Golang yang memungkinkan kita menjalankan tugas atau fungsi secara **konkuren** (bersamaan). Dalam bahasa pemrograman lain, kita mungkin mengenal istilah *thread* untuk menjalankan tugas secara paralel. Nah, Goroutine mirip seperti thread, tapi jauh lebih ringan dan efisien.

Bayangkan kamu sedang memasak di dapur. Kamu bisa merebus air sambil memotong sayuran, bukan? Nah, Goroutine memungkinkan programmu melakukan banyak hal sekaligus seperti itu, tanpa harus menunggu satu tugas selesai baru mengerjakan tugas berikutnya.

Goroutine dikelola oleh *runtime* Golang, bukan oleh sistem operasi, sehingga sangat cepat dan hemat sumber daya. Satu program Golang bisa menjalankan ribuan Goroutine sekaligus tanpa membuat komputer menjadi lambat.

## Mengapa Menggunakan Goroutine?

1. **Efisiensi**: Goroutine sangat ringan dibandingkan thread tradisional. Satu Goroutine hanya membutuhkan beberapa kilobyte memori.
    
2. **Konkurensi**: Memungkinkan program menjalankan banyak tugas secara bersamaan, sehingga lebih cepat dalam menyelesaikan pekerjaan.
    
3. **Kemudahan**: Golang menyediakan sintaks yang sangat sederhana untuk membuat dan mengelola Goroutine.
    

## Cara Membuat Goroutine di Golang

Sekarang, mari kita coba membuat Goroutine pertama kita. Untuk membuat Goroutine, kita hanya perlu menambahkan kata kunci `go` sebelum memanggil sebuah fungsi. Ketika fungsi dipanggil dengan `go`, fungsi tersebut akan berjalan secara konkuren (di latar belakang) tanpa menghentikan eksekusi program utama.

Berikut adalah langkah-langkah dan contoh kode sederhana:

### Langkah 1: Buat Fungsi Sederhana

Kita akan membuat fungsi yang mencetak pesan ke layar. Fungsi ini akan kita jalankan sebagai Goroutine.

### Langkah 2: Panggil Fungsi dengan Kata Kunci `go`

Kita akan menggunakan `go` untuk menjalankan fungsi tersebut secara konkuren.

### Contoh Kode

```go
package main

import (
	"fmt"
	"time"
)

func sayHello() {
	fmt.Println("Hello from Goroutine!")
}

func main() {
	// Memanggil fungsi sayHello sebagai Goroutine
	go sayHello()

	// Mencetak pesan dari fungsi main
	fmt.Println("Hello from main!")

	// Memberi waktu agar Goroutine selesai sebelum program berakhir
	time.Sleep(time.Second)
}
```

### Penjelasan Kode

1. **Fungsi** `sayHello()`: Fungsi ini hanya mencetak pesan "Hello from Goroutine!".
    
2. `go sayHello()`: Dengan menambahkan `go` di depan pemanggilan fungsi, fungsi `sayHello()` akan berjalan sebagai Goroutine, artinya berjalan di latar belakang.
    
3. `time.Sleep(time.Second)`: Karena Goroutine berjalan secara konkuren, program utama (`main`) bisa selesai sebelum Goroutine sempat berjalan. Untuk mencegah itu, kita tambahkan `time.Sleep` agar program utama menunggu sebentar sebelum selesai.
    

### Output yang Mungkin

Saat kamu menjalankan kode di atas, outputnya bisa seperti ini:

```plaintext
Hello from main!
Hello from Goroutine!
```

Atau bisa juga urutannya terbalik, seperti:

```plaintext
Hello from Goroutine!
Hello from main!
```

Mengapa urutannya bisa berbeda? Karena Goroutine berjalan secara konkuren, kita tidak bisa memastikan mana yang selesai lebih dulu. Ini adalah sifat alami dari konkurensi.

## Contoh Lain: Goroutine dengan Loop

Mari kita coba contoh yang sedikit lebih menarik. Kita akan membuat dua fungsi yang berjalan bersamaan menggunakan Goroutine.

```go
package main

import (
	"fmt"
	"time"
)

func printNumbers() {
	for i := 1; i <= 5; i++ {
		fmt.Println("Number:", i)
		time.Sleep(100 * time.Millisecond) // Menunda sedikit agar output terlihat jelas
	}
}

func printLetters() {
	for _, letter := range []string{"a", "b", "c", "d", "e"} {
		fmt.Println("Letter:", letter)
		time.Sleep(100 * time.Millisecond)
	}
}

func main() {
	go printNumbers() // Jalankan sebagai Goroutine
	go printLetters() // Jalankan sebagai Goroutine

	// Tunggu sebentar agar semua Goroutine selesai
	time.Sleep(1 * time.Second)
	fmt.Println("Program selesai!")
}
```

### Penjelasan Kode

1. Fungsi `printNumbers()` mencetak angka 1 sampai 5.
    
2. Fungsi `printLetters()` mencetak huruf a sampai e.
    
3. Kedua fungsi dijalankan sebagai Goroutine dengan `go`, sehingga mereka berjalan bersamaan.
    
4. `time.Sleep` di dalam fungsi digunakan untuk memperlambat eksekusi agar kita bisa melihat outputnya dengan jelas.
    

### Output yang Mungkin

Outputnya bisa bervariasi, tapi kurang lebih seperti ini:

```plaintext
Number: 1
Letter: a
Number: 2
Letter: b
Number: 3
Letter: c
Number: 4
Letter: d
Number: 5
Letter: e
Program selesai!
```

Kamu akan melihat bahwa angka dan huruf dicetak secara bergantian atau acak, tergantung pada bagaimana Goroutine dijadwalkan oleh runtime Golang.

## Catatan Penting

1. **Jangan Lupa Menunggu Goroutine**: Jika program utama (`main`) selesai sebelum Goroutine selesai, maka Goroutine tidak akan sempat berjalan. Untuk saat ini, kita menggunakan `time.Sleep` sebagai solusi sederhana. Di tutorial berikutnya, kita akan belajar cara yang lebih baik menggunakan `sync.WaitGroup`.
    
2. **Goroutine Tidak Menjamin Urutan**: Karena sifatnya yang konkuren, kita tidak bisa memprediksi urutan eksekusi Goroutine.
    

## Kesimpulan

Goroutine adalah cara yang sangat mudah dan efisien untuk menjalankan tugas secara konkuren di Golang. Dengan hanya menambahkan kata kunci `go` sebelum pemanggilan fungsi, kita bisa membuat fungsi berjalan di latar belakang tanpa menghentikan program utama. Ini sangat berguna untuk membuat program yang responsif dan cepat, terutama untuk tugas-tugas seperti mengambil data dari internet, memproses file, atau menangani banyak pengguna sekaligus.

Di tutorial berikutnya, kita akan belajar bagaimana mengelola banyak Goroutine dengan lebih baik menggunakan `sync.WaitGroup` dan bagaimana menghindari masalah seperti *race condition*. Jadi, tetap ikuti seri tutorial ini ya!

Jika kamu punya pertanyaan atau ada bagian yang kurang jelas, jangan ragu untuk bertanya di kolom komentar. Selamat belajar Golang, dan sampai jumpa di tutorial berikutnya! 🚀

---

Semoga artikel ini membantu kamu memahami dasar-dasar Goroutine di Golang. Selamat mencoba kode-kode di atas dan bereksperimen dengan Goroutine!