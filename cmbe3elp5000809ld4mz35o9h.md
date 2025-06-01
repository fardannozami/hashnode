---
title: "Golang Murni Bikin REST API? Gampang Banget, Nih Contohnya"
datePublished: Sun Jun 01 2025 20:07:42 GMT+0000 (Coordinated Universal Time)
cuid: cmbe3elp5000809ld4mz35o9h
slug: golang-murni-bikin-rest-api-gampang-banget-nih-contohnya
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748664188271/75be2cb6-4cfd-4b64-ac4b-b339450ab70e.png

---

Kadang orang mikir bikin REST API harus pake framework segede gaban. Padahal, pakai **Golang murni** juga bisa — ringan, cepat, dan kita punya kontrol penuh. Di tutorial ini, kita bakal bikin **REST API sederhana untuk habit tracker**. Yuk langsung gas!

---

## 🛠️ 1. Inisialisasi Proyek

Pertama, kita mulai dengan setup project Go-nya.

```bash
mkdir habit-tracker-api
cd habit-tracker-api
go mod init github.com/fardannozami/habit-tracker-api
```

---

## 🗃️ 2. Setup Database MySQL

Kita pake MySQL sebagai database. Jalankan perintah berikut di MySQL CLI:

```sql
CREATE DATABASE `habit-tracker-api`;
USE `habit-tracker-api`;

CREATE TABLE habits (
  id INT NOT NULL AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  description TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id)
);

CREATE TABLE habit_checks (
  habit_id INT NOT NULL,
  check_date DATE NOT NULL,
  FOREIGN KEY (habit_id) REFERENCES habits(id) ON DELETE CASCADE,
  CONSTRAINT unique_habit_check UNIQUE (habit_id, check_date)
);
```

## 📦 3. Install Dependency MySQL Driver

```bash
go get github.com/go-sql-driver/mysql
```

---

## 📁 4. Definisikan Model

Buat file `model/habit.go`:

```go
package model

import "time"

type Habit struct {
	ID          int       `json:"id"`
	Name        string    `json:"name"`
	Description string    `json:"description"`
	CreatedAt   time.Time `json:"created_at"`
}

type HabitCheck struct {
	HabitID   int       `json:"habit_id"`
	CheckDate time.Time `json:"check_date"`
}
```

---

## 📂 5. Buat Repository Pattern

Kenapa repository? Biar logic DB kita terpisah rapi dari business logic. Scalability dan testability lebih enak.

### ✅ Habit Repository

Buat file `repository/habit_repository.go`:

```go
package repository

import (
	"context"
	"database/sql"
	"time"

	"github.com/fardannozami/habit-tracker-api/helper"
	"github.com/fardannozami/habit-tracker-api/model"
)

type HabitRepository interface {
	GetAll(ctx context.Context, tx *sql.Tx) []model.Habit
	GetById(ctx context.Context, tx *sql.Tx, habitId int) model.Habit
	Create(ctx context.Context, tx *sql.Tx, habit model.Habit) model.Habit
	Update(ctx context.Context, tx *sql.Tx, habit model.Habit) model.Habit
	Delete(ctx context.Context, tx *sql.Tx, habit model.Habit)
}

type mysqlHabitRepository struct{}

func NewMysqlHabitRepository() HabitRepository {
	return &mysqlHabitRepository{}
}

func (r *mysqlHabitRepository) GetAll(ctx context.Context, tx *sql.Tx) []model.Habit {
	SQL := "SELECT id, name, description, created_at FROM habits"
	rows, err := tx.QueryContext(ctx, SQL)
	helper.PanicIfError(err)

	defer rows.Close()

	var habits []model.Habit
	for rows.Next() {
		habit := model.Habit{}
		err := rows.Scan(&habit.ID, &habit.Name, &habit.Description, &habit.CreatedAt)
		helper.PanicIfError(err)

		habits = append(habits, habit)
	}

	return habits
}

func (r *mysqlHabitRepository) GetById(ctx context.Context, tx *sql.Tx, habitId int) model.Habit {
	var habit model.Habit

	SQL := "SELECT id, name, description, created_at FROM habits WHERE id = ?"
	err := tx.QueryRowContext(ctx, SQL, habitId).Scan(&habit.ID, &habit.Name, &habit.Description, &habit.CreatedAt)
	helper.PanicIfError(err)

	return habit
}

func (r *mysqlHabitRepository) Create(ctx context.Context, tx *sql.Tx, habit model.Habit) model.Habit {
	habit.CreatedAt = time.Now()

	SQL := "INSERT INTO habits(name, description, created_at) VALUES(?, ?, ?)"
	result, err := tx.ExecContext(ctx, SQL, habit.Name, habit.Description, habit.CreatedAt)
	helper.PanicIfError(err)

	id, err := result.LastInsertId()
	helper.PanicIfError(err)

	habit.ID = int(id)

	return habit
}

func (r *mysqlHabitRepository) Update(ctx context.Context, tx *sql.Tx, habit model.Habit) model.Habit {
	SQL := "UPDATE habits SET name = ?, description = ? WHERE id = ?"
	_, err := tx.ExecContext(ctx, SQL, habit.Name, habit.Description, habit.ID)
	helper.PanicIfError(err)

	return habit
}

func (r *mysqlHabitRepository) Delete(ctx context.Context, tx *sql.Tx, habit model.Habit) {
	SQL := "DELETE FROM habits WHERE id = ?"
	_, err := tx.ExecContext(ctx, SQL, habit.ID)
	helper.PanicIfError(err)
}
```

