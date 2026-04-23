# TESTING SPECIFICATION
## [FEAT-003] Manajemen Katalog Produk

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-003 |
| Feature Name | Manajemen Katalog Produk |
| Epic | EPIC-001: Admin Panel |
| Business Spec | `03-product-catalog---business.md` |
| Status | Draft |
| Owner | QA Engineer |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |

---

## 1. Test Strategy

### 1.1 Scope of Testing

| Type | In Scope | Out of Scope |
|------|----------|--------------|
| Functional | CRUD produk, kategori, nonaktifkan/aktifkan produk | Import CSV massal, manajemen stok, harga promo |
| Security | Akses halaman hanya untuk Admin | Penetration testing mendalam |
| Performance | Daftar produk load < 3 detik | Load testing |
| Usability | Form error jelas, pencarian responsif | A/B testing |

### 1.2 Risk-Based Priority

| Risk Area | Impact | Likelihood | Test Priority |
|-----------|--------|------------|---------------|
| SKU duplikat diterima sistem | High | Medium | P1 |
| Produk nonaktif masih muncul di form order Sales | High | Medium | P1 |
| User non-Admin dapat mengakses halaman ini | High | Medium | P1 |
| Harga negatif/nol diterima sistem | High | Low | P1 |
| Riwayat order terhapus saat nonaktifkan produk | High | Low | P1 |

### 1.3 Environment Requirements

| Environment | Purpose |
|-------------|---------|
| Staging | QA testing & regression |
| UAT | Client acceptance testing |

---

## 2. Acceptance Criteria

### US-01: Membuat Produk Baru

```gherkin
Feature: [FEAT-003-US-01] Membuat Produk Baru
  As Admin
  I want menambahkan produk baru ke dalam katalog sistem
  So that Sales dapat memilih produk tersebut saat membuat order digital

  Background:
    Given Admin sudah login ke Admin Panel
    And Admin berada di halaman Manajemen Katalog Produk

  @happy-path
  Scenario: Admin berhasil membuat produk baru
    Given belum ada produk dengan SKU "PRD-001"
    When Admin mengisi form dengan nama "Mie Goreng Instan", SKU "PRD-001", harga "3500", satuan "pcs", kategori "Makanan"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan sukses "Produk berhasil ditambahkan"
    And produk "Mie Goreng Instan" muncul di daftar dengan status "Aktif"
    And produk tersedia sebagai pilihan di form order Sales

  @validation
  Scenario: Admin mencoba membuat produk dengan SKU yang sudah digunakan
    Given sudah ada produk dengan SKU "PRD-001"
    When Admin mengisi form dengan SKU "PRD-001"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "SKU sudah digunakan oleh produk lain"
    And produk baru tidak tersimpan

  @validation
  Scenario: Admin mencoba menyimpan dengan harga negatif atau nol
    When Admin mengisi harga "-1000" atau "0"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "Harga harus lebih dari 0"

  @validation
  Scenario: Admin mencoba menyimpan form dengan field wajib kosong
    When Admin menekan tombol "Simpan" tanpa mengisi field apapun
    Then sistem menampilkan pesan error pada setiap field wajib yang kosong
    And produk tidak tersimpan

  @edge-case
  Scenario: Admin membuat produk baru dengan kategori baru
    When Admin mengisi kategori dengan nama kategori yang belum ada sebelumnya
    And Admin menekan tombol "Simpan"
    Then produk berhasil disimpan
    And kategori baru tersedia sebagai pilihan filter di halaman daftar produk
```

---

### US-02: Melihat Daftar Produk

