---
title: "Tipe Data Array, Slice, dan Map di Golang"
datePublished: Thu Mar 27 2025 20:44:12 GMT+0000 (Coordinated Universal Time)
cuid: cm8rtnb8b000108lhfhc78p7f
slug: tipe-data-array-slice-dan-map-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1743108231268/c3d8717d-b053-44e1-bff8-f1cb60e7eeb0.png
tags: go

---

Setelah sebelumnya kita sudah membahas tipe data string, boolean dan number, sekrang kita lanjutkan dengan  beberapa tipe data penting yang sering digunakan, yaitu **Array, Slice, dan Map**.

* **Array** digunakan untuk menyimpan sejumlah elemen dengan panjang tetap.
    
* **Slice** adalah versi fleksibel dari array yang dapat berubah ukurannya.
    
* **Map** adalah struktur data key-value yang memungkinkan penyimpanan dan pencarian data secara efisien.
    

Artikel ini akan membahas konsep dasar serta contoh implementasi dari masing-masing struktur data tersebut.

## 1\. Array di Golang

Array adalah kumpulan elemen dengan jumlah yang tetap dan tipe data yang sama.

### Contoh Penggunaan Array:

```go
package main
import "fmt"

func main() {
    var numbers [5]int // Deklarasi array dengan panjang 5
    numbers[0] = 10    // Mengisi elemen pertama dengan nilai 10
    fmt.Println(numbers)

    // Deklarasi dan inisialisasi langsung
    names := [3]string{"Andi", "Budi", "Citra"}
    fmt.Println(names)
}
```

**Karakteristik Array:**

* Panjangnya tetap.
    
* Tidak bisa diubah ukurannya setelah dideklarasikan.
    
* Harus memiliki elemen dengan tipe data yang sama.
    

## 2\. Slice di Golang

Slice adalah bagian dari array yang ukurannya bisa berubah secara dinamis.

### Contoh Penggunaan Slice:

```go
package main
import "fmt"

func main() {
    numbers := []int{1, 2, 3, 4, 5} // Slice tanpa panjang tetap
    fmt.Println(numbers)

    // Menambahkan elemen ke dalam slice
    numbers = append(numbers, 6, 7, 8)
    fmt.Println(numbers)

    // Membuat slice dari array
    arr := [5]int{10, 20, 30, 40, 50}
    slice := arr[1:4] // Memotong dari indeks 1 sampai sebelum indeks 4
    fmt.Println(slice)
}
```

**Karakteristik Slice:**

* Berbasis array tetapi lebih fleksibel.
    
* Bisa berubah ukuran menggunakan `append()`.
    
* Bisa diambil dari array atau dibuat langsung.
    

## 3\. Map di Golang

Map adalah struktur data key-value yang mirip dengan dictionary di Python atau objek di JavaScript.

### Contoh Penggunaan Map:

```go
package main
import "fmt"

func main() {
    person := make(map[string]string) // Deklarasi map
    person["name"] = "Alice"
    person["age"] = "25"
    fmt.Println(person)

    // Deklarasi dan inisialisasi langsung
    student := map[string]int{
        "John": 90,
        "Jane": 85,
        "Doe": 80,
    }
    fmt.Println(student)

    // Menghapus elemen dari map
    delete(student, "Doe")
    fmt.Println(student)
}
```

**Karakteristik Map:**

* Berisi pasangan key-value.
    
* Tidak memiliki urutan tetap.
    
* Bisa bertipe `map[KeyType]ValueType`.
    

## Kesimpulan

* **Array** memiliki ukuran tetap dan hanya bisa menyimpan tipe data yang sama.
    
* **Slice** lebih fleksibel dibanding array karena bisa bertambah ukurannya.
    
* **Map** digunakan untuk menyimpan data dalam bentuk key-value yang cepat untuk pencarian.
    

Itulah dasar penggunaan array, slice, dan map di Golang. Semoga bermanfaat!