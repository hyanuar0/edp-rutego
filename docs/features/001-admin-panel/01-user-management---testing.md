# TESTING SPECIFICATION
## [FEAT-001] Manajemen User & Role

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-001 |
| Feature Name | Manajemen User & Role |
| Epic | EPIC-001: Admin Panel |
| Business Spec | `01-user-management---business.md` |
| Status | Draft |
| Owner | QA Engineer |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |

---

## 1. Test Strategy

### 1.1 Scope of Testing

| Type | In Scope | Out of Scope |
|------|----------|--------------|
| Functional | CRUD user, assign role, nonaktifkan/aktifkan user | Forgot password flow, audit log |
| Security | Akses halaman hanya untuk Admin, tidak bisa bypass role | Penetration testing mendalam |
| Performance | Daftar user load < 3 detik | Load testing skala besar |
| Usability | Form error message jelas, navigasi intuitif | A/B testing |

### 1.2 Risk-Based Priority

| Risk Area | Impact | Likelihood | Test Priority |
|-----------|--------|------------|---------------|
| Email duplikat diterima sistem | High | Medium | P1 |
| Admin terakhir berhasil dinonaktifkan | High | Low | P1 |
| User non-Admin dapat mengakses halaman ini | High | Medium | P1 |
| Perubahan role tidak berlaku segera | High | Medium | P1 |
| Daftar user tidak muncul/loading gagal | High | Low | P1 |

### 1.3 Environment Requirements

| Environment | Purpose |
|-------------|---------|
| Staging | QA testing & regression |
| UAT | Client acceptance testing |

---

## 2. Acceptance Criteria

### US-01: Membuat User Baru

```gherkin
Feature: [FEAT-001-US-01] Membuat User Baru
  As Admin
  I want membuat akun user baru dengan data lengkap dan role yang sesuai
  So that user dapat langsung login dan mengakses sistem sesuai perannya

  Background:
    Given Admin sudah login ke Admin Panel
    And Admin berada di halaman Manajemen User

  @happy-path
  Scenario: Admin berhasil membuat user baru
    Given belum ada user dengan email "budi@rutego.com"
    When Admin mengisi form dengan nama "Budi Santoso", email "budi@rutego.com", password "TempPass123!", dan role "Sales"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan sukses "User berhasil dibuat"
    And user "Budi Santoso" muncul di daftar user dengan status "Aktif" dan role "Sales"
    And user "Budi Santoso" dapat login menggunakan email dan password tersebut

  @validation
  Scenario: Admin mencoba membuat user dengan email yang sudah digunakan
    Given sudah ada user dengan email "budi@rutego.com"
    When Admin mengisi form dengan email "budi@rutego.com"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "Email sudah digunakan oleh user lain"
    And user baru tidak tersimpan di sistem

  @validation
  Scenario: Admin mencoba menyimpan form dengan field wajib kosong
    Given Admin membuka form tambah user baru
    When Admin menekan tombol "Simpan" tanpa mengisi field apapun
    Then sistem menampilkan pesan error pada setiap field wajib yang kosong
    And form tidak tersimpan

  @validation
  Scenario: Admin tidak memilih role
    When Admin mengisi nama dan email tapi tidak memilih role
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "Role wajib dipilih"

  @edge-case
  Scenario: Admin membuat user dengan role Admin
    When Admin mengisi form dengan role "Admin"
    And Admin menekan tombol "Simpan"
    Then user baru berhasil dibuat dengan role "Admin"
    And user baru dapat mengakses halaman Admin Panel setelah login
```

---

### US-02: Melihat Daftar User

