---
title: "Claim Management System - Introduction (Part 1 edit)"
datePublished: Sat Dec 13 2025 10:45:45 GMT+0000 (Coordinated Universal Time)
cuid: cmj4671rm000102l5fhmle1jj
slug: claim-management-system-introduction-part-1-edit
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1765621944286/f66f1d5e-8882-4c8d-b8da-3890a2a9be77.png
tags: laravel, filament

---

## Apa yang akan kita bangun?

---

Sistem ini akan meng-handle:

✅ Input klaim dari seller (bukti: foto tutup + nota)  
✅ Verifikasi digital oleh distributor  
✅ Final approval oleh head office/produsen  
✅ Disbursement pembayaran klaim langsung ke seller (misalnya pakai Midtrans/transfer)  
✅ Status workflow + audit trail yang jelas

---

## Kenapa Sistem Ini Penting?

Kalau proses klaim masih manual (WA, email, excel), biasanya terjadi:

* **Double claim**: bukti sama diklaim berkali-kali
    
* **Manipulasi qty**: seller klaim 6, padahal distributor cuma supply 4
    
* **Nominal promo beda periode**: Jan–Feb Rp20k, Mar–Jul Rp30k → rawan salah hitung
    
* **Tidak ada audit trail**: pas dispute, susah cari siapa approve apa
    

CMS nge-fix semuanya dengan workflow yang rapi + data yang bisa ditrace.

---

## Aktor Sistem (Roles)

Kita pakai 3 aktor utama:

### 1) Seller / Retail

* Submit claim
    
* Upload bukti (foto tutup dan nota)
    
* Lihat status klaim
    

### 2) Distributor

* Verifikasi klaim secara digital
    
* Approve sebagian (partial approve) kalau perlu
    
* Kasih alasan reject
    

### 3) Head Office / Produsen

* Review & approve final
    
* Bayar klaim ke seller (disbursement)
    

---

## Business Flow Utama (Realistis, End-to-End)

Ini flow yang bakal jadi “tulang punggung” sistem.

### Step A — Setup Program Promo (oleh Produsen)

Produsen bikin program promo:

* Periode promo (misal Jan–Feb, Mar–Jul)
    
* Produk yang eligible
    
* Rate klaim per item per periode
    
    * Jan–Feb: Rp20.000/item
        
    * Mar–Jul: Rp30.000/item
        

📌 Poin penting: **rate itu bisa berubah tergantung periode**, bukan fix selamanya.

---

### Step B — Seller Submit Claim

Seller input:

* pilih program promo aktif
    
* pilih distributor yang mensupply dia
    
* input produk + qty
    
* upload:
    
    * 1 foto berisi banyak tutup (yang penting qty kelihatan)
        
    * nota pembelian/penjualan
        

Status: `SUBMITTED_BY_SELLER`

---

### Step C — Distributor Verification (Digital)

Distributor cek:

* apakah seller ini memang dia supply?
    
* apakah qty masuk akal berdasarkan supply?
    

#### Case normal:

Seller submit 10, distributor supply 10 → approve 10 ✅

#### Case real world (penting banget):

Seller submit 6, distributor cuma supply 4 → distributor:

* approve 4
    
* reject 2 (kasih alasan)
    

Status: `VERIFIED_BY_DISTRIBUTOR`

---

### Step D — Kirim ke Head Office (Produsen)

Sistem otomatis compile claim:

* item yang disetujui distributor
    
* hitung final amount (approved\_qty × promo\_rate)
    

Status: `SUBMITTED_TO_HO`

---

### Step E — HO Final Approval

HO review:

* pola klaim seller
    
* bukti dan riwayat
    
* distributor yang verifikasi
    

HO approve / reject.

Status:

* `APPROVED_BY_HO` atau `REJECTED_BY_HO`
    

---

### Step F — Disbursement ke Seller

Kalau approve:

* produsen bayar ke seller
    
* sistem update status jadi `PAID`
    

📌 Distributor **tidak terlibat uang sama sekali**.

---

## Status Workflow (Yang Kita Pakai di Series Ini)

Minimal state machine:

* `DRAFT` (opsional)
    
* `SUBMITTED_BY_SELLER`
    
* `VERIFIED_BY_DISTRIBUTOR`
    
* `SUBMITTED_TO_HO`
    
* `APPROVED_BY_HO`
    
* `REJECTED_BY_HO`
    
* `PAID`
    

---