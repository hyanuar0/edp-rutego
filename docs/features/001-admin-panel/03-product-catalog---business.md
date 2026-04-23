# BUSINESS SPECIFICATION
## [FEAT-003] Manajemen Katalog Produk

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-003 |
| Feature Name | Manajemen Katalog Produk |
| Epic | EPIC-001: Admin Panel |
| Priority | Must Have |
| Status | Draft |
| Owner | Product Manager |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Taiga Sync | Synced (US #4) |

---

## 1. Feature Overview

### 1.1 Business Context

**Problem:**
> Katalog produk saat ini dikelola secara manual (spreadsheet/kertas), menyebabkan data produk yang tidak konsisten. Sales tidak memiliki referensi produk yang akurat dan terkini saat membuat order di lapangan, yang berakibat pada kesalahan pencatatan dan potensi kehilangan revenue.

**Solution:**
> Menyediakan halaman manajemen katalog produk di Admin Panel yang memungkinkan Admin untuk membuat, melihat, mengedit, dan menonaktifkan produk, sehingga Sales selalu mengacu pada katalog produk yang valid dan terkini saat proses pembuatan order.

### 1.2 Target Users

| User Role | Primary/Secondary | Needs |
|-----------|-------------------|-------|
| Admin | Primary | Kelola data produk (buat, ubah, nonaktifkan) agar katalog selalu akurat |
| Sales | Secondary | Menggunakan katalog produk sebagai referensi saat membuat order digital |

### 1.3 Success Metrics

| Metric | Current | Target | Measurement |
|--------|---------|--------|-------------|
| Kelengkapan data produk di sistem | Tidak terukur | 100% produk aktif tersedia di katalog sebelum go-live | Jumlah produk di sistem vs daftar produk aktif dari tim bisnis |
| Kesalahan order akibat produk tidak ditemukan | Tidak terukur | 0 order gagal karena produk tidak ada di katalog | Laporan error order terkait produk |
| Waktu penambahan produk baru | Manual (tidak terstandar) | < 3 menit per produk | Durasi pengisian form hingga data tersimpan |

---

## 2. Scope

### 2.1 In Scope

| # | Capability | Description |
|---|------------|-------------|
| 1 | Buat produk baru | Admin mengisi data produk (nama, SKU, harga, satuan, deskripsi) |
| 2 | Lihat daftar produk | Admin melihat semua produk dengan filter dan pencarian |
| 3 | Edit data produk | Admin mengubah data produk yang sudah ada |
| 4 | Nonaktifkan / aktifkan produk | Admin menonaktifkan produk yang sudah tidak dijual |
| 5 | Kategorisasi produk | Admin mengelompokkan produk ke dalam kategori untuk kemudahan pencarian |

### 2.2 Out of Scope

| # | Item | Reason |
|---|------|--------|
| 1 | Import massal produk via file CSV/Excel | Out of scope MVP 1 |
| 2 | Manajemen stok / inventori | Out of scope MVP 1; sistem ini bukan sistem inventori |
| 3 | Pengelolaan harga per outlet atau harga promo | Out of scope MVP 1; satu harga per produk |
| 4 | Upload gambar produk | Out of scope MVP 1 |
| 5 | Integrasi dengan sistem ERP atau POS | Integrasi ERP di luar scope MVP 1 |

---

## 3. User Stories

### US-01: Membuat Produk Baru

> **As** Admin
> **I want** menambahkan produk baru ke dalam katalog sistem
> **So that** Sales dapat memilih produk tersebut saat membuat order digital di lapangan

**Expected Behavior / Acceptance Criteria:**
- Admin mengisi form dengan: nama produk (wajib), SKU/kode produk (wajib, unik), harga satuan (wajib), satuan (wajib, misal: pcs, dus, karton), kategori (wajib), dan deskripsi (opsional)
- Sistem memvalidasi bahwa SKU belum digunakan; jika sudah ada, tampilkan pesan error yang jelas
- Setelah disimpan, produk baru muncul di daftar produk dengan status "Aktif"
- Produk yang aktif langsung tersedia sebagai pilihan saat Sales membuat order digital
- Jika data wajib tidak lengkap, tampilkan pesan error pada field terkait dan tidak menyimpan data

---

### US-02: Melihat Daftar Produk

> **As** Admin
> **I want** melihat semua produk yang terdaftar di katalog beserta status dan data kuncinya
> **So that** saya dapat memantau kelengkapan katalog dan mengetahui produk mana yang aktif atau nonaktif

**Expected Behavior / Acceptance Criteria:**
- Halaman menampilkan daftar produk dalam format tabel dengan kolom: nama produk, SKU, kategori, harga satuan, satuan, status (Aktif/Nonaktif)
- Admin dapat mencari produk berdasarkan nama atau SKU
- Admin dapat memfilter daftar berdasarkan kategori dan/atau status
- Daftar mendukung paginasi jika jumlah produk melebihi batas tampilan satu halaman

---

### US-03: Mengedit Data Produk

> **As** Admin
> **I want** mengubah data produk yang sudah ada
> **So that** informasi produk (nama, harga, satuan) selalu akurat dan mencerminkan kondisi bisnis terkini

**Expected Behavior / Acceptance Criteria:**
- Admin memilih produk dari daftar dan membuka form edit
- Admin dapat mengubah semua field: nama, SKU, harga, satuan, kategori, deskripsi
- Sistem memvalidasi SKU tidak bentrok dengan produk lain saat form disimpan
- Setelah disimpan, perubahan langsung tercermin di daftar produk dan di form order Sales
- Perubahan harga berlaku untuk order baru yang dibuat setelah perubahan disimpan

---

### US-04: Menonaktifkan / Mengaktifkan Produk

> **As** Admin
> **I want** menonaktifkan produk yang sudah tidak dijual atau mengaktifkan kembali produk yang sebelumnya nonaktif
> **So that** Sales hanya dapat memesan produk yang saat ini tersedia, tanpa kehilangan riwayat order produk tersebut

**Expected Behavior / Acceptance Criteria:**
- Admin dapat menonaktifkan produk aktif dari halaman daftar atau detail produk
- Produk yang dinonaktifkan tidak muncul sebagai pilihan di form order Sales
- Riwayat order yang sudah menggunakan produk tersebut tetap tersimpan dan tidak terpengaruh
- Admin dapat mengaktifkan kembali produk yang nonaktif; produk tersebut langsung tersedia kembali di form order Sales
- Status produk (Aktif/Nonaktif) ditampilkan dengan jelas di daftar produk

---

### US-05: Mengelola Kategori Produk

> **As** Admin
> **I want** mengelompokkan produk ke dalam kategori
> **So that** Sales dapat dengan mudah menemukan produk yang relevan saat membuat order di lapangan

**Expected Behavior / Acceptance Criteria:**
- Saat membuat atau mengedit produk, Admin dapat memilih kategori dari daftar yang tersedia atau membuat kategori baru
- Kategori produk tampil sebagai opsi filter di halaman daftar produk (Admin) dan form order (Sales)
- Produk tanpa kategori masuk ke kategori default "Umum" atau "Tidak Berkategori"

---

## 4. Business Rules

| Rule ID | Rule | Description |
|---------|------|-------------|
| BR-01 | SKU unik | Setiap produk harus memiliki SKU yang unik di seluruh katalog |
| BR-02 | Harga tidak boleh negatif | Harga satuan produk harus bernilai positif (> 0) |
| BR-03 | Produk aktif saja untuk order | Hanya produk berstatus Aktif yang dapat dipilih Sales saat membuat order |
| BR-04 | Data historis terjaga | Menonaktifkan produk tidak mengubah atau menghapus riwayat order yang sudah tercatat |
| BR-05 | Satuan wajib | Setiap produk harus memiliki satuan yang jelas (pcs, dus, karton, dll.) untuk menghindari ambiguitas saat pencatatan order |

---

## 5. Open Questions

| # | Question | Asked To | Status | Answer |
|---|----------|----------|--------|--------|
| 1 | Apakah harga produk dapat berbeda per outlet atau per sales? | PM | Pending | - |
| 2 | Apakah ada kebutuhan multi-harga (harga grosir vs eceran) pada MVP 1? | PM | Pending | - |
| 3 | Berapa estimasi jumlah produk di katalog (untuk pertimbangan performa pencarian)? | PM / Tech Lead | Pending | - |
| 4 | Apakah kategori produk bersifat flat (satu level) atau hierarkis (kategori & subkategori)? | PM | Pending | - |

---

## 6. Definition of Done

- [ ] Testing spec created (QA) — `03-product-catalog---testing.md`
- [ ] Technical spec created (Dev) — `03-product-catalog---technical.md`
- [ ] Design spec created (Designer) — `03-product-catalog---design.md`
- [ ] All specs reviewed and approved
- [ ] Implemented and deployed to staging
- [ ] QA sign-off
- [ ] Design QA passed

---

## 7. Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| PM | | | Pending |
| QA Lead | | | Pending |
| Tech Lead | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-admin-panel/03-product-catalog---business.md

RELATED FILES:
- 03-product-catalog---testing.md (QA: Gherkin AC, test cases, validation rules)
- 03-product-catalog---technical.md (Dev: API, data model, implementation)
- 03-product-catalog---design.md (Design: UI/UX specifications)
- 00-admin-panel---overview.md (Epic context)
-->
