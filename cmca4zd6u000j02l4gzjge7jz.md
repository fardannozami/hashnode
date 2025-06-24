---
title: "🎬 Golang Ticket Booking System from Scratch – No Framework (Part 4)"
datePublished: Tue Jun 24 2025 06:20:28 GMT+0000 (Coordinated Universal Time)
cuid: cmca4zd6u000j02l4gzjge7jz
slug: golang-ticket-booking-system-from-scratch-no-framework-part-4
tags: go

---

## 🌐 Booking Controller & HTTP Handler (`main.go`)

---

Setelah kita berhasil membangun logic utama `BookingService` dan mengamankan sistem dari race condition di Part 3, sekarang saatnya membuat sistem ini bisa diakses melalui HTTP API. Di part ini, kita akan membahas:

* Pembuatan **Booking Controller** untuk menerima HTTP request
    
* Penyusunan **struct JSON** untuk request dan response
    
* Konfigurasi **HTTP server** menggunakan `httprouter`
    
* Contoh testing via **Postman** atau **Curl**
    

---

## 🔧 Struktur Proyek Saat Ini

```go
ticket-booking/
├── app/
│   └── database.go               # Koneksi database
├── controller/
│   └── booking_controller.go     # HTTP handler untuk booking
├── main.go                       # Entry point aplikasi
├── repository/
│   └── booking_repository.go     # Akses query ke DB
├── request/
│   └── booking_request.go        # Struct input request
├── response/
│   └── api_response.go           # Struct response standar
├── service/
│   └── booking_service.go        # Logika pemesanan kursi
├── go.mod
```

---

## 1\. Membuat Booking Controller

### 📄 File: `controller/booking_controller.go`

```go
package controller

import (
	"context"
	"encoding/json"
	"net/http"

	"github.com/fardannozami/ticket-booking/helper"
	"github.com/fardannozami/ticket-booking/request"
	"github.com/fardannozami/ticket-booking/response"
	"github.com/fardannozami/ticket-booking/service"
	"github.com/julienschmidt/httprouter"
)

type BookingController interface {
	BookSeat(writer http.ResponseWriter, req *http.Request, params httprouter.Params)
}

type bookingController struct {
	bookingService service.BookingService
}

func NewBookingController(bookingService service.BookingService) BookingController {
	return &bookingController{
		bookingService: bookingService,
	}
}

func (c *bookingController) BookSeat(writer http.ResponseWriter, req *http.Request, params httprouter.Params) {
	var bookCreateRequest request.BookingCreateRequest
	decoder := json.NewDecoder(req.Body)
	err := decoder.Decode(&bookCreateRequest)
	helper.PanicIfError(err)

	_, err = c.bookingService.BookSeat(context.Background(), bookCreateRequest.EventId, bookCreateRequest.SeatId, bookCreateRequest.UserId)
	helper.PanicIfError(err)

	apiResponse := response.ApiResponse{
		Code:    http.StatusCreated,
		Message: "created",
	}

	writer.Header().Set("Content-Type", "application/json")
	err = json.NewEncoder(writer).Encode(&apiResponse)
	helper.PanicIfError(err)

}
```

---

## 2\. Membuat Struct Request dan Response

### 📄 File: `request/booking_request.go`

```go
package request

type BookingCreateRequest struct {
	EventId int `json:"event_id"`
	SeatId  int `json:"seat_id"`
	UserId  int `json:"user_id"`
}
```

---

### 📄 File: `response/api_response.go`

```go
package response

type ApiResponse struct {
	Code    int         `json:"code"`
	Message string      `json:"message"`
	Data    interface{} `json:"data"`
}
```

---

## 3\. Setup Koneksi Database

### 📄 File: `app/database.go`

```go
package app

import (
	"database/sql"
	"github.com/fardannozami/ticket-booking/helper"
)

func NewSqlDb() *sql.DB {
	db, err := sql.Open("mysql", "root@tcp(127.0.0.1:3306)/ticket-booking")
	helper.PanicIfError(err)
	return db
}
```

---

## 4\. Tambahkan Exception Handler

### 📄 File: `exception/error_handler.go`

```go
package exception

import (
	"encoding/json"
	"net/http"

	"github.com/fardannozami/ticket-booking/helper"
	"github.com/fardannozami/ticket-booking/response"
)

func ErrorHandler(w http.ResponseWriter, r *http.Request, err interface{}) {
	internalServerError(w, r, err)
}

func internalServerError(w http.ResponseWriter, r *http.Request, err interface{}) {
	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusInternalServerError)

	var message string
	switch e := err.(type) {
	case string:
		message = e
	case error:
		message = e.Error()
	default:
		message = "Unknown error"
	}

	apiResponse := response.ApiResponse{
		Code:    http.StatusInternalServerError,
		Message: "Internal Server Error",
		Data:    message,
	}

	encode := json.NewEncoder(w)
	error := encode.Encode(&apiResponse)
	helper.PanicIfError(error)
}
```

## 5\. Menjalankan HTTP Server

### 📄 File: `main.go`

```go
package main

import (
	"net/http"

	"github.com/fardannozami/ticket-booking/app"
	"github.com/fardannozami/ticket-booking/controller"
	"github.com/fardannozami/ticket-booking/exception"
	"github.com/fardannozami/ticket-booking/helper"
	"github.com/fardannozami/ticket-booking/repository"
	"github.com/fardannozami/ticket-booking/service"
	_ "github.com/go-sql-driver/mysql"
	"github.com/julienschmidt/httprouter"
)

func main() {
	db := app.NewSqlDb()

	bookingRepo := repository.NewBookingRepository()
	bookingService := service.NewBookingService(db, bookingRepo)
	bookingController := controller.NewBookingController(bookingService)
	router := httprouter.New()

	router.POST("/api/bookseat", bookingController.BookSeat)

	router.PanicHandler = exception.ErrorHandler

	server := http.Server{
		Addr:    ":3000",
		Handler: router,
	}

	err := server.ListenAndServe()
	helper.PanicIfError(err)
}
```

---

## 🧪 Contoh Testing via Curl atau Postman

### 🔸 Request (Booking Kursi)

```bash
curl -X POST http://localhost:3000/api/bookseat \
  -H "Content-Type: application/json" \
  -d '{
    "event_id": 1,
    "seat_id": 2,
    "user_id": 12
  }'
```

### ✅ Response Berhasil

```json
{
  "code": 201,
  "message": "created",
  "data": null
}
```

### ❌ Response Gagal (kursi sudah dibooking)

```json
{
  "code": 409,
  "message": "seat already booked",
  "data": null
}
```

> 📌 Pastikan data `event_id` dan `seat_id` valid dan tersedia di database untuk testing ini.

---

## 📌 Summary

| Fitur | Status |
| --- | --- |
| BookingService | ✅ |
| Race Condition Handling | ✅ |
| JSON Request & Response | ✅ |
| HTTP Routing (`httprouter`) | ✅ |
| Endpoint Booking Seat | ✅ |

---

## 📘 Tips Tambahan

Jika kamu ingin meningkatkan proyek ini lebih jauh, pertimbangkan untuk:

* Menambahkan validasi input menggunakan `go-playground/validator`
    
* Menyimpan konfigurasi ke `.env` dan memuatnya menggunakan `joho/godotenv`
    
* Menambahkan middleware untuk logging atau recovery
    
* Menyusun error handling lebih rapi agar tidak semua pakai `PanicIfError`
    

---