```gherkin
Feature: [FEAT-001-US-02] Melihat Daftar User
  As Admin
  I want melihat semua user yang terdaftar beserta status dan role-nya
  So that saya dapat memantau dan mengelola akses pengguna secara keseluruhan

  Background:
    Given Admin sudah login ke Admin Panel
    And tersedia setidaknya 5 user di sistem

  @happy-path
  Scenario: Admin melihat daftar user
    When Admin membuka halaman Manajemen User
    Then halaman menampilkan daftar user dalam format tabel
    And setiap baris menampilkan nama, email, role, dan status user

  @happy-path
  Scenario: Admin mencari user berdasarkan nama
    When Admin mengetik "Budi" di field pencarian
    Then daftar user menampilkan hanya user yang mengandung kata "Budi" di nama atau email

  @happy-path
  Scenario: Admin memfilter user berdasarkan role
    When Admin memilih filter role "Sales"
    Then daftar menampilkan hanya user dengan role "Sales"

  @happy-path
  Scenario: Admin memfilter user berdasarkan status
    When Admin memilih filter status "Nonaktif"
    Then daftar menampilkan hanya user dengan status "Nonaktif"

  @edge-case
  Scenario: Pencarian tidak menemukan hasil
    When Admin mencari dengan kata kunci yang tidak ada di sistem
    Then halaman menampilkan pesan "Tidak ada user ditemukan"
```

---

### US-03: Mengedit Data User

```gherkin
Feature: [FEAT-001-US-03] Mengedit Data User
  As Admin
  I want mengubah data user yang sudah ada
  So that informasi user selalu akurat dan role sesuai tanggung jawab terkini

  Background:
    Given Admin sudah login ke Admin Panel
    And sudah ada user "Budi Santoso" dengan email "budi@rutego.com" dan role "Sales"

  @happy-path
  Scenario: Admin berhasil mengubah nama user
    When Admin membuka detail user "Budi Santoso"
    And Admin mengubah nama menjadi "Budi Santoso Wijaya"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan sukses "Data user berhasil diperbarui"
    And nama user di daftar berubah menjadi "Budi Santoso Wijaya"

  @happy-path
  Scenario: Admin berhasil mengubah role user
    When Admin membuka detail user "Budi Santoso"
    And Admin mengubah role menjadi "Manager"
    And Admin menekan tombol "Simpan"
    Then role user berubah menjadi "Manager"
    And pada sesi berikutnya, user memiliki hak akses sesuai role "Manager"

  @validation
  Scenario: Admin mengubah email dengan email yang sudah digunakan user lain
    Given sudah ada user lain dengan email "diana@rutego.com"
    When Admin mengubah email "Budi Santoso" menjadi "diana@rutego.com"
    And Admin menekan tombol "Simpan"
    Then sistem menampilkan pesan error "Email sudah digunakan oleh user lain"
    And perubahan tidak tersimpan
```

---

### US-04: Menonaktifkan / Mengaktifkan User

```gherkin
Feature: [FEAT-001-US-04] Menonaktifkan dan Mengaktifkan User
  As Admin
  I want menonaktifkan atau mengaktifkan kembali akun user
  So that user yang tidak aktif tidak dapat mengakses sistem namun data historisnya terjaga

  Background:
    Given Admin sudah login ke Admin Panel

  @happy-path
  Scenario: Admin berhasil menonaktifkan user
    Given ada user aktif "Budi Santoso"
    When Admin menekan tombol "Nonaktifkan" pada user "Budi Santoso"
    And Admin mengonfirmasi tindakan
    Then status user "Budi Santoso" berubah menjadi "Nonaktif"
    And user "Budi Santoso" tidak dapat login ke sistem

  @happy-path
  Scenario: Admin mengaktifkan kembali user yang nonaktif
    Given ada user nonaktif "Budi Santoso"
    When Admin menekan tombol "Aktifkan" pada user "Budi Santoso"
    Then status user berubah menjadi "Aktif"
    And user "Budi Santoso" dapat login kembali ke sistem

  @security
  Scenario: User yang dinonaktifkan mencoba login
    Given user "Budi Santoso" berstatus "Nonaktif"
    When user "Budi Santoso" mencoba login dengan email dan password yang benar
    Then sistem menampilkan pesan error "Akun Anda telah dinonaktifkan. Hubungi Admin."
    And user tidak berhasil masuk ke sistem

  @edge-case
  Scenario: Riwayat aktivitas user nonaktif tetap tersedia
    Given user "Budi Santoso" telah dinonaktifkan
    When Admin mencari data historis aktivitas "Budi Santoso"
    Then riwayat aktivitas "Budi Santoso" masih dapat dilihat oleh Admin

  @edge-case
  Scenario: Admin mencoba menonaktifkan Admin terakhir yang aktif
    Given hanya ada satu user dengan role "Admin" yang berstatus "Aktif"
    When Admin mencoba menonaktifkan user Admin tersebut
    Then sistem menolak tindakan dan menampilkan pesan "Minimal satu Admin aktif harus ada di sistem"
    And status user Admin tidak berubah
```