> 🔧 Semua operasi menggunakan transaction (`*sql.Tx`) agar mudah dikontrol dari service layer, apalagi buat rollback kalau error.

---

## 🧠 Best Practice Note

* ✅ Gunakan context agar setiap query aware terhadap cancellation atau timeout dari request.
    
* ✅ Bungkus semua query dalam transaction biar atomic.
    
* ✅ Pisahkan `model`, `repository`, `service`, dan `handler` agar kode lebih mudah diuji dan dimaintain.
    
* ❌ Hindari SQL hardcoded di seluruh tempat — simpan di layer repository saja.
    

---

---

## 🔧 6. Bangun Service Layer: `habit_service`

Service layer ini jadi penghubung antara controller (handler HTTP) dengan repository. Di sini kita bisa validasi request, handle transaction, dan atur alur bisnis.  
  
Sebelum buat service kita install dulu dependency untuk validasinya

```go
go get github.com/go-playground/validator/v10
```

### Interface `HabitService`

```go
package service

import (
	"context"
	"database/sql"

	"github.com/go-playground/validator/v10"
	"github.com/fardannozami/habit-tracker-api/model"
	"github.com/fardannozami/habit-tracker-api/repository"
	"github.com/fardannozami/habit-tracker-api/request"
	"github.com/fardannozami/habit-tracker-api/response"
	"github.com/fardannozami/habit-tracker-api/helper"
)

type HabitService interface {
	GetAll(ctx context.Context) []response.HabitResponse
	GetById(ctx context.Context, habitId int) response.HabitResponse
	Create(ctx context.Context, request request.HabitCreateRequest) response.HabitResponse
	Update(ctx context.Context, request request.HabitUpdateRequest) response.HabitResponse
	Delete(ctx context.Context, habitId int)
}
```

### Implementasi `habitService`

