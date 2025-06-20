---
title: "🎬 Golang Ticket Booking System from Scratch – No Framework (Part 1)"
datePublished: Fri Jun 20 2025 12:14:59 GMT+0000 (Coordinated Universal Time)
cuid: cmc4rvvgv002402l9bczd2gjj
slug: golang-ticket-booking-system-from-scratch-no-framework-part-1

---

---

Pada part pertama ini, kita akan memulai dari nol:

* Membuat struktur folder project
    
* Setup database MySQL
    
* Install dependency driver SQL
    
* Membuat repository awal
    
* Menulis unit test untuk function `GetSeatStatus`, termasuk test error case
    

---

### 🧱 Step 1: Inisialisasi Proyek

```bash
mkdir ticket-booking
cd ticket-booking
go mod init github.com/fardannozami/ticket-booking
```

---

### 🛢️ Step 2: Setup Database (MySQL)

Masuk ke MySQL dan jalankan query berikut:

```sql
CREATE DATABASE IF NOT EXISTS `ticket_booking`;
USE `ticket_booking`;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE events (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    quota INT NOT NULL
);

CREATE TABLE seats (
    id INT AUTO_INCREMENT PRIMARY KEY,
    event_id INT NOT NULL,
    seat_number VARCHAR(50) NOT NULL,
    status ENUM('available', 'booked') DEFAULT 'available',
    FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE CASCADE
);

CREATE TABLE bookings (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    event_id INT NOT NULL,
    seat_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE CASCADE,
    FOREIGN KEY (seat_id) REFERENCES seats(id) ON DELETE CASCADE
);
```

---

### 📦 Step 3: Install Dependency Driver MySQL

```bash
go get github.com/go-sql-driver/mysql
```

---

### 📁 Step 4: Struktur Folder dan Booking Repository

**Buat folder:**

```bash
mkdir repository
```

**File:** `repository/booking_repository.go`

```go
package repository

import (
	"context"
	"database/sql"

	"github.com/fardannozami/ticket-booking/helper"
)

type BookingRepository interface {
	GetSeatStatus(ctx context.Context, tx *sql.Tx, seatId int) string
}

type BookingRepositoryImpl struct{}

func NewBookingRepository() BookingRepository {
	return &BookingRepositoryImpl{}
}

func (repo *BookingRepositoryImpl) GetSeatStatus(ctx context.Context, tx *sql.Tx, seatId int) string {
	var status string
	SQL := `SELECT status FROM seats WHERE id = ?`
	err := tx.QueryRowContext(ctx, SQL, seatId).Scan(&status)
	helper.PanicIfError(err)

	return status
}
```

---

### 🛰️ Step 5: Unit Test untuk `GetSeatStatus`

sebelumnya install dependency untuk testing dulu

```go
go get github.com/stretchr/testify/assert
```

**File:** `repository/booking_repository_test.go`

```go
package repository

import (
	"context"
	"database/sql"
	"os"
	"testing"

	"github.com/fardannozami/ticket-booking/helper"
	"github.com/stretchr/testify/assert"

	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

func TestMain(m *testing.M) {
	var err error
	db, err = sql.Open("mysql", "root@tcp(127.0.0.1:3306)/ticket-booking?charset=utf8mb4&parseTime=True&loc=Local")
	helper.PanicIfError(err)

	exitCode := m.Run()
	db.Close()
	os.Exit(exitCode)
}

func insertEvent(id, quota int, name string) {
	SQL := `INSERT INTO events (id, quota, name) VALUES (?, ?, ?) ON DUPLICATE KEY UPDATE quota = VALUES(quota), name = VALUES(name)`
	_, err := db.Exec(SQL, id, quota, name)
	helper.PanicIfError(err)
}

func insertSeat(id, eventId int, status, seatNumber string) {
	SQL := `INSERT INTO seats (id, event_id, status, seat_number) VALUES (?, ?, ?, ?) ON DUPLICATE KEY UPDATE status = VALUES(status), seat_number = VALUES(seat_number)`
	_, err := db.Exec(SQL, id, eventId, status, seatNumber)
	helper.PanicIfError(err)
}

func TestGetSeatStatus(t *testing.T) {
	insertEvent(1, 100, "Event 1")
	insertSeat(9, 1, "AVAILABLE", "A1")
	repo := NewBookingRepository()
	ctx := context.Background()
	tx, err := db.BeginTx(ctx, nil)
	helper.PanicIfError(err)
	defer tx.Rollback()

	status := repo.GetSeatStatus(ctx, tx, 9)

	assert.Equal(t, "AVAILABLE", status)
}
```

---