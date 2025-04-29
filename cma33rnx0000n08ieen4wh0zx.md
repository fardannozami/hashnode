---
title: "Memahami Channel Direction di Golang"
datePublished: Tue Apr 29 2025 22:52:41 GMT+0000 (Coordinated Universal Time)
cuid: cma33rnx0000n08ieen4wh0zx
slug: memahami-channel-direction-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745967093160/6a34d563-6247-4578-b24c-e0f0bb01649c.png
tags: go

---

Di seri sebelumnya, kita sudah belajar tentang **Select statement**. Kali ini kita akan bahas fitur keren lainnya di Golang: **Channel Direction**.

---

## Apa Itu Channel Direction?

Biasanya, channel di Go bisa digunakan untuk **mengirim** dan **menerima** data.  
Tapi, dengan **Channel Direction**, kita bisa membatasi channel hanya untuk:

* **Kirim data saja** (`chan<- Type`)
    
* **Terima data saja** (`<-chan Type`)
    

Ini membuat program kita:

* **Lebih aman** (mengurangi kesalahan penggunaan channel)
    
* **Lebih mudah dibaca** (jelas mana channel untuk kirim, mana untuk terima)
    

---

## Cara Menggunakan Channel Direction

### 1\. Channel Hanya untuk Mengirim (Send-only)

Deklarasi `chan<- Type` berarti channel hanya bisa untuk **mengirim** data.

```go
func SendOnly(ch chan<- string) {
	ch <- "Data dikirim"
}
```

✅ Fungsi `SendOnly` hanya bisa mengirim data ke `ch`, tidak bisa menerima.

---

### 2\. Channel Hanya untuk Menerima (Receive-only)

Deklarasi `<-chan Type` berarti channel hanya bisa untuk **menerima** data.

```go
func ReceiveOnly(ch <-chan string) {
	data := <-ch
	fmt.Println("Menerima:", data)
}
```

✅ Fungsi `ReceiveOnly` hanya bisa membaca data dari `ch`, tidak bisa mengirim.

---

## Contoh Lengkap Channel Direction

```go
package goroutine_test

import (
	"fmt"
	"testing"
)

// Fungsi hanya bisa mengirim data
func SendOnly(ch chan<- string) {
	ch <- "Hello from SendOnly"
}

// Fungsi hanya bisa menerima data
func ReceiveOnly(ch <-chan string) {
	data := <-ch
	fmt.Println("Received:", data)
}

func TestChannelDirection(t *testing.T) {
	ch := make(chan string)

	go SendOnly(ch)    // kirim data
	ReceiveOnly(ch)    // terima data
}
```

---

## 🧠 Kenapa Harus Pakai Channel Direction?

* **Mencegah Bug**:  
    Kalau kamu tidak ingin fungsi tertentu menerima data, cukup ubah channel-nya jadi `chan<-`.
    
* **Lebih Terstruktur**:  
    Channel direction membantu mengatur tanggung jawab setiap fungsi dengan jelas.
    
* **Lebih Aman Saat Refactor**:  
    Kalau project kamu besar, channel direction membantu mengurangi resiko kesalahan penggunaan channel di banyak tempat.
    

---

## Kesimpulan

Hari ini kita belajar:

* Cara membatasi channel supaya hanya **kirim** atau hanya **terima**.
    
* Channel direction membuat kode Go kita lebih **aman, bersih, dan jelas**.
    
* Menggunakan channel direction adalah praktik yang baik terutama dalam aplikasi yang kompleks.
    

---

## Apa Selanjutnya?

Di **Golang Goroutine #9**, kita akan bahas **Select dengan Timeout**:  
Bagaimana caranya agar `select` tidak menunggu selamanya, tapi ada batas waktunya! ⏰

---

Kalau mau, aku bisa sekalian buatkan **file** `.go` berisi semua contoh kode Channel Direction ini.  
Atau sekalian mau kita lanjut ke **Select Timeout**? 🚀  
(Mau sekalian buatkan daftar isi besar untuk seluruh Seri Golang Goroutine juga?) 📚🔥