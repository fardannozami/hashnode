---
title: "Claim Management System - Setup Project & Database Design (Laravel 12 + MySQL)"
datePublished: Mon Dec 15 2025 08:42:51 GMT+0000 (Coordinated Universal Time)
cuid: cmj6wop78000902lg6xm5hpo0
slug: claim-management-system-setup-project-and-database-design-laravel-12-mysql
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1765788145638/5f54e75d-4b4d-48a1-a6a4-34992700c5d9.png
tags: laravel, filament

---

Di Part 1 kita sudah membahas **business flow dan konsep sistem**.  
Sekarang di Part 2, kita **langsung eksekusi**: bikin project Laravel, setup database, lalu define **model & migration** yang akan jadi fondasi Claim Management System.

Stack yang digunakan:

* **Laravel 12.11.0**
    
* **MySQL**
    

---

## 1️⃣ Membuat Project Laravel

Kita mulai dari Laravel Installer:

```bash
laravel new claim-management-system
```

Pada saat penulisan ini:

* Laravel version: **12.11.0**
    
* Database: **MySQL**
    

Pastikan konfigurasi database di `.env` sudah benar:

```plaintext
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=claim_management_system
DB_USERNAME=root
DB_PASSWORD=
```

---

## 2️⃣ Konsep Struktur User & Role

Dalam sistem ini:

* **User** adalah entitas autentikasi
    
* Role bisnis dipisahkan ke tabel domain:
    
    * Head Office
        
    * Distributor
        
    * Retail (Seller)
        

Kenapa?

* Lebih fleksibel
    
* Lebih aman untuk audit
    
* Tidak mencampur domain logic ke tabel `users`
    

---

## 3️⃣ Head Office (Produsen)

### Generate model & migration

```bash
php artisan make:model HeadOffice -m
```

### Migration: `head_offices`

```php
Schema::create('head_offices', function (Blueprint $table) {
    $table->id();
    $table->string('name');

    // user yang merepresentasikan HO
    $table->foreignId('user_id')
        ->constrained()
        ->restrictOnDelete()
        ->cascadeOnUpdate();

    $table->timestamps();
});
```

**Fungsi tabel ini:**

* Menyimpan data produsen / head office
    
* 1 user = 1 head office
    
* Akan digunakan untuk approval & disbursement
    

---

## 4️⃣ Distributor

### Generate model & migration

```bash
php artisan make:model Distributor -m
```

### Migration: `distributors`

```php
Schema::create('distributors', function (Blueprint $table) {
    $table->id();
    $table->string('name');

    // user distributor
    $table->foreignId('user_id')
        ->constrained()
        ->restrictOnDelete()
        ->cascadeOnUpdate();

    $table->timestamps();
});
```

**Catatan:**

* Distributor **tidak menerima uang**
    
* Tugas utama: **verifikasi klaim secara digital**
    

---

## 5️⃣ Retail (Seller)

### Generate model & migration

```bash
php artisan make:model Retail -m
```

### Migration: `retails`

```php
Schema::create('retails', function (Blueprint $table) {
    $table->id();
    $table->string('name');

    // data pembayaran klaim
    $table->string('payment_account'); // contoh: BCA, Mandiri
    $table->string('payment_number');  // nomor rekening / e-wallet

    $table->foreignId('user_id')
        ->constrained()
        ->restrictOnDelete()
        ->cascadeOnUpdate();

    // distributor yang mensupply retail ini
    $table->foreignId('distributor_id')
        ->constrained()
        ->cascadeOnUpdate();

    $table->timestamps();
});
```

**Kenapa** `payment_*` disimpan di Retail?

* Klaim dibayar **langsung ke seller**
    
* HO tidak perlu input manual rekening setiap klaim
    

---

## 6️⃣ Product

### Generate model & migration

```bash
php artisan make:model Product -m
```

### Migration: `products`

```php
Schema::create('products', function (Blueprint $table) {
    $table->id();
    $table->string('sku')->unique();
    $table->string('name');
    $table->timestamps();
});
```

Produk akan digunakan:

* di promo
    
* di claim item
    

---

## 7️⃣ Promo Program

### Generate model & migration

```bash
php artisan make:model PromoProgram -m
```

### Migration: `promo_programs`

```php
Schema::create('promo_programs', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->date('starts_at');
    $table->date('ends_at');
    $table->boolean('is_active')->default(true);
    $table->timestamps();
});
```

**PromoProgram = container utama promo**, contoh:

> Promo Tutup Susu Q1–Q3 2025

---

## 8️⃣ Promo Rates (WAJIB per Product)

### Aturan Bisnis

> Dalam satu Promo Program, **setiap produk wajib memiliki promo rate sendiri**.  
> Tidak ada rate global.

### Contoh:

* Susu 1,35 kg → Rp 20.000
    
