---
title: "Claim Management System - Filament v4 Setup & Workflow Awal"
datePublished: Thu Dec 18 2025 05:30:12 GMT+0000 (Coordinated Universal Time)
cuid: cmjb04hzf000102jmbs774uoa
slug: claim-management-system-filament-v4-setup-and-workflow-awal
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1766035774932/f632d029-722f-4131-b2f7-6fc13ea40513.png
tags: laravel, filament

---

Di **Part 2**, kita sudah:

* melakukan setup project **Laravel 12**
    
* mendesain **database schema** yang realistis dan audit-friendly
    

Sekarang di **Part 3**, kita mulai membangun **UI dan workflow awal** menggunakan **Filament v4**.

---

## 🎯 Target Part 3

Pada bagian ini kita akan:

* Menginstall **Filament v4**
    
* Setup **multi-panel**:
    
    * Retail
        
    * Distributor
        
    * Head Office
        
* Membuat **CRUD master data**:
    
    * Product
        
    * Promo Program (**beserta Promo Rate per product di dalamnya**)
        

---

## 1️⃣ Install Filament v4

### Install package

```bash
composer require filament/filament:"^4.0"
```

### Install panel support

```bash
php artisan filament:install --panels
```

Saat muncul prompt:

```php
What is the panel's ID? [admin]:
```

👉 jawab:

```php
retail
```

Ini akan membuat **Retail Panel** sebagai panel pertama.

---

## 2️⃣ Konsep Multi-Panel

Karena sistem ini memiliki **3 aktor utama**, pendekatan **multi-panel** adalah pilihan paling tepat.

| Panel | Aktor |
| --- | --- |
| Retail Panel | Seller |
| Distributor Panel | Verifikator |
| Head Office Panel | Approval & Disbursement |

### Kenapa multi-panel?

* menu lebih bersih dan fokus
    
* boundary antar role lebih jelas
    
* lebih aman untuk policy & permission
    

---

## 3️⃣ Membuat Panel Tambahan

### 3.1 Distributor Panel

```bash
php artisan make:filament-panel distributor
```

URL akses:

```php
/distributor
```

---

### 3.2 Head Office Panel

```bash
php artisan make:filament-panel head-office
```

URL akses:

```php
/head-office
```

📌 **Catatan**  
Di **Part 4**, kita akan menambahkan:

* middleware
    
* policy
    
* role-based access
    

agar user **tidak bisa mengakses panel yang bukan haknya**.

---

## 4️⃣ Definisi Relasi Model

### (Wajib Disiapkan Sebelum Filament)

Sebelum membuat Filament Resource, kita **wajib memastikan relasi Eloquent sudah benar**, karena Filament sangat bergantung pada:

* `relationship()`
    
* `Repeater::relationship()`
    
* eager loading & policy
    

---

## 4.1 Model Product

Produk **hanya dikelola oleh Head Office**.

```php
class Product extends Model
{
    protected $fillable = ['sku', 'name'];

    public function promoRates()
    {
        return $this->hasMany(PromoRate::class);
    }

    public function claimItems()
    {
        return $this->hasMany(ClaimItem::class);
    }
}
```

---

## 4.2 Model PromoProgram

Promo Program **hanya dikelola oleh Head Office** dan berfungsi sebagai *container* promo.

```php
class PromoProgram extends Model
{
    protected $fillable = [
        'name',
        'starts_at',
        'ends_at',
        'is_active'
    ];

    protected $casts = [
        'starts_at' => 'date',
        'ends_at' => 'date',
        'is_active' => 'boolean',
    ];

    public function promoRates()
    {
        return $this->hasMany(PromoRate::class);
    }

    public function claims()
    {
        return $this->hasMany(Claim::class);
    }
}
```

---

## 4.3 Model PromoRate

Promo Rate **selalu milik PromoProgram dan Product**.

