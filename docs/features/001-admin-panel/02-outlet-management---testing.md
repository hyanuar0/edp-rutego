# TESTING SPECIFICATION
## [FEAT-002] Manajemen Outlet

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-002 |
| Feature Name | Manajemen Outlet |
| Epic | EPIC-001: Admin Panel |
| Business Spec | `02-outlet-management---business.md` |
| Status | Draft |
| Owner | QA Engineer |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |

---

## 1. Test Strategy

### 1.1 Scope of Testing

| Type | In Scope | Out of Scope |
|------|----------|--------------|
| Functional | CRUD outlet, validasi koordinat GPS, nonaktifkan/aktifkan outlet | Import CSV massal, peta interaktif |
| Security | Akses halaman hanya untuk Admin | Penetration testing mendalam |
| Performance | Daftar outlet load < 3 detik | Load testing skala besar |
| Usability | Form error jelas, validasi koordinat terbaca | A/B testing |

### 1.2 Risk-Based Priority

| Risk Area | Impact | Likelihood | Test Priority |
|-----------|--------|------------|---------------|
| Koordinat GPS tidak valid diterima sistem | High | Medium | P1 |
| Outlet nonaktif masih muncul di Route Planning | High | Medium | P1 |
| User non-Admin dapat mengakses halaman ini | High | Medium | P1 |
| Outlet baru tidak tersedia di Route Planning | High | Low | P1 |
| Data historis kunjungan terhapus saat nonaktifkan outlet | High | Low | P1 |

### 1.3 Environment Requirements

| Environment | Purpose |
|-------------|---------|
| Staging | QA testing & regression |
| UAT | Client acceptance testing |

---

## 2. Acceptance Criteria

### US-01: Membuat Outlet Baru

```gherkin
Feature: [FEAT-002-US-01] Membuat Outlet Baru
  As Admin
  I want menambahkan outlet baru ke dalam sistem beserta koordinat GPS-nya
  So that Sales dapat menemukan dan mengunjungi outlet tersebut

  Background:
    Given Admin sudah login ke Admin Panel
    And Admin berada di halaman Manajemen Outlet

  @happy-path
  Scenario: Admin berhasil membuat outlet baru dengan koordinat valid
    When Admin mengisi form dengan nama "Toko Maju", alamat "Jl. Raya No. 1, Jakarta", latitude "-6.2088", longitude "106.8456"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan sukses "Outlet berhasil ditambahkan"
    And outlet "Toko Maju" muncul di daftar dengan status "Aktif"
    And outlet "Toko Maju" tersedia sebagai tujuan kunjungan di fitur Route Planning

  @validation
  Scenario: Admin mencoba menyimpan outlet tanpa koordinat GPS
    When Admin mengisi nama dan alamat tapi tidak mengisi latitude dan longitude
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "Koordinat GPS (latitude & longitude) wajib diisi"
    And outlet tidak tersimpan

  @validation
  Scenario: Admin mengisi koordinat GPS dengan format yang tidak valid
    When Admin mengisi latitude "abc" dan longitude "xyz"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "Format koordinat tidak valid. Gunakan angka desimal (contoh: -6.2088)"
    And outlet tidak tersimpan

  @validation
  Scenario: Admin mencoba menyimpan form tanpa nama outlet
    When Admin mengisi koordinat tapi mengosongkan field nama
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "Nama outlet wajib diisi"

  @edge-case
  Scenario: Admin membuat outlet dengan koordinat di luar batas umum (nilai ekstrem valid)
    When Admin mengisi latitude "90" dan longitude "180" (nilai batas maksimum valid)
    And Admin menekan tombol "Simpan"
    Then sistem menerima koordinat dan outlet berhasil disimpan
```

---

### US-02: Melihat Daftar Outlet

```gherkin
Feature: [FEAT-002-US-02] Melihat Daftar Outlet
  As Admin
  I want melihat semua outlet yang terdaftar beserta status dan data kuncinya
  So that saya dapat memantau kelengkapan data outlet

  Background:
    Given Admin sudah login ke Admin Panel
    And tersedia setidaknya 5 outlet di sistem

  @happy-path
  Scenario: Admin melihat daftar outlet
    When Admin membuka halaman Manajemen Outlet
    Then halaman menampilkan tabel dengan kolom nama, alamat, status, dan indikator GPS

  @happy-path
  Scenario: Admin mencari outlet berdasarkan nama
    When Admin mengetik "Toko Maju" di field pencarian
    Then hanya outlet yang mengandung "Toko Maju" di nama atau alamat yang ditampilkan

  @happy-path
  Scenario: Admin memfilter outlet berdasarkan status
    When Admin memilih filter "Nonaktif"
    Then hanya outlet dengan status "Nonaktif" yang ditampilkan

  @edge-case
  Scenario: Daftar outlet kosong
    Given belum ada outlet yang dibuat
    When Admin membuka halaman Manajemen Outlet
    Then halaman menampilkan pesan "Belum ada outlet. Tambah outlet pertama Anda."
```

