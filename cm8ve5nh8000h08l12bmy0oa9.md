---
title: "Function di Golang"
datePublished: Sun Mar 30 2025 08:41:38 GMT+0000 (Coordinated Universal Time)
cuid: cm8ve5nh8000h08l12bmy0oa9
slug: function-di-golang
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1743324353348/6b76c111-226a-4af1-9b00-31a83009aa4a.png
tags: go

---

Dalam pemrograman Golang, **function** digunakan untuk mengelompokkan kode agar lebih rapi dan bisa digunakan kembali (reusable). Function di Golang mirip seperti di bahasa pemrograman lain, tetapi memiliki beberapa aturan khas.

### **1\. Cara Mendeklarasikan Function di Golang**

Di Golang, function dideklarasikan menggunakan kata kunci `func`, diikuti dengan nama function, parameter (jika ada), tipe return (jika ada), dan isi function dalam `{}`.

**Contoh: Function tanpa parameter dan tanpa return value**

```go
package main

import "fmt"

// Mendeklarasikan function
func sayHello() {
    fmt.Println("Hello, Golang!")
}

func main() {
    // Memanggil function
    sayHello()
}
```

**Output:**

```plaintext
Hello, Golang!
```

---

### **2\. Function dengan Parameter**

Function juga bisa menerima parameter yang digunakan sebagai input.

**Contoh: Function dengan parameter**

```go
package main

import "fmt"

func greet(name string) {
    fmt.Println("Hello,", name)
}

func main() {
    greet("Budi")
}
```

**Output:**

```plaintext
Hello, Budi
```

---

### **3\. Function dengan Return Value**

Function bisa mengembalikan nilai menggunakan `return`.

**Contoh: Function dengan return value**

```go
package main

import "fmt"

func add(a int, b int) int {
    return a + b
}

func main() {
    result := add(3, 5)
    fmt.Println("Hasil penjumlahan:", result)
}
```

**Output:**

```plaintext
Hasil penjumlahan: 8
```

---

### **4\. Function dengan Multiple Return Values**

Golang memungkinkan function untuk mengembalikan lebih dari satu nilai.

**Contoh: Function dengan multiple return values**

```go
package main

import "fmt"

func calculate(a int, b int) (int, int) {
    sum := a + b
    product := a * b
    return sum, product
}

func main() {
    sum, product := calculate(4, 5)
    fmt.Println("Jumlah:", sum)
    fmt.Println("Perkalian:", product)
}
```

**Output:**

```plaintext
Jumlah: 9
Perkalian: 20
```

---

### **5\. Function dengan Named Return Values**

Di Golang, kita bisa memberikan nama pada return values untuk meningkatkan keterbacaan kode.

**Contoh: Function dengan named return values**

```go
package main

import "fmt"

func divide(a, b float64) (result float64, err string) {
    if b == 0 {
        err = "Error: Pembagian dengan nol!"
        return
    }
    result = a / b
    return
}

func main() {
    hasil, err := divide(10, 2)
    if err != "" {
        fmt.Println(err)
    } else {
        fmt.Println("Hasil pembagian:", hasil)
    }
}
```

**Output:**

```plaintext
Hasil pembagian: 5
```

---

### **6\. Function sebagai Parameter**

Function di Golang bisa diteruskan sebagai parameter ke function lain.

**Contoh: Function sebagai parameter**

```go
package main

import "fmt"

func operate(a int, b int, operation func(int, int) int) int {
    return operation(a, b)
}

func multiply(x int, y int) int {
    return x * y
}

func main() {
    result := operate(4, 5, multiply)
    fmt.Println("Hasil operasi:", result)
}
```

**Output:**

```plaintext
Hasil operasi: 20
```

---

### **7\. Function sebagai Return Value**

Golang juga memungkinkan function mengembalikan function lain.

**Contoh: Function mengembalikan function**

```go
package main

import "fmt"

func getMultiplier(factor int) func(int) int {
    return func(x int) int {
        return x * factor
    }
}

func main() {
    double := getMultiplier(2)
    fmt.Println("Hasil:", double(5)) // Output: 10
}
```

**Output:**

```plaintext
Hasil: 10
```

---

### **Kesimpulan**

1. **Function** digunakan untuk membungkus logika kode agar lebih modular dan reusable.
    
2. Function bisa memiliki **parameter** dan **return value**.
    
3. Function bisa mengembalikan lebih dari satu nilai (**multiple return values**).
    
4. Function bisa digunakan sebagai **parameter** atau **return value**.
    
5. Golang mendukung **named return values** untuk meningkatkan keterbacaan kode.
    

---

Itulah dasar-dasar function di Golang! 🚀 Selamat belajar