```gherkin
Feature: [FEAT-003-US-02] Melihat Daftar Produk
  As Admin
  I want melihat semua produk yang terdaftar di katalog beserta status dan data kuncinya
  So that saya dapat memantau kelengkapan katalog

  Background:
    Given Admin sudah login ke Admin Panel
    And tersedia setidaknya 5 produk di sistem

  @happy-path
  Scenario: Admin melihat daftar produk
    When Admin membuka halaman Manajemen Katalog Produk
    Then halaman menampilkan tabel dengan kolom nama, SKU, kategori, harga, satuan, status

  @happy-path
  Scenario: Admin mencari produk berdasarkan nama
    When Admin mengetik "Mie" di field pencarian
    Then hanya produk yang mengandung "Mie" di nama atau SKU yang ditampilkan

  @happy-path
  Scenario: Admin memfilter produk berdasarkan kategori
    When Admin memilih filter kategori "Makanan"
    Then hanya produk dalam kategori "Makanan" yang ditampilkan

  @happy-path
  Scenario: Admin memfilter produk berdasarkan status
    When Admin memilih filter status "Nonaktif"
    Then hanya produk dengan status "Nonaktif" yang ditampilkan

  @edge-case
  Scenario: Katalog produk kosong
    Given belum ada produk yang dibuat
    When Admin membuka halaman Manajemen Katalog Produk
    Then halaman menampilkan pesan "Belum ada produk. Tambah produk pertama Anda."
```

---

### US-03: Mengedit Data Produk

```gherkin
Feature: [FEAT-003-US-03] Mengedit Data Produk
  As Admin
  I want mengubah data produk yang sudah ada
  So that informasi produk selalu akurat

  Background:
    Given Admin sudah login ke Admin Panel
    And ada produk "Mie Goreng Instan" dengan SKU "PRD-001" dan harga "3500"

  @happy-path
  Scenario: Admin berhasil mengubah harga produk
    When Admin membuka detail produk "Mie Goreng Instan"
    And Admin mengubah harga menjadi "4000"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan sukses "Produk berhasil diperbarui"
    And harga produk di katalog berubah menjadi "4000"
    And order baru yang dibuat Sales menggunakan harga "4000"

  @happy-path
  Scenario: Admin berhasil mengubah SKU produk
    When Admin mengubah SKU "PRD-001" menjadi "PRD-001-NEW"
    And Admin menekan tombol "Simpan"
    Then SKU produk berubah menjadi "PRD-001-NEW"

  @validation
  Scenario: Admin mengubah SKU dengan SKU yang sudah digunakan produk lain
    Given ada produk lain dengan SKU "PRD-002"
    When Admin mengubah SKU "Mie Goreng Instan" menjadi "PRD-002"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "SKU sudah digunakan oleh produk lain"
    And perubahan tidak tersimpan
```

---

### US-04: Menonaktifkan / Mengaktifkan Produk

```gherkin
Feature: [FEAT-003-US-04] Menonaktifkan dan Mengaktifkan Produk
  As Admin
  I want menonaktifkan produk yang tidak dijual lagi atau mengaktifkan kembali produk yang sebelumnya nonaktif
  So that Sales hanya dapat memesan produk yang tersedia saat ini

  Background:
    Given Admin sudah login ke Admin Panel

  @happy-path
  Scenario: Admin berhasil menonaktifkan produk
    Given ada produk aktif "Mie Goreng Instan"
    When Admin menekan tombol "Nonaktifkan" pada produk tersebut
    And Admin mengonfirmasi tindakan
    Then status produk berubah menjadi "Nonaktif"
    And produk tidak muncul sebagai pilihan di form order Sales

  @happy-path
  Scenario: Admin mengaktifkan kembali produk nonaktif
    Given ada produk nonaktif "Mie Goreng Instan"
    When Admin menekan tombol "Aktifkan" pada produk tersebut
    Then status produk berubah menjadi "Aktif"
    And produk kembali tersedia sebagai pilihan di form order Sales

  @edge-case
  Scenario: Riwayat order produk nonaktif tetap tersimpan
    Given produk "Mie Goreng Instan" telah dinonaktifkan
    And ada riwayat order yang mengandung produk tersebut
    When Admin atau Manager membuka riwayat order
    Then riwayat order masih tampil lengkap termasuk nama dan harga produk saat order dibuat
```

---

### US-05: Mengelola Kategori Produk

