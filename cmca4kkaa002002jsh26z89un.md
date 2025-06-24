---
title: "🎟️ Golang Ticket Booking System from Scratch – No Framework (Part 3)"
datePublished: Tue Jun 24 2025 06:08:57 GMT+0000 (Coordinated Universal Time)
cuid: cmca4kkaa002002jsh26z89un
slug: golang-ticket-booking-system-from-scratch-no-framework-part-3
tags: go

---

## 🧠 Booking Service & Race Condition Simulation

Di part 3 ini, kita akan **membuat service** untuk proses booking kursi, **menambahkan unit test**, dan melakukan **simulasi race condition** agar memastikan **1 kursi hanya bisa dibooking oleh 1 orang** meskipun ada banyak request bersamaan.

---

## 🔧 Struktur File Project

Untuk kamu yang mengikuti seri ini, kita sudah punya struktur seperti berikut:

```go
ticket-booking/
├── cmd/api/main.go              # HTTP server (nanti)
├── internal/
│   └── service/
│       └── booking_service.go   # BookingService kita buat sekarang
├── repository/
│   └── booking_repository.go
│   └── booking_repository_test.go
```

---

## 1\. Membuat BookingService

Kita akan buat interface `BookingService` dan implementasinya di `service/booking_service.go`.

### 🔨 File: `service/booking_service.go`

```go
package service

import (
	"context"
	"database/sql"
	"errors"

	"github.com/fardannozami/ticket-booking/helper"
	"github.com/fardannozami/ticket-booking/repository"
)

type BookingService interface {
	BookSeat(ctx context.Context, eventId, seatId, userId int) (int, error)
}

type bookingService struct {
	Db                *sql.DB
	BookingRepository repository.BookingRepository
}

func NewServiceRepository(db *sql.DB, bookingRepository repository.BookingRepository) BookingService {
	return &bookingService{
		Db:                db,
		BookingRepository: bookingRepository,
	}
}
```

### 💡 Penjelasan:

* `BookSeat` adalah fungsi utama untuk booking.
    
* Kita mulai dengan membuka transaksi, lalu:
    
    * Cek apakah seat masih `"AVAILABLE"`
        
    * Tandai seat sebagai `"BOOKED"`
        
    * Kurangi kuota event
        
    * Simpan data booking
        

### 🧠 Implementasi BookSeat

```go
func (service *bookingService) BookSeat(ctx context.Context, eventId int, seatId int, userId int) (int, error) {
	tx, err := service.Db.BeginTx(ctx, nil)
	helper.PanicIfError(err)

	defer func() {
		r := recover()
		if r != nil {
			tx.Rollback()
			panic(r)
		}
	}()

	status := service.BookingRepository.GetSeatStatus(ctx, tx, seatId)
	if status != "AVAILABLE" {
		tx.Rollback()
		return 0, errors.New("seat already booked")
	}

	service.BookingRepository.MarkSeatAsBooked(ctx, tx, seatId)
	service.BookingRepository.DecrementEventQuota(ctx, tx, eventId)
	id := service.BookingRepository.InsertBooking(ctx, tx, eventId, seatId, userId)

	return id, tx.Commit()
}
```

---

## 2\. Unit Test BookingService

### 🧪 File: `service/booking_repository_test.go`

```go
func TestBookSeat(t *testing.T) {
	inserUser(1, "Ajitama")
	repo := repository.NewBookingRepository()
	service := NewServiceRepository(db, repo)
	ctx := context.Background()

	id, err := service.BookSeat(ctx, 1, 2, 3)
	assert.NoError(t, err)

	var eventId, seatId, userId int
	err = db.QueryRowContext(ctx, "SELECT event_id, seat_id, user_id FROM bookings WHERE id = ?", id).Scan(&eventId, &seatId, &userId)

	assert.Equal(t, 1, eventId)
	assert.Equal(t, 2, seatId)
	assert.Equal(t, 3, userId)
}
```

### 💡 Penjelasan:

* Kita pastikan booking berhasil.
    
* Data di database sesuai input (eventId, seatId, userId).
    

---

## 3\. Simulasi Race Condition

Untuk memastikan hanya **1 user** yang bisa booking kursi walaupun ada **50 goroutine**, kita buat test berikut:

### 🔥 File: `service/booking_repository_test.go`

```go
func TestBookSeatRaceCondition(t *testing.T) {
	repo := repository.NewBookingRepository()
	service := NewBookingService(db, repo)
	ctx := context.Background()

	totalUser := 50
	for i := 1; i <= totalUser; i++ {
		inserUser(i, fmt.Sprintf("user %d", i))
	}

	var wg sync.WaitGroup
	type bookingResult struct {
		userId int
		err    error
	}

	result := make(chan bookingResult, totalUser)
	wg.Add(totalUser)

	for i := 1; i <= totalUser; i++ {
		go func(userId int) {
			defer wg.Done()
			_, err := service.BookSeat(ctx, 1, 2, userId)
			result <- bookingResult{userId: userId, err: err}
		}(i)
	}

	wg.Wait()
	close(result)

	var successUsers []int
	for res := range result {
		if res.err == nil {
			successUsers = append(successUsers, res.userId)
			fmt.Printf("[SUCCESS] User dengan ID %d berhasil booking seat\n", res.userId)
		} else {
			fmt.Printf("[FAILED] User dengan ID %d gagal booking seat\n", res.userId)
		}
	}

	assert.Equal(t, 1, len(successUsers), "Harusnya hanya satu user yang berhasil booking")
}
```

---

## 🧠 Kenapa Bisa Aman?

Karena:

* Kita menggunakan **transaksi database** (`BEGIN` – `COMMIT`)
    
* Kita **cek status kursi di awal transaksi**, dan hanya lanjut jika seat `"AVAILABLE"`
    
* Bila dua transaksi bersamaan, salah satu pasti gagal saat mencoba update
    

---

## ✅ Kesimpulan

* Kita telah berhasil membuat `BookingService` yang menggunakan transaksi SQL.
    
* Kita menambahkan **unit test** dan **race condition simulation**.
    
* Terbukti hanya **1 user yang berhasil booking** meskipun ada 50 goroutine serentak!
    

---