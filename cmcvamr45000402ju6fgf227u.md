---
title: "Swagger API Golang update | Step by Step Pakai Swaggo"
datePublished: Wed Jul 09 2025 01:41:47 GMT+0000 (Coordinated Universal Time)
cuid: cmcvamr45000402ju6fgf227u
slug: swagger-api-golang-step-by-step-pakai-swaggo

---

Swagger adalah tools dokumentasi API yang sangat membantu dalam menjelaskan dan mencoba endpoint secara interaktif. Di Golang, kita bisa menggunakan [swaggo/swag](https://github.com/swaggo/swag) untuk mengenerate dokumentasi dari komentar di kode.

---

### 1\. ✅ Install Swag CLI

Pertama-tama, install command-line tool `swag`:

```bash
go install github.com/swaggo/swag/cmd/swag@latest
```

> `swag` akan membaca komentar-komentar di atas handler untuk menghasilkan dokumentasi OpenAPI secara otomatis.

---

### 2\. 📝 Tambahkan Komentar Swagger di Atas Controller

Tambahkan komentar berformat `swag` di atas fungsi controller. Contoh pada endpoint `BookSeat`:

```go
// BookSeat godoc
// @Summary      Book Seat
// @Description  Book Event Seat
// @Tags         book
// @Accept       json
// @Produce      json
// @Param        request body request.BookingCreateRequest true "Book Seat"
// @Success      201 {object} response.SuccessResponse
// @Failure      400 {object} response.ErrorResponse
// @Router       /api/book-seat [post]
```

**Penjelasan per baris:**

* `@Summary`: Deskripsi singkat endpoint.
    
* `@Description`: Penjelasan lebih lengkap.
    
* `@Tags`: Kategori endpoint (bisa untuk gruping di UI Swagger).
    
* `@Accept/@Produce`: Format request/response.
    
* `@Param`: Parameter yang dikirim (body, path, query).
    
* `@Success/@Failure`: Response untuk status code tertentu.
    
* `@Router`: Jalur endpoint dan metode HTTP-nya.
    

---

### 3\. ⚙️ Jalankan `swag init`

Perintah ini akan membaca komentar `@...` dan menghasilkan dokumentasi di folder `docs/`:

```bash
swag init
```

Hasilnya:

* `docs/docs.go`
    
* `docs/swagger.json`
    
* `docs/swagger.yaml`
    

---

### 4\. 📦 Install HTTP Swagger Middleware

Tambahkan dependency `swaggo/http-swagger`:

```bash
go get github.com/swaggo/http-swagger
```

---

### 5\. 🔌 Setup Endpoint Swagger di Router

Karena kita menggunakan `httprouter`, dan `httpSwagger.WrapHandler` mengembalikan `http.Handler`, kita perlu adapter:

```go
import (
	"github.com/julienschmidt/httprouter"
	httpSwagger "github.com/swaggo/http-swagger"
	"net/http"
)

func adaptHandler(h http.Handler) httprouter.Handle {
	return func(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
		h.ServeHTTP(w, r)
	}
}

router.GET("/docs/*any", adaptHandler(httpSwagger.WrapHandler))
```

---

### 6\. 📄 Tambahkan Contoh (Example) di Struct Request & Response

Tambahkan tag `example` agar Swagger menampilkan contoh nilai.

```go
type BookingCreateRequest struct {
	EventId int `json:"event_id" example:"1"`
	SeatId  int `json:"seat_id" example:"2"`
	UserId  int `json:"user_id" example:"14"`
}

type SuccessResponse struct {
	Code    int    `json:"code" example:"201"`
	Message string `json:"message" example:"created"`
}

type ErrorResponse struct {
	Code    int    `json:"code" example:"400"`
	Message string `json:"message" example:"bad request"`
}
```

> Contoh di atas akan langsung terlihat di Swagger UI sebagai contoh payload.

---

### 7\. 🚀 Jalankan Aplikasi

Pastikan server Golang kamu jalan, lalu buka Swagger-nya:

```go
http://localhost:3000/docs/index.html
```

---

### 🧪 Hasil Akhir Swagger

Tampilan Swagger akan menampilkan endpoint `POST /api/book-seat` lengkap dengan:

* Deskripsi
    
* Contoh request
    
* Contoh response
    
* Tombol "Try it out" untuk testing langsung
    

---

### 📦 Rangkuman Struktur Folder

```go
project/
├── controller/
│   └── booking_controller.go  <-- komentar Swagger di sini
├── request/
│   └── booking_request.go     <-- BookingCreateRequest
├── response/
│   └── response.go            <-- SuccessResponse, ErrorResponse
├── docs/                      <-- hasil dari swag init
└── main.go
```

---

### 📝 Penutup

Dengan setup Swagger ini, dokumentasi API kamu jadi otomatis, rapi, dan bisa langsung dicoba. Cocok untuk tim besar atau saat kerja sama dengan frontend developer.