```gherkin
Feature: [FEAT-003-US-05] Mengelola Kategori Produk
  As Admin
  I want mengelompokkan produk ke dalam kategori
  So that Sales dapat dengan mudah menemukan produk yang relevan

  Background:
    Given Admin sudah login ke Admin Panel

  @happy-path
  Scenario: Admin menetapkan kategori pada produk baru
    When Admin mengisi kategori "Minuman" saat membuat produk baru
    And Admin menyimpan produk
    Then produk tersimpan dalam kategori "Minuman"
    And kategori "Minuman" tersedia sebagai filter di halaman daftar produk

  @happy-path
  Scenario: Admin membuat kategori baru saat menambah produk
    Given belum ada kategori "Produk Rumah Tangga"
    When Admin mengetik "Produk Rumah Tangga" di field kategori
    And menyimpan produk
    Then kategori baru "Produk Rumah Tangga" terbuat
    And tersedia sebagai opsi filter untuk produk lain
```

---

## 3. Validation Rules

| Field | Rule | Pesan Error |
|-------|------|-------------|
| Nama produk | Wajib diisi, maks 200 karakter | "Nama produk wajib diisi" |
| SKU | Wajib diisi, unik, maks 50 karakter | "SKU wajib diisi" / "SKU sudah digunakan" |
| Harga | Wajib diisi, angka positif > 0 | "Harga harus lebih dari 0" |
| Satuan | Wajib diisi (pcs, dus, karton, dll.) | "Satuan wajib diisi" |
| Kategori | Wajib dipilih atau diisi | "Kategori wajib diisi" |
| Deskripsi | Opsional | — |

---

## 4. Test Cases

### 4.1 Functional Tests

| ID | Skenario | Preconditions | Steps | Expected Result | Priority |
|----|----------|---------------|-------|-----------------|----------|
| TC-003-001 | Buat produk baru berhasil | Login Admin | Isi form lengkap > Simpan | Produk muncul di daftar dan form order | P1 |
| TC-003-002 | SKU duplikat ditolak | Ada produk dengan SKU sama | Isi form SKU sama > Simpan | Error SKU sudah digunakan | P1 |
| TC-003-003 | Harga negatif ditolak | Login Admin | Isi harga "-100" > Simpan | Error harga harus > 0 | P1 |
| TC-003-004 | Harga nol ditolak | Login Admin | Isi harga "0" > Simpan | Error harga harus > 0 | P1 |
| TC-003-005 | Form kosong ditolak | Login Admin | Klik simpan tanpa isi | Error semua field wajib | P1 |
| TC-003-006 | Lihat daftar produk | Minimal 1 produk | Buka halaman | Tabel tampil benar | P1 |
| TC-003-007 | Cari produk by nama | Ada produk | Ketik nama di pencarian | Hanya produk cocok tampil | P2 |
| TC-003-008 | Filter by kategori | Ada produk berbagai kategori | Pilih filter | Hanya produk dalam kategori tampil | P2 |
| TC-003-009 | Edit harga produk | Ada produk aktif | Ubah harga > Simpan | Harga baru berlaku di form order | P1 |
| TC-003-010 | Edit SKU duplikat | Ada 2 produk | Ubah SKU ke SKU produk lain | Error SKU sudah digunakan | P1 |
| TC-003-011 | Nonaktifkan produk | Ada produk aktif | Nonaktifkan > Konfirmasi | Status Nonaktif, hilang dari form order | P1 |
| TC-003-012 | Aktifkan produk | Ada produk nonaktif | Aktifkan | Status Aktif, muncul di form order | P1 |
| TC-003-013 | Riwayat order terjaga setelah nonaktifkan | Ada riwayat order | Nonaktifkan produk > cek riwayat order | Riwayat order masih ada | P1 |
| TC-003-014 | Buat kategori baru saat tambah produk | Login Admin | Ketik nama kategori baru | Kategori baru terbuat | P2 |

### 4.2 Security Tests

| ID | Skenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-003-S01 | Akses halaman tanpa login | Buka URL langsung | Redirect ke halaman login | P1 |
| TC-003-S02 | Akses halaman sebagai Sales | Login Sales > buka URL | Ditolak | P1 |

### 4.3 Usability Tests