```php
class PromoRate extends Model
{
    protected $fillable = [
        'promo_program_id',
        'product_id',
        'starts_at',
        'ends_at',
        'amount_per_item'
    ];

    protected $casts = [
        'starts_at' => 'date',
        'ends_at' => 'date',
        'amount_per_item' => 'integer',
    ];

    public function promoProgram()
    {
        return $this->belongsTo(PromoProgram::class);
    }

    public function product()
    {
        return $this->belongsTo(Product::class);
    }
}
```

---

## 4.4 Model Claim

Claim **dibuat oleh Retail** dan menjadi pusat workflow.

```php
class Claim extends Model
{
    protected $fillable = [
        'code',
        'retail_id',
        'distributor_id',
        'promo_program_id',
        'status',
        'submitted_at',
        'verified_at',
        'ho_reviewed_at',
        'approved_amount',
        'notes'
    ];

    protected $casts = [
        'submitted_at' => 'datetime',
        'verified_at' => 'datetime',
        'ho_reviewed_at' => 'datetime',
        'approved_amount' => 'integer',
    ];

    public function retail()
    {
        return $this->belongsTo(Retail::class);
    }

    public function distributor()
    {
        return $this->belongsTo(Distributor::class);
    }

    public function promoProgram()
    {
        return $this->belongsTo(PromoProgram::class);
    }

    public function items()
    {
        return $this->hasMany(ClaimItem::class);
    }

    public function evidences()
    {
        return $this->hasMany(ClaimEvidence::class);
    }

    public function verifications()
    {
        return $this->hasMany(ClaimVerification::class);
    }

    public function disbursement()
    {
        return $this->hasOne(Disbursement::class);
    }
}
```

---

## 4.5 Model ClaimItem

```php
class ClaimItem extends Model
{
    protected $fillable = [
        'claim_id',
        'product_id',
        'qty_submitted',
        'qty_approved',
        'unit_amount',
        'subtotal_amount',
        'rejection_reason'
    ];

    protected $casts = [
        'qty_submitted' => 'integer',
        'qty_approved' => 'integer',
        'unit_amount' => 'integer',
        'subtotal_amount' => 'integer',
    ];

    public function claim()
    {
        return $this->belongsTo(Claim::class);
    }

    public function product()
    {
        return $this->belongsTo(Product::class);
    }
}
```

---

## 4.6 Model ClaimEvidence

```php
class ClaimEvidence extends Model
{
    protected $fillable = [
        'claim_id',
        'type',
        'file_path',
        'uploaded_by',
        'meta'
    ];

    protected $casts = [
        'meta' => 'array',
    ];

    public function claim()
    {
        return $this->belongsTo(Claim::class);
    }

    public function uploader()
    {
        return $this->belongsTo(User::class, 'uploaded_by');
    }
}
```

---

## 4.7 Model ClaimVerification

```php
class ClaimVerification extends Model
{
    protected $fillable = [
        'claim_id',
        'verifier_user_id',
        'action',
        'notes'
    ];

    public function claim()
    {
        return $this->belongsTo(Claim::class);
    }

    public function verifier()
    {
        return $this->belongsTo(User::class, 'verifier_user_id');
    }
}
```

---

## 4.8 Model Disbursement

```php
class Disbursement extends Model
{
    protected $fillable = [
        'claim_id',
        'amount',
        'method',
        'provider_ref',
        'status',
        'paid_at'
    ];

    protected $casts = [
        'amount' => 'integer',
        'paid_at' => 'datetime',
    ];

    public function claim()
    {
        return $this->belongsTo(Claim::class);
    }
}
```

---

## 5️⃣ Head Office — Product Resource

### Generate Resource

```bash
php artisan make:filament-resource Product --panel=HeadOffice --generate
```

Filament akan membuat:

```php
App\Filament\HeadOffice\Resources\ProductResource
```

berdasarkan migration dan relasi yang sudah didefinisikan.

---

## 6️⃣ Promo Program + Promo Rate (Embedded)

👉 **PromoRate TIDAK dibuat sebagai resource terpisah**

### Alasan desain

* PromoRate tidak berdiri sendiri
    
* Selalu milik PromoProgram
    
* Sidebar lebih ringkas
    
