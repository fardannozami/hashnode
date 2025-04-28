---
title: "Race Condition dan Cara Menghindarinya di Golang"
datePublished: Mon Apr 28 2025 06:36:03 GMT+0000 (Coordinated Universal Time)
cuid: cma0pfuxf00070aic5aane8sg
slug: race-condition-dan-cara-menghindarinya-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745822128263/215ddb9f-18aa-4500-a145-14d25e007651.png
tags: go

---

Halo teman-teman! Kembali lagi di seri **Golang Goroutine**.  
Di artikel sebelumnya, kita belajar tentang **Channel**. Kali ini, kita akan membahas sesuatu yang sangat penting saat bekerja dengan Goroutine, yaitu **Race Condition**.

Kita akan belajar:

* Apa itu Race Condition
    
* Kenapa Race Condition berbahaya
    
* Contoh nyata Race Condition
    
* Cara menghindari Race Condition di Golang
    

Yuk kita mulai!

---

## Apa Itu Race Condition?

**Race Condition** terjadi saat **dua atau lebih goroutine** mengakses **data yang sama** **secara bersamaan**, dan **minimal satu** dari mereka **mengubah data tersebut**.

Akibatnya, hasil akhirnya bisa **tidak terduga**.  
Kadang berhasil, kadang error, kadang nilainya aneh. Ini membuat program kita **tidak stabil** dan **sulit untuk debug**.

> Bayangkan dua orang yang berusaha menulis di papan tulis yang sama tanpa aturan. Bisa kacau, kan?

---

## Kenapa Race Condition Berbahaya?

* ❌ **Data menjadi korup** — hasil perhitungan bisa salah
    
* ❌ **Program sulit diprediksi** — bug yang jarang muncul dan sulit ditemukan
    
* ❌ **Aplikasi bisa crash**
    

Makanya, race condition adalah **musuh utama** saat kita membuat program konkuren.

---

## Contoh Race Condition di Golang

Mari kita lihat contoh nyata:

```go
package goroutine_test

import (
	"fmt"
	"testing"
	"time"
)

func TestRaceCondition(t *testing.T) {
	var counter int

	for i := 0; i < 1000; i++ {
		go func() {
			counter++
		}()
	}

	time.Sleep(1 * time.Second)
	fmt.Println("Counter:", counter)
}
```

🧠 **Penjelasan:**

* Kita membuat 1000 goroutine.
    
* Setiap goroutine menambahkan `counter++`.
    
* Kita berharap hasil akhirnya `1000`, kan?
    
* Tapi kalau kamu jalankan beberapa kali, hasilnya bisa `800`, `950`, bahkan `500`.
    

Itulah Race Condition: **data diubah bersamaan tanpa sinkronisasi**!

---

## Bagaimana Cara Menghindari Race Condition?

Ada beberapa cara, di antaranya:

### 1\. Menggunakan Mutex

**Mutex** (Mutual Exclusion) adalah alat untuk **mengunci** akses ke data sehingga hanya **satu goroutine** yang boleh mengakses pada satu waktu.

Contoh menggunakan `sync.Mutex`:

```go
package goroutine_test

import (
	"fmt"
	"sync"
	"testing"
	"time"
)

func TestRaceConditionFixed(t *testing.T) {
	var counter int
	var mutex sync.Mutex

	for i := 0; i < 1000; i++ {
		go func() {
			mutex.Lock()
			counter++
			mutex.Unlock()
		}()
	}

	time.Sleep(1 * time.Second)
	fmt.Println("Counter:", counter)
}
```

🧠 **Penjelasan:**

* `mutex.Lock()` mengunci akses sebelum `counter++`.
    
* `mutex.Unlock()` membuka kunci setelah perubahan selesai.
    
* Dengan ini, tidak ada dua goroutine yang bisa mengakses `counter` pada saat yang sama.
    

---

### 2\. Menggunakan Channel untuk Sinkronisasi

Cara lain yang lebih idiomatik di Go adalah menggunakan **Channel**.

```go
func TestRaceConditionWithChannel(t *testing.T) {
	var counter int
	channel := make(chan struct{}, 1) // Use a buffered channel

	for i := 0; i < 1000; i++ {
		go func() {
			channel <- struct{}{} // Send an empty struct to the channel
			counter++
			<-channel // Receive from the channel
		}()
	}

	time.Sleep(2 * time.Second)
	fmt.Println("Counter:", counter)
}
```

---

## 📢 Bonus: Cara Cek Race Condition Secara Otomatis

Go menyediakan **race detector** bawaan.  
Saat menjalankan `go test`, cukup tambahkan flag:

```bash
go test -race
```

Kalau ada race condition, Go akan otomatis mendeteksinya dan memberitahu kita! 🔥

---

## Kesimpulan

Hari ini kita sudah belajar:

* Apa itu Race Condition dan kenapa berbahaya
    
* Bagaimana Race Condition terjadi saat Goroutine saling berebut data
    
* Cara menghindari Race Condition:
    
    * Dengan **sync.Mutex**
        
    * Dengan **Channel**
        
* Cara cek Race Condition otomatis dengan `go test -race`
    

Dengan menghindari race condition, program kamu akan jadi **lebih stabil**, **lebih aman**, dan **lebih profesional**.

---

Sampai jumpa di tutorial berikutnya! 🚀