```go
type habitService struct {
	habitRepository repository.HabitRepository
	dB              *sql.DB
	validate        *validator.Validate
}

func NewHabitService(habitRepository repository.HabitRepository, db *sql.DB, validate *validator.Validate) HabitService {
	return &habitService{
		habitRepository: habitRepository,
		dB:              db,
		validate:        validate,
	}
}

func (s *habitService) GetAll(ctx context.Context) []response.HabitResponse {
	tx, err := s.dB.Begin()
	helper.PanicIfError(err)

	defer helper.CommitOrRollback(tx)

	habits := s.habitRepository.GetAll(ctx, tx)

	return helper.ToHabitResponses(habits)
}

func (s *habitService) GetById(ctx context.Context, habitId int) response.HabitResponse {
	tx, err := s.dB.Begin()
	helper.PanicIfError(err)

	defer helper.CommitOrRollback(tx)

	habit := s.habitRepository.GetById(ctx, tx, habitId)

	return helper.ToHabitResponse(habit)

}

func (s *habitService) Create(ctx context.Context, request request.HabitCreateRequest) response.HabitResponse {
	var habitResponse response.HabitResponse
	err := s.validate.Struct(request)
	helper.PanicIfError(err)

	tx, err := s.dB.Begin()
	helper.PanicIfError(err)

	habit := model.Habit{Name: request.Name, Description: request.Description}
	defer helper.CommitOrRollback(tx)

	savedHabit := s.habitRepository.Create(ctx, tx, habit)

	habitResponse = helper.ToHabitResponse(savedHabit)

	return habitResponse
}

func (s *habitService) Update(ctx context.Context, request request.HabitUpdateRequest) response.HabitResponse {
	err := s.validate.Struct(request)
	helper.PanicIfError(err)

	tx, err := s.dB.Begin()
	helper.PanicIfError(err)

	defer helper.CommitOrRollback(tx)

	habit := s.habitRepository.GetById(ctx, tx, request.ID)

	habit.Name = request.Name
	habit.Description = request.Description

	habit = s.habitRepository.Update(ctx, tx, habit)

	return helper.ToHabitResponse(habit)
}

func (s *habitService) Delete(ctx context.Context, habitId int) {
	tx, err := s.dB.Begin()
	helper.PanicIfError(err)

	defer helper.CommitOrRollback(tx)

	habit := s.habitRepository.GetById(ctx, tx, habitId)

	s.habitRepository.Delete(ctx, tx, habit)
}
```

> Semua method-nya pakai transaction (dari `sql.DB`) dan konversi response pakai helper. Struktur ini sangat fleksibel buat testing juga.

---

## 🧰 7. Buat Helper Utility

File `helper/helper.go`:

```go
package helper

import (
	"database/sql"

	"github.com/fardannozami/habit-tracker-api/model"
	"github.com/fardannozami/habit-tracker-api/response"
)

func PanicIfError(err error) {
	if err != nil {
		panic(err.Error())
	}
}

func CommitOrRollback(tx *sql.Tx) {
	err := recover()
	if err != nil {
		errRollback := tx.Rollback()
		PanicIfError(errRollback)
		panic(err)
	} else {
		errCommit := tx.Commit()
		PanicIfError(errCommit)
	}
}

func ToHabitResponse(habit model.Habit) response.HabitResponse {
	return response.HabitResponse{
		ID:          habit.ID,
		Name:        habit.Name,
		Description: habit.Description,
	}
}

func ToHabitResponses(habits []model.Habit) []response.HabitResponse {
	var responses []response.HabitResponse
	for _, habit := range habits {
		responses = append(responses, ToHabitResponse(habit))
	}
	return responses
}
```

> 🔥 Kenapa pakai `recover()` di `CommitOrRollback`? Ini supaya kita bisa handle panic dari dalam service (misal error validasi atau SQL), lalu rollback otomatis.

---

## 📨 8. Request & Response DTO

Simpan di folder `request/` dan `response/`.

### `request/habit_request.go`

```go
package request

type HabitCreateRequest struct {
	Name        string `json:"name" validate:"required"`
	Description string `json:"description" validate:"required"`
}

type HabitUpdateRequest struct {
	ID          int    `json:"id" validate:"required"`
	Name        string `json:"name" validate:"required"`
	Description string `json:"description" validate:"required"`
}
```

### `response/habit_response.go`

```go
package response

type HabitResponse struct {
	ID          int    `json:"id"`
	Name        string `json:"name"`
	Description string `json:"description"`
}
```

---

## 🧠 Best Practice Insight

* ✅ **Validation di service layer**: biar centralized dan bisa custom behavior-nya.
    
* ✅ **Pemisahan DTO dan model**: model mewakili struktur database, sedangkan DTO (request/response) mewakili struktur API.
    
* ✅ **Transaction-per-request**: bikin semua operasi atomic, terutama kalau nanti nambah fitur kompleks (multiple insert/update dalam 1 request).
    
