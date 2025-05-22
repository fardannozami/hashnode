---
title: "Doctor Booking App – Part 2: Membuat Model dan Relasi Database"
datePublished: Thu May 22 2025 07:46:44 GMT+0000 (Coordinated Universal Time)
cuid: cmaz2j6xj000908kz2goobhl6
slug: doctor-booking-app-part-2-membuat-model-dan-relasi-database

---

Setelah menyelesaikan **setup awal** di Part 1, kali ini kita akan mulai membangun struktur data untuk aplikasi booking dokter. Kita akan membuat beberapa model penting dan mendefinisikan relasi antar tabel melalui migration.

## 1\. Generate Model & Migration

Pertama, buat beberapa model berikut lengkap dengan migration dan factory:

```bash
php artisan make:model Doctor -mf
php artisan make:model DoctorSchedule -mf
php artisan make:model Speciality -mf
php artisan make:model Patient -mf
php artisan make:model Appointment -mf
php artisan make:model Prescription -mf
```

## 2\. Update Struktur Tabel

### 🔸 Users Table

Tambahkan kolom `role` untuk membedakan antara admin, dokter, dan pasien.

```php
$table->enum('role', ['admin', 'doctor', 'patient'])->default('patient');
```

### 🔸 Specialities Table

```php
$table->string('name');
$table->string('description')->nullable();
$table->boolean('status')->default(true);
```

### 🔸 Doctors Table

```php
$table->string('name');
$table->foreignId('user_id')->constrained('users')->cascadeOnDelete();
$table->foreignId('speciality_id')->constrained('specialities')->cascadeOnDelete();
$table->text('bio')->nullable();
$table->integer('experience')->nullable();
$table->boolean('is_featured')->default(true); // typo diperbaiki dari text('is_featured('status)
```

### 🔸 Patients Table

```php
$table->string('name');
$table->foreignId('user_id')->constrained('users')->cascadeOnDelete();
$table->text('bio')->nullable();
```

### 🔸 Doctor Schedules Table

```php
$table->foreignId('doctor_id')->constrained('doctors')->cascadeOnDelete();
$table->integer('available_day')->nullable(); // bisa gunakan 0–6 (minggu–sabtu)
$table->time('from');
$table->time('to');
```

### 🔸 Appointments Table

```php
$table->foreignId('doctor_id')->constrained('doctors')->cascadeOnDelete();
$table->foreignId('patient_id')->constrained('patients')->cascadeOnDelete();
$table->date('appointment_date');
$table->time('appointment_time');
$table->enum('status', ['completed', 'in-complete'])->default('in-complete');
```

### 🔸 Prescriptions Table

```php
$table->foreignId('doctor_id')->constrained('doctors')->cascadeOnDelete();
$table->foreignId('patient_id')->constrained('patients')->cascadeOnDelete();
$table->string('medication_name');
$table->string('dosage');
$table->string('frequency');
$table->string('instructions')->nullable();
$table->date('start_date')->nullable();
$table->date('end_date')->nullable();
```

## 3\. Migrasi Database

Setelah semua migration sudah disesuaikan, jalankan:

```bash
php artisan migrate:fresh
```

## 4\. Tambahkan Relasi di Model

### 🔹 Doctor Model

```php
public function user()
{
    return $this->belongsTo(User::class);
}

public function patients()
{
    return $this->hasMany(Patient::class);
}

public function appointments()
{
    return $this->hasMany(Appointment::class);
}
```

### 🔹 DoctorSchedule Model

```php
public function doctor()
{
    return $this->belongsTo(Doctor::class);
}
```

### 🔹 Patient Model

```php
public function user()
{
    return $this->belongsTo(User::class);
}

public function appointments()
{
    return $this->hasMany(Appointment::class);
}
```

### 🔹 Prescription Model

```php
public function doctor()
{
    return $this->belongsTo(Doctor::class);
}

public function patient()
{
    return $this->belongsTo(Patient::class);
}
```

---

## Penutup

Sampai di sini kita sudah menyusun struktur database untuk aplikasi Doctor Booking lengkap dengan relasi antar modelnya. Di tahap selanjutnya, kita akan mulai membuat panel admin untuk mengelola data dokter dan spesialisasi menggunakan **Filament**.

> Pastikan untuk commit setiap perubahan besar di Git agar progress development tetap aman.

---