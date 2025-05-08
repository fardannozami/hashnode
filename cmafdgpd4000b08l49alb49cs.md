---
title: "Golang Todolist CLI #2 – Membuat Model dan Repository Task"
datePublished: Thu May 08 2025 12:57:20 GMT+0000 (Coordinated Universal Time)
cuid: cmafdgpd4000b08l49alb49cs
slug: golang-todolist-cli-2-membuat-model-dan-repository-task
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746709000205/f98ddf40-06a9-4ad9-a8fa-bf836029c856.png
tags: go

---

Selamat datang kembali di seri **Golang Todolist CLI** bersama saya, Ajitama! 🎉  
Pada bagian sebelumnya, kita telah berhasil menginisialisasi proyek. Kali ini, kita akan mulai membuat struktur data dan lapisan penyimpanan data: **model** `Task` dan **repository-nya**.

---

## 📁 Struktur Folder

Sebelum kita mulai, mari kita perbaiki struktur proyek agar lebih idiomatik sesuai best practice Golang:

```plaintext
golang-todolist-cli/
├── go.mod
├── internal/
│   ├── model/
│   │   └── task.go
│   └── repository/
│       ├── task_repository.go
│       └── task_repository_test.go
```

Kita akan menyimpan kode domain aplikasi (seperti model dan repository) di dalam folder `internal/`, karena ini merupakan praktik umum untuk membatasi akses antar package.

---

## 1️⃣ Membuat Model `Task`

Pertama, kita buat struktur data `Task` di file `internal/model/task.go`:

```go
package model

import "time"

type Task struct {
	Id          int
	Description string
	CreatedAt   time.Time
	CompletedAt *time.Time
}
```

Struktur `Task` ini menyimpan informasi deskripsi tugas, waktu dibuat, dan waktu selesai (jika sudah diselesaikan).

---

## 2️⃣ Membuat Task Repository

Selanjutnya, kita buat repository untuk menyimpan dan mengelola data `Task`. Di sini, kita gunakan penyimpanan **in-memory** agar mudah dan ringan untuk tahap awal.

File: `internal/repository/task_repository.go`

```go
package repository

import (
	"fmt"
	"time"

	"github.com/fardannozami/golang-todolist-cli/internal/model"
)

type TaskRepository interface {
	AddTask(task model.Task) error
	GetAllTasks() ([]model.Task, error)
	DeleteTask(id int) error
	MarkTaskAsCompleted(id int) error
	GetNextId() int
}

type InMemoryTaskRepository struct {
	tasks []model.Task
}

func NewInMemoryTaskRepository() *InMemoryTaskRepository {
	return &InMemoryTaskRepository{
		tasks: make([]model.Task, 0),
	}
}

func (r *InMemoryTaskRepository) AddTask(task model.Task) error {
	r.tasks = append(r.tasks, task)
	return nil
}

func (r *InMemoryTaskRepository) GetAll() ([]model.Task, error) {
	taskCopy := make([]model.Task, len(r.tasks))
	copy(taskCopy, r.tasks)
	return taskCopy, nil
}

func (r *InMemoryTaskRepository) DeleteTaskById(id int) error {
	for i, task := range r.tasks {
		if task.Id == id {
			r.tasks = append(r.tasks[:i], r.tasks[i+1:]...)
			return nil
		}
	}
	return fmt.Errorf("task with id %d not found", id)
}

func (r *InMemoryTaskRepository) MarkTaskAsCompleted(id int) error {
	for i, task := range r.tasks {
		if task.Id == id {
			completedAt := time.Now()
			r.tasks[i].CompletedAt = &completedAt
			return nil
		}
	}
	return fmt.Errorf("task with id %d not found", id)
}

func (r *InMemoryTaskRepository) GetNextId() int {
	if len(r.tasks) == 0 {
		return 1
	}
	return r.tasks[len(r.tasks)-1].Id + 1
}
```

---

## 3️⃣ Menambahkan Unit Test untuk Repository

File: `internal/repository/task_repository_test.go`

Untuk memastikan repository kita bekerja dengan benar, kita buat serangkaian unit test menggunakan library `testify/assert`.