---

### US-05: Mengubah Role User

```gherkin
Feature: [FEAT-001-US-05] Mengubah Role User
  As Admin
  I want mengubah role user yang sudah ada
  So that hak akses user sesuai perubahan tanggung jawab

  Background:
    Given Admin sudah login ke Admin Panel

  @happy-path
  Scenario: Admin berhasil mengubah role dari Sales ke Manager
    Given ada user "Budi Santoso" dengan role "Sales"
    When Admin mengubah role menjadi "Manager" dan menyimpan
    Then role user berubah menjadi "Manager"
    And pada login berikutnya user memiliki akses fitur Manager

  @edge-case
  Scenario: Admin tidak dapat menghapus role Admin terakhir yang aktif via perubahan role
    Given hanya ada satu user Admin aktif di sistem
    When Admin mencoba mengubah role user Admin tersebut menjadi "Sales"
    Then sistem menampilkan pesan error "Minimal satu Admin aktif harus ada di sistem"
    And role tidak berubah
```

---

## 3. Validation Rules

| Field | Rule | Pesan Error |
|-------|------|-------------|
| Nama | Wajib diisi, maks 100 karakter | "Nama wajib diisi" |
| Email | Wajib diisi, format email valid, unik di sistem | "Email tidak valid" / "Email sudah digunakan" |
| Password | Wajib diisi, minimal 8 karakter | "Password minimal 8 karakter" |
| Role | Wajib dipilih (Admin / Manager / Sales) | "Role wajib dipilih" |

---

## 4. Test Cases

### 4.1 Functional Tests

| ID | Skenario | Preconditions | Steps | Expected Result | Priority |
|----|----------|---------------|-------|-----------------|----------|
| TC-001-001 | Buat user baru berhasil | Login sebagai Admin | Isi form lengkap > Simpan | User muncul di daftar, status Aktif | P1 |
| TC-001-002 | Email duplikat ditolak | Ada user dengan email sama | Isi form dengan email yang sama > Simpan | Error "Email sudah digunakan" | P1 |
| TC-001-003 | Form kosong ditolak | Form edit terbuka | Klik Simpan tanpa isi apapun | Error pada semua field wajib | P1 |
| TC-001-004 | Lihat daftar user | Minimal 1 user ada | Buka halaman Manajemen User | Tabel tampil dengan data benar | P1 |
| TC-001-005 | Cari user by nama | Ada user bernama "Budi" | Ketik "Budi" di pencarian | Hanya user "Budi" tampil | P2 |
| TC-001-006 | Filter by role | Ada user berbagai role | Pilih filter "Sales" | Hanya user Sales tampil | P2 |
| TC-001-007 | Edit nama user | Ada user aktif | Edit nama > Simpan | Nama berubah di daftar | P1 |
| TC-001-008 | Edit email duplikat | Ada 2 user | Ubah email ke email user lain | Error email sudah digunakan | P1 |
| TC-001-009 | Nonaktifkan user | Ada user aktif | Klik Nonaktifkan > Konfirmasi | Status jadi Nonaktif, tidak bisa login | P1 |
| TC-001-010 | Aktifkan user | Ada user nonaktif | Klik Aktifkan | Status jadi Aktif, bisa login | P1 |
| TC-001-011 | Nonaktifkan Admin terakhir | 1 Admin aktif tersisa | Coba nonaktifkan | Ditolak sistem | P1 |
| TC-001-012 | Ubah role berhasil | Ada user Sales | Ubah role ke Manager | Role berubah, akses menyesuaikan | P1 |

### 4.2 Security Tests

| ID | Skenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-001-S01 | Akses halaman tanpa login | Buka URL halaman langsung | Redirect ke halaman login | P1 |
| TC-001-S02 | Akses halaman sebagai Sales | Login sebagai Sales > buka URL halaman | Redirect / tampil pesan tidak punya akses | P1 |
| TC-001-S03 | Akses halaman sebagai Manager | Login sebagai Manager > buka URL halaman | Redirect / tampil pesan tidak punya akses | P1 |

