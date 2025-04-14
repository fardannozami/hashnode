---
title: "🧠 Latihan Pointer di Golang"
datePublished: Mon Apr 14 2025 07:39:23 GMT+0000 (Coordinated Universal Time)
cuid: cm9grjdrh000409lb2y352qjs
slug: latihan-pointer-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744616332547/af503569-e870-46e8-aa6b-2dbd30af4bad.png
tags: go

---

### ✅ **Soal 1 - Dasar Pointer**

**Deskripsi:**  
Buat program yang:

1. Membuat variabel `angka` dengan nilai `5`
    
2. Menampilkan alamat dari variabel tersebut
    
3. Menyimpan alamatnya ke variabel pointer
    
4. Tampilkan nilai dari pointer
    

**Contoh Output:**

```plaintext
Alamat dari angka: 0xc000012034
Nilai dari pointer: 5
```

---

### ✅ **Soal 2 - Ubah Nilai Lewat Pointer**

**Deskripsi:**  
Buat fungsi `ubahNilai` yang menerima parameter pointer ke `int`. Fungsi ini akan mengubah nilai integer tersebut menjadi 100.  
Panggil fungsi itu dari `main()` dan tunjukkan bahwa nilai aslinya berubah.

---

### ✅ **Soal 3 - Pointer dengan Struct**

**Deskripsi:**  
Buat struct `Product` dengan field:

```go
type Product struct {
    Name string
    Price int
}
```

Lalu buat fungsi `UpdatePrice` yang menerima pointer ke `Product` dan mengubah harga produknya menjadi 50.  
Coba jalankan fungsi itu di `main()` dan cetak perubahan harganya.

---

### ✅ **Soal 4 - Nil Pointer**

**Deskripsi:**  
Buat pointer ke `string` tanpa menginisialisasinya (alias `nil`).  
Coba akses nilainya, dan amati apa yang terjadi.  
Lalu, gunakan `if` untuk mengecek apakah pointer tersebut `nil` sebelum mengaksesnya, agar tidak panic.

---

### ✅ **Soal 5 - Swap 2 Variabel**

**Deskripsi:**  
Buat fungsi `swap(a *int, b *int)` yang menukar isi dari dua variabel integer menggunakan pointer.  
Contoh:

```go
x := 10
y := 20
swap(&x, &y)
fmt.Println(x, y)  // Output: 20 10
```

---

## 🔁 Bonus Challenge - Pointer Array

**Deskripsi:**  
Buat array `[5]int`, lalu buat fungsi `ubahArray(arr *[5]int)` yang akan mengubah semua elemen array menjadi 1.  
Cetak array sebelum dan sesudah perubahan.

---