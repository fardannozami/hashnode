---
title: "Struct dan Struct Method di Golang"
datePublished: Thu Apr 10 2025 07:15:34 GMT+0000 (Coordinated Universal Time)
cuid: cm9b0xcce002k09k1d0sbhluo
slug: struct-dan-struct-method-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744269203004/1fe0ff94-2413-4da6-a1d5-733da3a3b7df.png
tags: go

---

Halo teman-teman, kali ini kita bakal bahas salah satu fitur penting di Golang yang sering banget dipakai di dunia backend, yaitu **struct** dan **struct method**.

Kalau kamu udah pernah pakai OOP di bahasa lain kayak Java atau Python, kamu bisa nganggep struct itu kayak "class" versi Golang, tapi lebih ringan dan tanpa inheritance. Nah, yuk kita kupas satu-satu.

---

## Apa Itu Struct?

Struct adalah kumpulan dari beberapa field (atau properti) yang didefinisikan di dalam satu tipe data. Cocok banget buat ngegambarin entitas dalam aplikasi kamu. Misalnya, kita bikin struct `User`:

```go
package main

import "fmt"

type User struct {
    Name  string
    Email string
    Age   int
}

func main() {
    user1 := User{
        Name:  "Fardan",
        Email: "fardan@example.com",
        Age:   25,
    }

    fmt.Println(user1)
}
```

Output:

```plaintext
{Fardan fardan@example.com 25}
```

Struct ini kayak blueprint untuk objek. Kamu bisa bikin banyak user dari struct `User`.

---

## Access Field di Struct

Mau ambil data tertentu? Gampang:

```go
fmt.Println("Nama:", user1.Name)
fmt.Println("Email:", user1.Email)
```

---

## Struct Method: Bikin Struct Jadi Lebih Hidup

Struct method adalah function yang "nempel" ke struct tertentu. Jadi kayak method di dalam class.

Contoh: kita mau bikin method `IsAdult()` di struct `User`.

```go
func (u User) IsAdult() bool {
    return u.Age >= 18
}
```

Penjelasan:

* `(u User)` artinya kita bikin method yang receiver-nya bertipe `User`.
    
* Nama variabel `u` bebas, tapi biasanya huruf pertama dari nama struct.
    

Lalu di `main()`:

```go
fmt.Println("Apakah user dewasa?", user1.IsAdult())
```

---

## Bonus: Struct dalam Struct

Struct bisa juga di-nest. Misalnya:

```go
type Address struct {
    City    string
    Country string
}

type User struct {
    Name    string
    Email   string
    Age     int
    Address Address
}
```

Lalu di `main()`:

```go
user2 := User{
    Name:  "Ajitama",
    Email: "ajitama@example.com",
    Age:   30,
    Address: Address{
        City:    "Jakarta",
        Country: "Indonesia",
    },
}

fmt.Println("Kota user:", user2.Address.City)
```

---

## Kesimpulan

Struct dan struct method itu fondasi penting dalam pemrograman Go. Mereka menggantikan konsep class dan object-oriented programming dengan cara yang lebih simpel dan langsung. Setelah kamu ngerti ini, kamu bakal lebih pede bikin model untuk aplikasi, handle request, atau bahkan desain arsitektur REST API.

---

***Challege:***

* Coba bikin struct `Product` dan method `IsAvailable()` yang return `true` kalau stok &gt; 0