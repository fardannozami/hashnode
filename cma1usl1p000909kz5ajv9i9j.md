---
title: "Menunggu Goroutine Selesai dengan WaitGroup"
datePublished: Tue Apr 29 2025 01:53:41 GMT+0000 (Coordinated Universal Time)
cuid: cma1usl1p000909kz5ajv9i9j
slug: menunggu-goroutine-selesai-dengan-waitgroup
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745891333542/76313f92-3e41-4441-82ea-70ee2ed7d154.png
tags: go

---

Halo teman-teman! Kita ketemu lagi di seri **Golang Goroutine**.  
Setelah sebelumnya kita belajar tentang **Race Condition**, kali ini kita akan membahas **cara menunggu semua Goroutine selesai dengan benar** menggunakan **sync.WaitGroup**.

Ini topik penting supaya program kita **tidak berakhir sebelum semua goroutine selesai bekerja**.

---

## Masalah dengan `time.Sleep`

Sebelumnya, kita sering menggunakan `time.Sleep` seperti ini:

```go
time.Sleep(1 * time.Second)
```

Tujuannya supaya Goroutine selesai sebelum program berakhir. Tapi ini **bukan cara yang baik**, karena:

* Tidak presisi: kita tidak tahu pasti kapan semua goroutine selesai.
    
* Membuat program jadi **tidak efisien** (kadang menunggu terlalu lama, kadang kurang lama).
    
* Sulit diatur untuk program yang kompleks.
    

**Solusinya adalah WaitGroup**

---

## Apa Itu WaitGroup?

`sync.WaitGroup` adalah fitur dari package `sync` yang berguna untuk:

* **Mendaftarkan** jumlah goroutine yang harus ditunggu.
    
* **Menunggu** sampai semua goroutine selesai.
    

> Intinya, WaitGroup seperti "penjaga pintu" yang baru membolehkan program lanjut kalau semua pekerjaan sudah selesai.

---

## Cara Menggunakan WaitGroup

Berikut langkah-langkahnya:

1. **Tambah jumlah pekerjaan** dengan `Add(n)`.
    
2. **Panggil** `Done()` di setiap goroutine setelah selesai.
    
3. **Panggil** `Wait()` di program utama untuk menunggu semua selesai.
    

---

## Contoh Penggunaan WaitGroup

Langsung aja ke contoh unit test-nya:

```go
package goroutine_test

import (
	"fmt"
	"sync"
	"testing"
)

func RunTask(number int, wg *sync.WaitGroup) {
	defer wg.Done() // pastikan Done dipanggil saat fungsi selesai
	fmt.Println("Running task", number)
}

func TestWaitGroup(t *testing.T) {
	var wg sync.WaitGroup

	for i := 1; i <= 5; i++ {
		wg.Add(1) // Tambahkan 1 pekerjaan
		go RunTask(i, &wg)
	}

	wg.Wait() // Tunggu semua goroutine selesai
	fmt.Println("All tasks completed")
}
```

---

🧠 **Penjelasan:**

* `wg.Add(1)`: memberitahu WaitGroup ada **1 goroutine baru** yang harus ditunggu.
    
* `defer wg.Done()`: saat tugas selesai, goroutine ini memberitahu WaitGroup bahwa dia sudah selesai.
    
* `wg.Wait()`: program akan **berhenti di sini** dan **menunggu semua** goroutine yang terdaftar selesai.
    

Hasilnya lebih presisi, lebih aman, dan tidak perlu lagi tebak-tebakan `sleep` berapa lama!

---

## Catatan Penting

* **Selalu** pastikan `wg.Done()` dipanggil tepat satu kali untuk setiap `wg.Add(1)`.
    
* Biasanya kita pakai `defer wg.Done()` di dalam goroutine untuk menghindari lupa memanggilnya.
    
* `WaitGroup` **tidak boleh di-reuse** setelah semua pekerjaannya selesai.
    

---

## Kesimpulan

Hari ini kita sudah belajar:

* Masalah menggunakan `time.Sleep`
    
* Apa itu `sync.WaitGroup`
    
* Cara menggunakan WaitGroup dengan benar
    
* Contoh nyata menunggu goroutine selesai
    

Dengan `WaitGroup`, program kamu akan jadi **lebih profesional**, **presisi**, dan **lebih mudah dirawat**.

---

## Apa Selanjutnya?

Di **Golang Goroutine #5**, kita akan belajar:

* Apa itu **Buffered Channel**
    
* Bagaimana Buffered Channel bekerja
    
* Kapan sebaiknya menggunakan Buffered Channel untuk meningkatkan performa
    

Sampai ketemu di tutorial berikutnya ya! 🚀