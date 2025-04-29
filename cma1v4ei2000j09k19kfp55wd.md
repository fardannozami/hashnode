---
title: "Memahami Buffered Channel di Golang"
datePublished: Tue Apr 29 2025 02:02:53 GMT+0000 (Coordinated Universal Time)
cuid: cma1v4ei2000j09k19kfp55wd
slug: memahami-buffered-channel-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745892007001/7d154243-1979-4099-beb3-748730674235.png
tags: go

---

Setelah kita belajar tentang **channel** dan **WaitGroup**, sekarang saatnya masuk ke topik yang lebih dalam: **Buffered Channel**.

Artikel ini cocok buat kamu yang:

* Ingin tahu perbedaan antara **unbuffered** dan **buffered channel**
    
* Penasaran **kapan** harus menggunakan buffered channel
    
* Ingin tahu contoh real-world untuk meningkatkan performa goroutine
    

Yuk kita mulai!

---

## Apa Itu Buffered Channel?

Secara default, channel di Go adalah **unbuffered**:  
Artinya, saat goroutine mengirim data ke channel, dia akan **menunggu** sampai ada goroutine lain yang **menerima** data tersebut.

Buffered channel adalah channel yang punya **ruang penyimpanan** (buffer).  
Goroutine bisa **mengirim data tanpa menunggu** langsung diterima, selama buffer-nya belum penuh.

### Perbandingan Singkat

| Tipe Channel | Perilaku |
| --- | --- |
| Unbuffered | Pengirim **menunggu** sampai penerima siap |
| Buffered | Pengirim **tidak menunggu**, selama buffer masih punya tempat kosong |

---

## Cara Membuat Buffered Channel

Gunakan fungsi `make` dengan kapasitas:

```go
ch := make(chan int, 3) // channel dengan kapasitas 3
```

Artinya, channel bisa menyimpan **3 nilai** tanpa perlu segera diterima.

---

## Contoh Penggunaan Buffered Channel

Yuk kita lihat contoh konkritnya:

```go
package goroutine_test

import (
	"fmt"
	"testing"
	"time"
)

func TestBufferedChannel(t *testing.T) {
	ch := make(chan string, 3) // membuat buffered channel dengan kapasitas 3
	defer close(ch)

	ch <- "one"
	ch <- "two"
	ch <- "three"

	// tidak error, karena buffer masih muat 3 data

	fmt.Println(<-ch)
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}
```

### Penjelasan:

* Channel bisa di-*isi* sampai 3 data tanpa ada yang menerima.
    
* Saat buffer penuh (lebih dari 3), goroutine akan **menunggu** sampai ada penerima.
    
* Kita `defer close(ch)` supaya channel tertutup dengan aman di akhir.
    

---

## Kenapa Buffered Channel Berguna?

Buffered channel cocok saat kamu:

* Ingin mengirim data ke goroutine **dengan cepat**
    
* Ingin mengurangi **blocking** antara pengirim dan penerima
    
* Butuh komunikasi **asinkron** antar-goroutine
    

---

## Contoh: Buffered Channel dengan Goroutine Producer-Consumer

```go
package goroutine_test

import (
	"fmt"
	"sync"
	"testing"
)

func TestBufferedChannelWithWaitGroup(t *testing.T) {
	ch := make(chan int, 5)
	defer close(ch)

	var wg sync.WaitGroup
	wg.Add(2) // kita punya 2 goroutine: producer dan consumer

	// Producer
	go func() {
		defer wg.Done()
		for i := 1; i <= 5; i++ {
			fmt.Println("Sending", i)
			ch <- i
		}
	}()

	// Consumer
	go func() {
		defer wg.Done()
		for i := 1; i <= 5; i++ {
			data := <-ch
			fmt.Println("Received", data)
		}
	}()

	wg.Wait()
	fmt.Println("All tasks completed")
}
```

### 🧠 Penjelasan:

* `ch := make(chan int, 5)`: buffered channel yang bisa menyimpan 5 item.
    
* `wg.Add(2)`: kita punya 2 goroutine (producer & consumer).
    
* `defer wg.Done()`: memastikan setiap goroutine memberi sinyal selesai.
    
* `wg.Wait()`: menunggu semua goroutine selesai.
    

Dengan ini:

* Program jadi **presisi** (tidak bergantung waktu).
    
* Cocok untuk skenario nyata di mana goroutine harus selesai dengan jelas.
    

---

## Kesimpulan

Hari ini kita sudah belajar:

* Apa itu buffered channel dan bagaimana cara membuatnya
    
* Perbedaan dengan unbuffered channel
    
* Kapan buffered channel berguna
    
* Contoh penggunaannya dalam model producer-consumer
    

Buffered channel membuat komunikasi antar goroutine jadi lebih fleksibel dan efisien!