* 🔒 **Error handling** pakai panic + recover cukup oke untuk skala kecil, tapi nanti kita bisa refactor pakai custom error.
    

---

## 📦 9. Bangun HTTP Controller: `habit_controller.go`

### Interface dan Struct

```go
type HabitController interface {
	Create(http.ResponseWriter, *http.Request, httprouter.Params)
	Update(http.ResponseWriter, *http.Request, httprouter.Params)
	Delete(http.ResponseWriter, *http.Request, httprouter.Params)
	GetAll(http.ResponseWriter, *http.Request, httprouter.Params)
	GetById(http.ResponseWriter, *http.Request, httprouter.Params)
}

type habitController struct {
	habitService service.HabitService
}

func NewHabitController(habitService service.HabitService) HabitController {
	return &habitController{habitService: habitService}
}
```

---

## ⚙️ Handler Implementasi

### Create

```go
func (c *habitController) Create(w http.ResponseWriter, req *http.Request, _ httprouter.Params) {
	var habitCreateRequest request.HabitCreateRequest
	err := json.NewDecoder(req.Body).Decode(&habitCreateRequest)
	helper.PanicIfError(err)

	habit := c.habitService.Create(req.Context(), habitCreateRequest)

	writeJsonResponse(w, http.StatusCreated, "created", habit)
}
```

### Update

```go
func (c *habitController) Update(w http.ResponseWriter, req *http.Request, params httprouter.Params) {
	var habitUpdateRequest request.HabitUpdateRequest
	err := json.NewDecoder(req.Body).Decode(&habitUpdateRequest)
	helper.PanicIfError(err)

	habitId, err := strconv.Atoi(params.ByName("id"))
	helper.PanicIfError(err)
	habitUpdateRequest.ID = habitId

	habit := c.habitService.Update(req.Context(), habitUpdateRequest)

	writeJsonResponse(w, http.StatusOK, "success", habit)
}
```

### Delete

```go
func (c *habitController) Delete(w http.ResponseWriter, req *http.Request, params httprouter.Params) {
	habitId, err := strconv.Atoi(params.ByName("id"))
	helper.PanicIfError(err)

	c.habitService.Delete(req.Context(), habitId)

	writeJsonResponse(w, http.StatusOK, "success", nil)
}
```

### GetAll

```go
func (c *habitController) GetAll(w http.ResponseWriter, req *http.Request, _ httprouter.Params) {
	habits := c.habitService.GetAll(req.Context())
	writeJsonResponse(w, http.StatusOK, "success", habits)
}
```

### GetById

```go
func (c *habitController) GetById(w http.ResponseWriter, req *http.Request, params httprouter.Params) {
	habitId, err := strconv.Atoi(params.ByName("id"))
	helper.PanicIfError(err)

	habit := c.habitService.GetById(req.Context(), habitId)
	writeJsonResponse(w, http.StatusOK, "success", habit)
}
```

---

## 📤 10. API Response Wrapper

Simpan di `response/api_response.go`:

```go
package response

type ApiResponse struct {
	Code    int         `json:"code"`
	Message string      `json:"message"`
	Data    interface{} `json:"data,omitempty"`
}
```

Tambahkan helper untuk response writer di `helper/json_response.go`:

```go
package helper

import (
	"encoding/json"
	"net/http"

	"github.com/fardannozami/habit-tracker-api/response"
)

func writeJsonResponse(w http.ResponseWriter, code int, message string, data interface{}) {
	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(code)

	apiResponse := response.ApiResponse{
		Code:    code,
		Message: message,
		Data:    data,
	}

	err := json.NewEncoder(w).Encode(apiResponse)
	PanicIfError(err)
}
```

> 🎯 Ini bikin respons kamu konsisten, DRY, dan siap untuk error handling juga nanti.

---

## ✅ Summary

Controller ini:

* Fokus pada parsing dan routing request.
    
* Delegasi semua logic ke service layer.
    
* Mengembalikan JSON response yang konsisten.
    
* Pakai `httprouter` untuk routing yang performa tinggi.
    

---

---

## 🛣️ 11. Setup Routing

### File: `router/habit_router.go`