---

### US-03: Mengedit Data Outlet

```gherkin
Feature: [FEAT-002-US-03] Mengedit Data Outlet
  As Admin
  I want mengubah data outlet yang sudah ada termasuk memperbaiki koordinat GPS
  So that data outlet selalu akurat

  Background:
    Given Admin sudah login ke Admin Panel
    And ada outlet "Toko Maju" dengan koordinat GPS yang tersimpan

  @happy-path
  Scenario: Admin berhasil mengubah koordinat GPS outlet
    When Admin membuka detail outlet "Toko Maju"
    And Admin mengubah latitude menjadi "-6.2500" dan longitude menjadi "106.9000"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan sukses "Outlet berhasil diperbarui"
    And koordinat baru berlaku untuk perhitungan rute di sesi Sales berikutnya

  @happy-path
  Scenario: Admin berhasil mengubah nama outlet
    When Admin mengubah nama "Toko Maju" menjadi "Toko Maju Jaya"
    And Admin menekan tombol "Simpan"
    Then nama outlet berubah menjadi "Toko Maju Jaya" di daftar

  @validation
  Scenario: Admin menghapus koordinat GPS saat mengedit
    When Admin mengosongkan field latitude
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "Koordinat GPS wajib diisi"
    And perubahan tidak tersimpan
```

---

### US-04: Menonaktifkan / Mengaktifkan Outlet

```gherkin
Feature: [FEAT-002-US-04] Menonaktifkan dan Mengaktifkan Outlet
  As Admin
  I want menonaktifkan outlet yang tidak relevan atau mengaktifkan kembali outlet nonaktif
  So that Sales hanya melihat outlet aktif di Route Planning

  Background:
    Given Admin sudah login ke Admin Panel

  @happy-path
  Scenario: Admin berhasil menonaktifkan outlet
    Given ada outlet aktif "Toko Maju"
    When Admin menekan tombol "Nonaktifkan" pada outlet "Toko Maju"
    And Admin mengonfirmasi tindakan
    Then status outlet "Toko Maju" berubah menjadi "Nonaktif"
    And outlet "Toko Maju" tidak muncul sebagai pilihan di fitur Route Planning Sales

  @happy-path
  Scenario: Admin mengaktifkan kembali outlet nonaktif
    Given ada outlet nonaktif "Toko Maju"
    When Admin menekan tombol "Aktifkan" pada outlet "Toko Maju"
    Then status outlet berubah menjadi "Aktif"
    And outlet "Toko Maju" kembali tersedia di Route Planning

  @edge-case
  Scenario: Riwayat kunjungan ke outlet nonaktif tetap tersedia
    Given outlet "Toko Maju" telah dinonaktifkan
    And ada riwayat kunjungan ke "Toko Maju" sebelum dinonaktifkan
    When Admin atau Manager membuka laporan kunjungan
    Then riwayat kunjungan ke "Toko Maju" masih tampil di laporan
```

---

## 3. Validation Rules

| Field | Rule | Pesan Error |
|-------|------|-------------|
| Nama outlet | Wajib diisi, maks 150 karakter | "Nama outlet wajib diisi" |
| Alamat | Wajib diisi | "Alamat wajib diisi" |
| Latitude | Wajib diisi, angka desimal, -90 s/d 90 | "Format latitude tidak valid" |
| Longitude | Wajib diisi, angka desimal, -180 s/d 180 | "Format longitude tidak valid" |
| Nama kontak | Opsional, maks 100 karakter | — |
| Nomor telepon | Opsional, format angka/tanda baca standar | "Format nomor telepon tidak valid" |

---

## 4. Test Cases

### 4.1 Functional Tests

| ID | Skenario | Preconditions | Steps | Expected Result | Priority |
|----|----------|---------------|-------|-----------------|----------|
| TC-002-001 | Buat outlet baru berhasil | Login Admin | Isi form lengkap + GPS > Simpan | Outlet muncul di daftar dan Route Planning | P1 |
| TC-002-002 | Buat outlet tanpa GPS | Login Admin | Isi form tanpa lat/lng > Simpan | Error GPS wajib diisi | P1 |
| TC-002-003 | Format GPS tidak valid | Login Admin | Isi GPS "abc" > Simpan | Error format koordinat | P1 |
| TC-002-004 | Lihat daftar outlet | Minimal 1 outlet | Buka halaman | Tabel tampil dengan data benar | P1 |
| TC-002-005 | Cari outlet by nama | Ada outlet | Ketik nama di pencarian | Hanya outlet cocok tampil | P2 |
| TC-002-006 | Filter by status | Ada outlet aktif & nonaktif | Pilih filter | Hanya outlet sesuai filter tampil | P2 |
| TC-002-007 | Edit koordinat GPS | Ada outlet | Ubah lat/lng > Simpan | Koordinat baru tersimpan | P1 |
| TC-002-008 | Edit - hapus GPS saat edit | Ada outlet | Kosongkan lat > Simpan | Error GPS wajib diisi | P1 |
| TC-002-009 | Nonaktifkan outlet | Ada outlet aktif | Nonaktifkan > Konfirmasi | Status Nonaktif, hilang dari Route Planning | P1 |
| TC-002-010 | Aktifkan outlet | Ada outlet nonaktif | Aktifkan | Status Aktif, muncul di Route Planning | P1 |
| TC-002-011 | Riwayat kunjungan terjaga setelah nonaktifkan | Ada riwayat kunjungan | Nonaktifkan outlet > cek laporan | Riwayat masih ada di laporan | P1 |