* UX admin lebih natural
    

---

### Generate PromoProgram Resource

```bash
php artisan make:filament-resource PromoProgram --panel=HeadOffice --generate
```

Filament akan membuat:

```php
App\Filament\HeadOffice\Resources\PromoProgramResource
```

---

### Form PromoProgram

```php
<?php

namespace App\Filament\HeadOffice\Resources\PromoPrograms\Schemas;

use Filament\Forms\Components\DatePicker;
use Filament\Forms\Components\TextInput;
use Filament\Forms\Components\Toggle;
use Filament\Schemas\Schema;
use Filament\Schemas\Components\Section;
use Filament\Forms\Components\Repeater;
use Filament\Forms\Components\Select;

class PromoProgramForm
{
    public static function configure(Schema $schema): Schema
    {
        return $schema
            ->components([
                Section::make('Informasi Promo')
                    ->description('Pengaturan umum periode dan status promo')
                    ->schema([
                        TextInput::make('name')
                            ->label('Nama Promo')
                            ->placeholder('Contoh: Promo Tutup Botol Q1')
                            ->required(),

                        DatePicker::make('starts_at')
                            ->label('Tanggal Mulai')
                            ->native(false)
                            ->required(),

                        DatePicker::make('ends_at')
                            ->label('Tanggal Selesai')
                            ->native(false)
                            ->required(),

                        Toggle::make('is_active')
                            ->label('Status Promo')
                            ->inline(false)
                            ->default(true),
                    ])
                    ->columns(2)
                    ->columnSpanFull(),

                Section::make('Promo Rate per Produk')
                    ->description('Nominal klaim untuk setiap produk dalam promo ini')
                    ->schema([
                        Repeater::make('promoRates')
                            ->relationship()
                            ->label('Daftar Produk & Nominal Klaim')
                            ->columns(3)
                            ->schema([
                                Select::make('product_id')
                                    ->label('Produk')
                                    ->relationship('product', 'name')
                                    ->searchable()
                                    ->preload()
                                    ->required()
                                    ->disableOptionWhen(function ($value, $state, $get) {
                                        $selectedProducts = collect($get('../../promoRates'))
                                            ->pluck('product_id')
                                            ->filter()
                                            ->toArray();

                                        return in_array($value, $selectedProducts) && $value !== $state;
                                    }),

                                TextInput::make('amount_per_item')
                                    ->label('Nominal / Item')
                                    ->numeric()
                                    ->prefix('Rp')
                                    ->placeholder('Contoh: 20.000')
                                    ->required(),
                            ])
                            ->addActionLabel('Tambah Produk Promo')
                            ->minItems(1)
                            ->required()
                            ->reorderable(false)
                            ->collapsible()
                            ->itemLabel(fn (array $state): ?string =>
                                $state['product_id']
                                    ? 'Produk ID: ' . $state['product_id']
                                    : 'Produk Baru'
                            ),
                    ])
                    ->columnSpanFull(),
            ]);
    }
}
```

---

## 7️⃣ Login ke Head Office Panel

Agar bisa melihat UI, kita perlu login ke panel **Head Office**.

### Tambahkan `login()` pada PanelProvider

```php
return $panel
    ->id('head-office')
    ->path('head-office')
    ->login() //tambahkan ini
```

Ini akan membuat route:

```php
/head-office/login
```

---

### Membuat User Head Office

```bash
php artisan make:filament-user
```

Saat muncul prompt:

```php
Which panel would you like to create this user in? [retail]:
```

👉 ketik:

```php
head-office
```

Lalu isi:

* name
    
* email
    
* password
    

Gunakan akun ini untuk login ke **Head Office Panel**.

---

## Penutup Part 3

Pada **Part 3** ini kita sudah:

* menginstall **Filament v4**
    
* setup **multi-panel**
    
* mendefinisikan **Eloquent relationship lengkap**
    
* membuat CRUD:
    
    * Product
        
    * Promo Program + Promo Rate (embedded)
        
* memastikan UI Head Office bisa diakses dan diuji
    

---