```go
package router

import (
	"github.com/fardannozami/habit-tracker-api/controller"
	"github.com/julienschmidt/httprouter"
)

func HabitRoutes(router *httprouter.Router, habitController controller.HabitController) {
	router.GET("/api/habits", habitController.GetAll)
	router.GET("/api/habits/:id", habitController.GetById)
	router.POST("/api/habits", habitController.Create)
	router.PUT("/api/habits/:id", habitController.Update)
	router.DELETE("/api/habits/:id", habitController.Delete)
}
```

---

## 🧠 12. `main.go` – Setup Seluruh Komponen & Jalankan Server

```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"os"
	"os/signal"
	"time"

	"github.com/go-playground/validator/v10"
	"github.com/julienschmidt/httprouter"

	"github.com/fardannozami/habit-tracker-api/app"
	"github.com/fardannozami/habit-tracker-api/controller"
	"github.com/fardannozami/habit-tracker-api/helper"
	"github.com/fardannozami/habit-tracker-api/repository"
	"github.com/fardannozami/habit-tracker-api/router"
	"github.com/fardannozami/habit-tracker-api/service"
)

func main() {
	validate := validator.New()

	db, err := app.NewMySqlDB()
	helper.PanicIfError(err)

	// Repository
	habitRepository := repository.NewMysqlHabitRepository()
	habitCheckRepository := repository.NewHabitCheckRepository()

	// Service
	habitService := service.NewHabitService(habitRepository, db, validate)
	habitCheckService := service.NewHabitCheckService(db, habitCheckRepository, habitRepository, validate)

	// Controller
	habitController := controller.NewHabitController(habitService)
	habitCheckController := controller.NewHabitCheckController(habitCheckService)

	// Router
	r := httprouter.New()
	router.HabitRoutes(r, habitController)
	router.HabitCheckRoutes(r, habitCheckController)

	port := os.Getenv("PORT")
	if port == "" {
		port = "3000"
	}

	server := &http.Server{
		Addr:    ":" + port,
		Handler: r,
	}

	// Jalankan server di goroutine
	go func() {
		fmt.Println("🚀 Server running on http://localhost:" + port)
		helper.PanicIfError(server.ListenAndServe())
	}()

	// Graceful shutdown
	quit := make(chan os.Signal, 1)
	signal.Notify(quit, os.Interrupt)
	<-quit

	fmt.Println("\n🛑 Server shutting down...")
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()

	if err := server.Shutdown(ctx); err != nil {
		fmt.Printf("❌ Server forced to shutdown: %s\n", err)
	} else {
		fmt.Println("✅ Server exited gracefully")
	}
}
```

---

## 🔌 13. Koneksi ke Database

### File: `app/database.go`

```go
package app

import (
	"database/sql"
	"fmt"
	"time"

	_ "github.com/go-sql-driver/mysql"
)

func NewMySqlDB() (*sql.DB, error) {
	dsn := "root:@tcp(127.0.0.1:3306)/golang-restful-api?parseTime=true"
	db, err := sql.Open("mysql", dsn)
	if err != nil {
		return nil, fmt.Errorf("failed to open DB: %w", err)
	}

	if err := db.Ping(); err != nil {
		return nil, fmt.Errorf("failed to connect to DB: %w", err)
	}

	db.SetMaxIdleConns(5)
	db.SetMaxOpenConns(20)
	db.SetConnMaxLifetime(60 * time.Minute)
	db.SetConnMaxIdleTime(10 * time.Minute)

	return db, nil
}
```

---

## 🧪 14. Tes Jalankan Server

Pastikan:

* File `go.mod` telah mengimpor semua dependency seperti [`github.com/go-sql-driver/mysql`](http://github.com/go-sql-driver/mysql), [`github.com/julienschmidt/httprouter`](http://github.com/julienschmidt/httprouter), [`github.com/go-playground/validator/v10`](http://github.com/go-playground/validator/v10), dll.
    
* Database `golang-restful-api` dan tabel `habits` sudah tersedia.
    

Lalu jalankan:

```bash
go run main.go
```

Jika berhasil, akan muncul:

```go
🚀 Server running on http://localhost:3000
```

---