### 4.2 Security Tests

| ID | Skenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-002-S01 | Akses halaman tanpa login | Buka URL langsung | Redirect ke halaman login | P1 |
| TC-002-S02 | Akses halaman sebagai Sales | Login Sales > buka URL | Ditolak, tidak punya akses | P1 |

### 4.3 Usability Tests

| ID | Skenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-002-U01 | Konfirmasi sebelum nonaktifkan outlet | Klik Nonaktifkan | Muncul dialog konfirmasi | P2 |
| TC-002-U02 | Indikator GPS di daftar outlet | Lihat daftar | Outlet tanpa GPS teridentifikasi jelas | P2 |

---

## 5. Test Data

### 5.1 Test Accounts

| Account | Role | Purpose |
|---------|------|---------|
| admin@rutego.com | Admin | Executor semua test |
| sales@rutego.com | Sales | Verifikasi akses ditolak |

### 5.2 Test Data Sets

| Dataset | Description |
|---------|-------------|
| Outlet seed set | 10 outlet: 7 aktif (semua punya GPS), 3 nonaktif |
| Outlet tanpa GPS | 2 outlet tanpa koordinat (untuk edge case) |

---

## 6. Automation Plan

### 6.1 Coverage

| Category | Total | Automated | Manual |
|----------|-------|-----------|--------|
| Functional | 11 | 9 | 2 |
| Security | 2 | 2 | 0 |
| Usability | 2 | 0 | 2 |

### 6.2 Playwright Mapping

| Test Case | Playwright File | Page Object |
|-----------|-----------------|-------------|
| TC-002-001 hingga TC-002-011 | `test/web/features/outlet-management.spec.ts` | `test/web/pages/outlet-management.page.ts` |
| TC-002-S01, TC-002-S02 | `test/web/features/outlet-management-security.spec.ts` | `test/web/pages/outlet-management.page.ts` |

---

## 7. UAT Scenarios

### UAT Scenario 01: Menambahkan Outlet Baru

**Objective:** Memastikan Admin dapat menambahkan outlet baru dan outlet tersedia di Route Planning

**Prerequisites:**
- Login sebagai Admin
- Buka halaman Manajemen Outlet

**Steps:**
1. Klik tombol "Tambah Outlet"
2. Isi nama outlet, alamat lengkap, latitude, dan longitude
3. Klik "Simpan"
4. Verifikasi outlet muncul di daftar dengan status Aktif

**Expected Results:**
- Outlet baru tampil di daftar dengan indikator GPS terisi
- Outlet tersedia sebagai tujuan kunjungan di modul Route Planning

**Status:** [ ] Pass  [ ] Fail

---

### UAT Scenario 02: Menonaktifkan Outlet

**Objective:** Memastikan outlet nonaktif tidak muncul sebagai pilihan kunjungan Sales

**Prerequisites:**
- Login sebagai Admin
- Ada outlet aktif yang siap dinonaktifkan
- Akun Sales tersedia untuk verifikasi

**Steps:**
1. Temukan outlet di daftar
2. Klik tombol "Nonaktifkan" dan konfirmasi
3. Verifikasi status berubah menjadi "Nonaktif"
4. Login sebagai Sales, buka fitur Route Planning
5. Verifikasi outlet yang dinonaktifkan tidak tersedia

**Expected Results:**
- Status outlet berubah menjadi "Nonaktif"
- Outlet tidak muncul di pilihan tujuan kunjungan Sales

**Status:** [ ] Pass  [ ] Fail

---

## 8. Sign-off

| Role | Name | Date | Status |
|------|------|------|--------|
| QA Lead | | | Pending |
| PM | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-admin-panel/02-outlet-management---testing.md

RELATED FILES:
- 02-outlet-management---business.md (PM: user stories, business rules)
- 02-outlet-management---technical.md (Dev: API, data model)
- 02-outlet-management---design.md (Design: wireframes)
-->