```go
package repository

import (
	"testing"
	"time"

	"github.com/fardannozami/golang-todolist-cli/internal/model"
	"github.com/stretchr/testify/assert"
)

var tasks = []model.Task{
	{
		Id:          1,
		Description: "Learn Golang",
		CreatedAt:   time.Now(),
	},
	{
		Id:          2,
		Description: "Write Unit Tests",
		CreatedAt:   time.Now(),
	},
}

func TestNewInMemoryTaskRepository(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	assert.NotNil(t, repo)
	assert.Empty(t, repo.tasks)
}

func TestInMemoryTaskRepository_AddTask(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	for _, task := range tasks {
		err := repo.AddTask(task)
		assert.NoError(t, err)
	}
	assert.Len(t, repo.tasks, len(tasks))
	assert.Equal(t, tasks, repo.tasks)
}

func TestInMemoryTaskRepository_GetAll(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	for _, task := range tasks {
		_ = repo.AddTask(task)
	}
	result, err := repo.GetAll()
	assert.NoError(t, err)
	assert.Len(t, result, len(tasks))
	assert.Equal(t, tasks, result)

	// Pastikan hasil GetAll merupakan salinan (bukan referensi langsung)
	result[0].Description = "Modified"
	assert.NotEqual(t, result[0].Description, repo.tasks[0].Description)
}

func TestInMemoryTaskRepository_DeleteTaskById(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	for _, task := range tasks {
		_ = repo.AddTask(task)
	}

	err := repo.DeleteTaskById(1)
	assert.NoError(t, err)
	assert.Len(t, repo.tasks, 1)
	assert.Equal(t, 2, repo.tasks[0].Id)

	err = repo.DeleteTaskById(999)
	assert.Error(t, err)
	assert.Contains(t, err.Error(), "task with id 999 not found")
}

func TestInMemoryTaskRepository_MarkTaskAsCompleted(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	for _, task := range tasks {
		_ = repo.AddTask(task)
	}

	err := repo.MarkTaskAsCompleted(1)
	assert.NoError(t, err)
	assert.NotNil(t, repo.tasks[0].CompletedAt)
	assert.Nil(t, repo.tasks[1].CompletedAt)

	err = repo.MarkTaskAsCompleted(999)
	assert.Error(t, err)
	assert.Contains(t, err.Error(), "task with id 999 not found")
}

func TestInMemoryTaskRepository_GetNextId(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	for _, task := range tasks {
		_ = repo.AddTask(task)
	}

	nextId := repo.GetNextId()
	assert.Equal(t, 3, nextId)
}

func TestInMemoryTaskRepository_MarkAlreadyCompletedTask(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	for _, task := range tasks {
		_ = repo.AddTask(task)
	}

	// Tandai task sebagai completed
	err := repo.MarkTaskAsCompleted(1)
	assert.NoError(t, err)
	completedTime := repo.tasks[0].CompletedAt

	// Coba tandai lagi task yang sudah completed
	err = repo.MarkTaskAsCompleted(1)
	assert.NoError(t, err)
	assert.Equal(t, completedTime, repo.tasks[0].CompletedAt)
}

func TestInMemoryTaskRepository_GetAllEmpty(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	result, err := repo.GetAll()
	assert.NoError(t, err)
	assert.Empty(t, result)
}

func TestInMemoryTaskRepository_GetNextIdEmpty(t *testing.T) {
	repo := NewInMemoryTaskRepository()
	nextId := repo.GetNextId()
	assert.Equal(t, 1, nextId)
}
```

Untuk menjalankan test, cukup gunakan perintah:

```bash
go test ./internal/repository
```

---

## ✅ Kesimpulan

Di seri ini kita telah:

* Membuat model `Task`
    
* Mengimplementasikan `InMemoryTaskRepository`
    
* Menambahkan unit test lengkap untuk setiap metode repository
    

Ini adalah pondasi penting sebelum kita masuk ke bagian interaktif (command line interface) di seri berikutnya.

---

Sampai jumpa di bagian ketiga! 🚀