| ID | Skenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-003-U01 | Konfirmasi sebelum nonaktifkan produk | Klik Nonaktifkan | Muncul dialog konfirmasi | P2 |
| TC-003-U02 | Pencarian produk responsif | Ketik di field pencarian | Hasil muncul tanpa perlu klik tombol search | P2 |

---

## 5. Test Data

### 5.1 Test Accounts

| Account | Role | Purpose |
|---------|------|---------|
| admin@rutego.com | Admin | Executor semua test |
| sales@rutego.com | Sales | Verifikasi akses halaman admin ditolak & produk di form order |

### 5.2 Test Data Sets

| Dataset | Description |
|---------|-------------|
| Produk seed set | 15 produk: 10 aktif (3 kategori berbeda), 5 nonaktif |
| Produk riwayat order | Minimal 2 produk yang punya riwayat order (untuk test BR-04) |

---

## 6. Automation Plan

### 6.1 Coverage

| Category | Total | Automated | Manual |
|----------|-------|-----------|--------|
| Functional | 14 | 12 | 2 |
| Security | 2 | 2 | 0 |
| Usability | 2 | 0 | 2 |

### 6.2 Playwright Mapping

| Test Case | Playwright File | Page Object |
|-----------|-----------------|-------------|
| TC-003-001 hingga TC-003-014 | `test/web/features/product-catalog.spec.ts` | `test/web/pages/product-catalog.page.ts` |
| TC-003-S01, TC-003-S02 | `test/web/features/product-catalog-security.spec.ts` | `test/web/pages/product-catalog.page.ts` |

---

## 7. UAT Scenarios

### UAT Scenario 01: Menambahkan Produk Baru ke Katalog

**Objective:** Memastikan Admin dapat menambah produk dan produk tersedia di form order Sales

**Prerequisites:**
- Login sebagai Admin
- Buka halaman Manajemen Katalog Produk

**Steps:**
1. Klik tombol "Tambah Produk"
2. Isi nama produk, SKU, harga, satuan, dan kategori
3. Klik "Simpan"
4. Verifikasi produk muncul di daftar dengan status Aktif
5. Login sebagai Sales, buka form order, verifikasi produk baru tersedia

**Expected Results:**
- Produk baru tampil di daftar Admin
- Produk tersedia sebagai pilihan di form order Sales

**Status:** [ ] Pass  [ ] Fail

---

### UAT Scenario 02: Menonaktifkan Produk

**Objective:** Memastikan produk nonaktif tidak dapat dipilih Sales saat membuat order

**Prerequisites:**
- Login sebagai Admin
- Ada produk aktif
- Akun Sales tersedia untuk verifikasi

**Steps:**
1. Temukan produk di daftar
2. Klik tombol "Nonaktifkan" dan konfirmasi
3. Verifikasi status berubah menjadi "Nonaktif"
4. Login sebagai Sales, buka form order
5. Verifikasi produk yang dinonaktifkan tidak tersedia

**Expected Results:**
- Status produk berubah menjadi "Nonaktif"
- Produk tidak muncul di pilihan form order Sales

**Status:** [ ] Pass  [ ] Fail

---

### UAT Scenario 03: Mengubah Harga Produk

**Objective:** Memastikan perubahan harga berlaku untuk order baru

**Prerequisites:**
- Login sebagai Admin
- Ada produk aktif dengan harga tertentu

**Steps:**
1. Buka detail produk
2. Ubah harga dan simpan
3. Login sebagai Sales
4. Buat order baru dan pilih produk tersebut
5. Verifikasi harga yang tampil di form order sudah menggunakan harga baru

**Expected Results:**
- Harga berubah di katalog Admin
- Form order Sales menampilkan harga yang sudah diperbarui

**Status:** [ ] Pass  [ ] Fail

---

## 8. Sign-off

| Role | Name | Date | Status |
|------|------|------|--------|
| QA Lead | | | Pending |
| PM | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-admin-panel/03-product-catalog---testing.md

RELATED FILES:
- 03-product-catalog---business.md (PM: user stories, business rules)
- 03-product-catalog---technical.md (Dev: API, data model)
- 03-product-catalog---design.md (Design: wireframes)
-->
