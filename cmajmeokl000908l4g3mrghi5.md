---
title: "Golang Todolist CLI #3 – Menambahkan Command CLI Interaktif"
datePublished: Sun May 11 2025 12:18:47 GMT+0000 (Coordinated Universal Time)
cuid: cmajmeokl000908l4g3mrghi5
slug: golang-todolist-cli-3-menambahkan-command-cli-interaktif
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746718845840/86b1ffe0-674a-4572-95dc-2538e6550e79.png
tags: go

---

Halo selamat datang kembali di seri **Golang Todolist CLI** bersama Ajitama! 🎉  
Setelah sebelumnya kita membuat model `Task` dan repository-nya, sekarang saatnya membuat aplikasi kita bisa dikendalikan langsung dari terminal!

Di seri ini kita akan:

✅ Menambahkan command CLI: `add`, `list`, `delete`, dan `complete`  
✅ Menggunakan package `survey` sebagai framework CLI interaktif  
✅ Menyusun struktur file sesuai best practice

---

## 📦 Step 1 – Install Survey

Pertama, kita akan meng-install [survey](https://github.com/AlecAivazis/survey), package populer untuk membuat CLI interaktif di Golang:

```bash
go get github.com/AlecAivazis/survey/v2
```

---

## 🗂 Step 2 – Struktur Folder

Kita tambahkan folder `cli/` untuk menyimpan semua logic CLI:

```go
golang-todolist-cli/
├── internal/
│   └── cli/
│       └── interactive.go
└── main.go
```

---

## 🧠 Step 3 – Buat Interactive CLI

**File:** `internal/cli/interactive.go`

```go
package cli

import (
	"fmt"
	"os"
	"time"

	"github.com/AlecAivazis/survey/v2"
	"github.com/fardannozami/golang-todolist-cli/internal/model"
	"github.com/fardannozami/golang-todolist-cli/internal/repository"
)

type CLI struct {
	repo         repository.TaskRepository
	PromptRunner func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error
	ExitFunc     func(code int)
}

func NewCLI(repo repository.TaskRepository) *CLI {
	return &CLI{
		repo:         repo,
		PromptRunner: survey.AskOne,
		ExitFunc:     os.Exit,
	}
}

func (c *CLI) Run() {
	for {
		if !c.RunOnce() {
			break
		}
	}
}

func (c *CLI) RunOnce() bool {
	choice := ""
	prompt := &survey.Select{
		Message: "Apa yang ingin kamu lakukan?",
		Options: []string{"➕ Add Task", "📋 List Tasks", "✅ Complete Task", "🗑️ Delete Task", "🚪 Exit"},
	}
	c.PromptRunner(prompt, &choice)

	switch choice {
	case "➕ Add Task":
		c.addTask()
	case "📋 List Tasks":
		c.listTasks()
	case "✅ Complete Task":
		c.completeTask()
	case "🗑️ Delete Task":
		c.deleteTask()
	case "🚪 Exit":
		fmt.Println("Sampai jumpa 👋")
		c.ExitFunc(0)
		return false
	}
	return true
}

func (c *CLI) addTask() {
	var desc string
	prompt := &survey.Input{Message: "Masukkan deskripsi task:"}
	c.PromptRunner(prompt, &desc)

	if desc == "" {
		fmt.Println("❗ Deskripsi tidak boleh kosong")
		return
	}

	task := model.Task{
		Id:          time.Now().Nanosecond(),
		Description: desc,
		CreatedAt:   time.Now(),
	}
	c.repo.AddTask(task)
	fmt.Println("✅ Task berhasil ditambahkan!")
}

func (c *CLI) listTasks() {
	tasks, _ := c.repo.GetAllTasks()
	if len(tasks) == 0 {
		fmt.Println("📭 Tidak ada task.")
		return
	}
	fmt.Println("📋 Daftar Task:")
	for _, task := range tasks {
		status := "❌"
		if task.CompletedAt != nil {
			status = "✅"
		}
		fmt.Printf("[%s] #%d - %s\n", status, task.Id, task.Description)
	}
}

func (c *CLI) completeTask() {
	tasks, _ := c.repo.GetAllTasks()
	if len(tasks) == 0 {
		fmt.Println("📭 Tidak ada task.")
		return
	}

	options := []string{}
	taskMap := map[string]int{}
	for _, t := range tasks {
		label := fmt.Sprintf("#%d - %s", t.Id, t.Description)
		options = append(options, label)
		taskMap[label] = t.Id
	}

	var selected string
	prompt := &survey.Select{Message: "Pilih task yang ingin diselesaikan:", Options: options}
	c.PromptRunner(prompt, &selected)

	err := c.repo.MarkTaskAsCompleted(taskMap[selected])
	if err != nil {
		fmt.Println("❌", err)
	} else {
		fmt.Println("✅ Task selesai!")
	}
}

func (c *CLI) deleteTask() {
	tasks, _ := c.repo.GetAllTasks()
	if len(tasks) == 0 {
		fmt.Println("📭 Tidak ada task.")
		return
	}

	options := []string{}
	taskMap := map[string]int{}
	for _, t := range tasks {
		label := fmt.Sprintf("#%d - %s", t.Id, t.Description)
		options = append(options, label)
		taskMap[label] = t.Id
	}

	var selected string
	prompt := &survey.Select{Message: "Pilih task yang ingin dihapus:", Options: options}
	c.PromptRunner(prompt, &selected)

	err := c.repo.DeleteTask(taskMap[selected])
	if err != nil {
		fmt.Println("❌", err)
	} else {
		fmt.Println("🗑️ Task berhasil dihapus.")
	}
}
```

---

## 🧪 Step 4 – Unit Test

**File:** `internal/cli/interactive_test.go`

Kita akan menggunakan `testify` untuk mocking dan assertions:

```bash
go get github.com/stretchr/testify
```

Berikut kode pengujian CLI:

```go
package cli

import (
	"fmt"
	"testing"
	"time"

	"github.com/AlecAivazis/survey/v2"
	"github.com/fardannozami/golang-todolist-cli/internal/model"
	"github.com/stretchr/testify/assert"
	"github.com/stretchr/testify/mock"
)

// Mock repository untuk testing
type MockTaskRepository struct {
	mock.Mock
}

func (m *MockTaskRepository) AddTask(task model.Task) error {
	args := m.Called(task)
	return args.Error(0)
}

func (m *MockTaskRepository) GetAllTasks() ([]model.Task, error) {
	args := m.Called()
	return args.Get(0).([]model.Task), args.Error(1)
}

func (m *MockTaskRepository) DeleteTask(id int) error {
	args := m.Called(id)
	return args.Error(0)
}

func (m *MockTaskRepository) MarkTaskAsCompleted(id int) error {
	args := m.Called(id)
	return args.Error(0)
}

func TestNewCLI(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	cli := NewCLI(mockRepo)
	assert.NotNil(t, cli)
	assert.Equal(t, mockRepo, cli.repo)
}

func TestCLI_ListTasks(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	cli := NewCLI(mockRepo)

	t.Run("daftar kosong", func(t *testing.T) {
		mockRepo.On("GetAllTasks").Return([]model.Task{}, nil).Once()
		cli.listTasks()
		mockRepo.AssertExpectations(t)
	})

	t.Run("daftar berisi task", func(t *testing.T) {
		completedTime := time.Now()
		tasks := []model.Task{
			{Id: 1, Description: "Task 1", CreatedAt: time.Now()},
			{Id: 2, Description: "Task 2", CreatedAt: time.Now(), CompletedAt: &completedTime},
		}
		mockRepo.On("GetAllTasks").Return(tasks, nil).Once()
		cli.listTasks()
		mockRepo.AssertExpectations(t)
	})
}

func TestCLI_AddTask(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	mockRepo.On("AddTask", mock.AnythingOfType("model.Task")).Return(nil)

	c := NewCLI(mockRepo)
	c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
		*response.(*string) = "Belajar Golang"
		return nil
	}

	c.addTask()
	mockRepo.AssertCalled(t, "AddTask", mock.AnythingOfType("model.Task"))
}

func TestCLI_CompleteTask(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	tasks := []model.Task{
		{Id: 101, Description: "Task Testing", CreatedAt: time.Now()},
	}
	mockRepo.On("GetAllTasks").Return(tasks, nil)
	mockRepo.On("MarkTaskAsCompleted", 101).Return(nil)

	c := NewCLI(mockRepo)
	c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
		*response.(*string) = "#101 - Task Testing"
		return nil
	}

	c.completeTask()

	mockRepo.AssertCalled(t, "MarkTaskAsCompleted", 101)
	mockRepo.AssertExpectations(t)
}

func TestCLI_DeleteTask(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	tasks := []model.Task{
		{Id: 202, Description: "Task to delete", CreatedAt: time.Now()},
	}
	mockRepo.On("GetAllTasks").Return(tasks, nil)
	mockRepo.On("DeleteTask", 202).Return(nil)

	c := NewCLI(mockRepo)
	c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
		*response.(*string) = "#202 - Task to delete"
		return nil
	}

	c.deleteTask()

	mockRepo.AssertCalled(t, "DeleteTask", 202)
	mockRepo.AssertExpectations(t)
}

func TestCLI_ListTasks_Error(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	cli := NewCLI(mockRepo)

	mockRepo.On("GetAllTasks").Return([]model.Task{}, fmt.Errorf("database error")).Once()
	cli.listTasks()
	mockRepo.AssertExpectations(t)
}

func TestCLI_AddTask_EmptyDescription(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	c := NewCLI(mockRepo)
	c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
		*response.(*string) = ""
		return nil
	}

	c.addTask()
	mockRepo.AssertNotCalled(t, "AddTask")
}

func TestCLI_CompleteTask_Error(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	tasks := []model.Task{
		{Id: 101, Description: "Task Testing", CreatedAt: time.Now()},
	}
	mockRepo.On("GetAllTasks").Return(tasks, nil)
	mockRepo.On("MarkTaskAsCompleted", 101).Return(fmt.Errorf("failed to mark task as completed"))

	c := NewCLI(mockRepo)
	c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
		*response.(*string) = "#101 - Task Testing"
		return nil
	}

	c.completeTask()
	mockRepo.AssertExpectations(t)
}

func TestCLI_DeleteTask_Error(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	tasks := []model.Task{
		{Id: 202, Description: "Task to delete", CreatedAt: time.Now()},
	}
	mockRepo.On("GetAllTasks").Return(tasks, nil)
	mockRepo.On("DeleteTask", 202).Return(fmt.Errorf("failed to delete task"))

	c := NewCLI(mockRepo)
	c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
		*response.(*string) = "#202 - Task to delete"
		return nil
	}

	c.deleteTask()
	mockRepo.AssertExpectations(t)
}

func TestCLI_CompleteTask_EmptyList(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	mockRepo.On("GetAllTasks").Return([]model.Task{}, nil)

	c := NewCLI(mockRepo)
	c.completeTask()
	mockRepo.AssertExpectations(t)
}

func TestCLI_DeleteTask_EmptyList(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	mockRepo.On("GetAllTasks").Return([]model.Task{}, nil)

	c := NewCLI(mockRepo)
	c.deleteTask()
	mockRepo.AssertExpectations(t)
}

func TestCLI_RunOnce(t *testing.T) {
	t.Run("add task", func(t *testing.T) {
		mockRepo := new(MockTaskRepository)
		mockRepo.On("AddTask", mock.AnythingOfType("model.Task")).Return(nil).Once()

		c := NewCLI(mockRepo)
		var promptCount int
		c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
			if promptCount == 0 {
				*response.(*string) = "➕ Add Task"
			} else {
				*response.(*string) = "Test Task"
			}
			promptCount++
			return nil
		}

		shouldContinue := c.RunOnce()
		assert.True(t, shouldContinue)
		mockRepo.AssertExpectations(t)
	})

	t.Run("list tasks", func(t *testing.T) {
		mockRepo := new(MockTaskRepository)
		tasks := []model.Task{{Id: 1, Description: "Task 1", CreatedAt: time.Now()}}
		mockRepo.On("GetAllTasks").Return(tasks, nil).Once()

		c := NewCLI(mockRepo)
		c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
			*response.(*string) = "📋 List Tasks"
			return nil
		}

		shouldContinue := c.RunOnce()
		assert.True(t, shouldContinue)
		mockRepo.AssertExpectations(t)
	})

	t.Run("complete task", func(t *testing.T) {
		mockRepo := new(MockTaskRepository)
		tasks := []model.Task{{Id: 1, Description: "Task 1", CreatedAt: time.Now()}}
		mockRepo.On("GetAllTasks").Return(tasks, nil).Once()
		mockRepo.On("MarkTaskAsCompleted", 1).Return(nil).Once()

		c := NewCLI(mockRepo)
		var promptCount int
		c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
			if promptCount == 0 {
				*response.(*string) = "✅ Complete Task"
			} else {
				*response.(*string) = "#1 - Task 1"
			}
			promptCount++
			return nil
		}

		shouldContinue := c.RunOnce()
		assert.True(t, shouldContinue)
		mockRepo.AssertExpectations(t)
	})

	t.Run("delete task", func(t *testing.T) {
		mockRepo := new(MockTaskRepository)
		tasks := []model.Task{{Id: 1, Description: "Task 1", CreatedAt: time.Now()}}
		mockRepo.On("GetAllTasks").Return(tasks, nil).Once()
		mockRepo.On("DeleteTask", 1).Return(nil).Once()

		c := NewCLI(mockRepo)
		var promptCount int
		c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
			if promptCount == 0 {
				*response.(*string) = "🗑️ Delete Task"
			} else {
				*response.(*string) = "#1 - Task 1"
			}
			promptCount++
			return nil
		}

		shouldContinue := c.RunOnce()
		assert.True(t, shouldContinue)
		mockRepo.AssertExpectations(t)
	})

	t.Run("exit", func(t *testing.T) {
		mockRepo := new(MockTaskRepository)
		c := NewCLI(mockRepo)
		exitCalled := false
		c.ExitFunc = func(code int) {
			exitCalled = true
			assert.Equal(t, 0, code)
		}
		c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
			*response.(*string) = "🚪 Exit"
			return nil
		}

		shouldContinue := c.RunOnce()
		assert.False(t, shouldContinue)
		assert.True(t, exitCalled)
	})

	t.Run("prompt error", func(t *testing.T) {
		mockRepo := new(MockTaskRepository)
		c := NewCLI(mockRepo)
		c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
			return fmt.Errorf("prompt error")
		}

		shouldContinue := c.RunOnce()
		assert.True(t, shouldContinue)
	})
}

func TestCLI_Run(t *testing.T) {
	mockRepo := new(MockTaskRepository)
	c := NewCLI(mockRepo)

	var runCount int
	c.PromptRunner = func(p survey.Prompt, response interface{}, opts ...survey.AskOpt) error {
		if runCount == 0 {
			*response.(*string) = "📋 List Tasks"
		} else {
			*response.(*string) = "🚪 Exit"
		}
		runCount++
		return nil
	}

	mockRepo.On("GetAllTasks").Return([]model.Task{}, nil).Once()
	exitCalled := false
	c.ExitFunc = func(code int) {
		exitCalled = true
		assert.Equal(t, 0, code)
	}

	c.Run()
	assert.True(t, exitCalled)
	mockRepo.AssertExpectations(t)
}
```

jalankan test dengan perintah

```go
go test ./internal/cli -v
```

---

## 🚀 Step 5 – Jalankan Aplikasi

**File:** `main.go`

```go
package main

import (
	"github.com/fardannozami/golang-todolist-cli/internal/cli"
	"github.com/fardannozami/golang-todolist-cli/internal/repository"
)

func main() {
	repo := repository.NewInMemoryTaskRepository()
	app := cli.NewCLI(repo)
	app.Run()
}
```

Jalankan aplikasi:

```bash
go run main.go
```

---

---

---