### 4.3 Usability Tests

| ID | Skenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-001-U01 | Pesan error jelas saat validasi gagal | Submit form dengan data salah | Setiap field error diberi keterangan yang mudah dipahami | P2 |
| TC-001-U02 | Konfirmasi sebelum nonaktifkan user | Klik Nonaktifkan | Muncul dialog konfirmasi sebelum eksekusi | P2 |

---

## 5. Test Data

### 5.1 Test Accounts

| Account | Role | Purpose |
|---------|------|---------|
| admin@rutego.com | Admin | Executor semua test |
| sales@rutego.com | Sales | Verifikasi akses ditolak |
| manager@rutego.com | Manager | Verifikasi akses ditolak |

### 5.2 Test Data Sets

| Dataset | Description |
|---------|-------------|
| User seed set | Minimal 5 user dengan variasi role dan status |
| Admin-only seed | Hanya 1 Admin aktif (untuk test BR-03) |

---

## 6. Automation Plan

### 6.1 Coverage

| Category | Total | Automated | Manual |
|----------|-------|-----------|--------|
| Functional | 12 | 10 | 2 |
| Security | 3 | 3 | 0 |
| Usability | 2 | 0 | 2 |

### 6.2 Playwright Mapping

| Test Case | Playwright File | Page Object |
|-----------|-----------------|-------------|
| TC-001-001 hingga TC-001-012 | `test/web/features/user-management.spec.ts` | `test/web/pages/user-management.page.ts` |
| TC-001-S01 hingga TC-001-S03 | `test/web/features/user-management-security.spec.ts` | `test/web/pages/user-management.page.ts` |

---

## 7. UAT Scenarios

### UAT Scenario 01: Menambahkan User Baru

**Objective:** Memastikan Admin dapat membuat akun user baru dan user tersebut dapat langsung login

**Prerequisites:**
- Login sebagai Admin
- Buka halaman Manajemen User

**Steps:**
1. Klik tombol "Tambah User"
2. Isi nama, email, password sementara, dan pilih role "Sales"
3. Klik "Simpan"
4. Verifikasi user muncul di daftar
5. Login menggunakan akun yang baru dibuat

**Expected Results:**
- User baru tampil di daftar dengan status Aktif
- Login berhasil menggunakan akun baru

**Status:** [ ] Pass  [ ] Fail

---

### UAT Scenario 02: Menonaktifkan User

**Objective:** Memastikan user yang dinonaktifkan tidak dapat login

**Prerequisites:**
- Login sebagai Admin
- Ada user aktif yang siap dinonaktifkan

**Steps:**
1. Temukan user di daftar
2. Klik tombol "Nonaktifkan"
3. Konfirmasi tindakan
4. Verifikasi status berubah menjadi "Nonaktif"
5. Coba login menggunakan akun yang dinonaktifkan

**Expected Results:**
- Status user berubah menjadi "Nonaktif"
- Login dengan akun tersebut ditolak dengan pesan yang jelas

**Status:** [ ] Pass  [ ] Fail

---

### UAT Scenario 03: Mengubah Role User

**Objective:** Memastikan perubahan role user berlaku dan memengaruhi hak akses

**Prerequisites:**
- Login sebagai Admin
- Ada user dengan role "Sales"

**Steps:**
1. Buka detail user
2. Ubah role menjadi "Manager"
3. Simpan perubahan
4. Login menggunakan akun user tersebut
5. Verifikasi tampilan/fitur sesuai role Manager

**Expected Results:**
- Role berubah menjadi Manager
- User memiliki akses fitur sesuai role Manager

**Status:** [ ] Pass  [ ] Fail

---

## 8. Sign-off

| Role | Name | Date | Status |
|------|------|------|--------|
| QA Lead | | | Pending |
| PM | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-admin-panel/01-user-management---testing.md

RELATED FILES:
- 01-user-management---business.md (PM: user stories, business rules)
- 01-user-management---technical.md (Dev: API, data model)
- 01-user-management---design.md (Design: wireframes)
-->