* Susu 700 gram → Rp 10.000
    

### Generate model & migration

```bash
php artisan make:model PromoRate -m
```

> ⚠️ Catatan kecil: nama model sebaiknya **singular (**`PromoRate`), bukan `PromoRates`.

### Migration: `promo_rates`

```php
Schema::create('promo_rates', function (Blueprint $table) {
    $table->id();

    $table->foreignId('promo_program_id')
        ->constrained()
        ->cascadeOnDelete();

    // WAJIB produk
    $table->foreignId('product_id')
        ->constrained()
        ->restrictOnDelete();

    $table->unsignedBigInteger('amount_per_item');

    $table->timestamps();

    $table->unique([
        'promo_program_id',
        'product_id',
    ], 'promo_rate_unique');
});
```

**Keuntungan desain ini:**

* tidak ada ambiguity rate
    
* klaim produk tanpa rate otomatis ditolak
    
* audit & laporan jauh lebih aman
    

---

## 9️⃣ Claim (Header Klaim)

### Generate model & migration

```bash
php artisan make:model Claim -m
```

### Migration: `claims`

```php
Schema::create('claims', function (Blueprint $table) {
    $table->id();
    $table->string('code')->unique();

    $table->foreignId('retail_id')
        ->constrained()
        ->restrictOnDelete();

    $table->foreignId('distributor_id')
        ->constrained()
        ->restrictOnDelete();

    $table->foreignId('promo_program_id')
        ->constrained()
        ->restrictOnDelete();

    $table->string('status');
    $table->timestamp('submitted_at')->nullable();
    $table->timestamp('verified_at')->nullable();
    $table->timestamp('ho_reviewed_at')->nullable();

    $table->unsignedBigInteger('approved_amount')->default(0);
    $table->text('notes')->nullable();

    $table->timestamps();
});
```

---

## 🔟 Claim Item (Detail Klaim)

```bash
php artisan make:model ClaimItem -m
```

```php
Schema::create('claim_items', function (Blueprint $table) {
    $table->id();
    $table->foreignId('claim_id')->constrained()->cascadeOnDelete();
    $table->foreignId('product_id')->constrained()->restrictOnDelete();

    $table->unsignedInteger('qty_submitted');
    $table->unsignedInteger('qty_approved')->nullable();

    // freeze rate saat submit
    $table->unsignedBigInteger('unit_amount');
    $table->unsignedBigInteger('subtotal_amount')->default(0);

    $table->string('rejection_reason')->nullable();
    $table->timestamps();

    $table->index(['claim_id', 'product_id']);
});
```

**Ini yang memungkinkan:**

* partial approve
    
* audit jumlah & nilai klaim
    

---

## 1️⃣1️⃣ Claim Evidence (Bukti)

```bash
php artisan make:model ClaimEvidence -m
```

```php
Schema::create('claim_evidences', function (Blueprint $table) {
    $table->id();
    $table->foreignId('claim_id')->constrained()->cascadeOnDelete();
    $table->string('type'); // LID_PHOTO | RECEIPT
    $table->string('file_path');
    $table->foreignId('uploaded_by')->constrained('users')->restrictOnDelete();
    $table->json('meta')->nullable();
    $table->timestamps();
});
```

---

## 1️⃣2️⃣ Claim Verification Log

```bash
php artisan make:model ClaimVerification -m
```

```php
Schema::create('claim_verifications', function (Blueprint $table) {
    $table->id();
    $table->foreignId('claim_id')->constrained()->cascadeOnDelete();
    $table->foreignId('verifier_user_id')->constrained('users')->restrictOnDelete();
    $table->string('status'); // APPROVE | PARTIAL_APPROVE | REJECT
    $table->text('notes')->nullable();
    $table->timestamps();
});
```

Audit trail ✅

---

## 1️⃣3️⃣ Disbursement (Pembayaran)

```bash
php artisan make:model Disbursement -m
```

```php
Schema::create('disbursements', function (Blueprint $table) {
    $table->id();
    $table->foreignId('claim_id')->constrained()->cascadeOnDelete();

    $table->unsignedBigInteger('amount');
    $table->string('method')->default('BANK_TRANSFER');
    $table->string('provider_ref')->nullable();
    $table->string('status'); // PENDING | PAID | FAILED
    $table->timestamp('paid_at')->nullable();

    $table->timestamps();

    $table->unique('claim_id');
});
```

---

## 1️⃣4️⃣ Jalankan Migration

```bash
php artisan migrate
```

Kalau ini lolos tanpa error, berarti **fondasi sistem sudah solid** 🔥

---

## Penutup Part 2

Di Part 2 ini kita sudah:  
✅ Membuat project Laravel  
✅ Mendesain database **berdasarkan flow bisnis real**  
✅ Menghindari desain “sekadar CRUD”

---