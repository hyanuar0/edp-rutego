# BUSINESS SPECIFICATION
## [FEAT-001] Manajemen User & Role

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-001 |
| Feature Name | Manajemen User & Role |
| Epic | EPIC-001: Admin Panel |
| Priority | Must Have |
| Status | Draft |
| Owner | Product Manager |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Taiga Sync | Synced (US #2) |

---

## 1. Feature Overview

### 1.1 Business Context

**Problem:**
> Admin saat ini tidak memiliki alat untuk mengelola akun pengguna secara mandiri. Penambahan, perubahan, atau penonaktifan user memerlukan intervensi developer langsung, yang menyebabkan keterlambatan operasional dan risiko keamanan.

**Solution:**
> Menyediakan halaman manajemen user di Admin Panel yang memungkinkan Admin untuk membuat, melihat, mengedit, menonaktifkan, dan menetapkan role kepada user tanpa ketergantungan pada tim teknis.

### 1.2 Target Users

| User Role | Primary/Secondary | Needs |
|-----------|-------------------|-------|
| Admin | Primary | Kelola akun user (buat, ubah, nonaktifkan) dan tetapkan role |

### 1.3 Success Metrics

| Metric | Current | Target | Measurement |
|--------|---------|--------|-------------|
| Waktu onboarding user baru | Manual (dev dependent) | < 5 menit per user | Durasi dari request hingga akun aktif |
| Perubahan role user | Manual (dev dependent) | < 2 menit | Durasi eksekusi perubahan di Admin Panel |
| Error akses user (akses tidak sesuai role) | Tidak terukur | 0 insiden pasca go-live | Laporan incident monitoring |

---

## 2. Scope

### 2.1 In Scope

| # | Capability | Description |
|---|------------|-------------|
| 1 | Buat user baru | Admin mengisi data user (nama, email, password, role) dan menyimpannya |
| 2 | Lihat daftar user | Admin melihat semua user terdaftar dengan filter dan pencarian |
| 3 | Edit data user | Admin mengubah nama, email, atau role user yang sudah ada |
| 4 | Nonaktifkan / aktifkan user | Admin menangguhkan akses user tanpa menghapus data |
| 5 | Assign / ubah role | Admin menetapkan role (Admin, Manager, Sales) ke user |

### 2.2 Out of Scope

| # | Item | Reason |
|---|------|--------|
| 1 | Self-registration oleh user | Hanya Admin yang dapat membuat akun |
| 2 | Reset password oleh user sendiri (forgot password flow) | Ditangani terpisah di fitur autentikasi |
| 3 | Pengelolaan permission granular per fitur | Cukup RBAC berbasis 3 role: Admin, Manager, Sales |
| 4 | Audit log aktivitas user | Out of scope MVP 1 |

---

## 3. User Stories

### US-01: Membuat User Baru

> **As** Admin
> **I want** membuat akun user baru dengan data lengkap dan role yang sesuai
> **So that** user dapat langsung login dan mengakses sistem sesuai perannya

**Expected Behavior / Acceptance Criteria:**
- Admin mengisi form dengan: nama lengkap, email (unik), password sementara, dan role (Admin / Manager / Sales)
- Sistem memvalidasi bahwa email belum digunakan; jika sudah ada, tampilkan pesan error yang jelas
- Setelah disimpan, user baru muncul di daftar user dengan status "Aktif"
- User baru dapat langsung login menggunakan email dan password sementara yang ditetapkan Admin
- Jika data tidak lengkap atau tidak valid, form menampilkan pesan error pada field terkait dan tidak menyimpan data

---

### US-02: Melihat Daftar User

> **As** Admin
> **I want** melihat semua user yang terdaftar di sistem beserta status dan role-nya
> **So that** saya dapat memantau dan mengelola akses pengguna secara keseluruhan

**Expected Behavior / Acceptance Criteria:**
- Halaman menampilkan daftar user dalam format tabel dengan kolom: nama, email, role, status (Aktif/Nonaktif)
- Admin dapat mencari user berdasarkan nama atau email
- Admin dapat memfilter daftar berdasarkan role dan/atau status
- Daftar mendukung paginasi jika jumlah user melebihi batas tampilan satu halaman

---

### US-03: Mengedit Data User

> **As** Admin
> **I want** mengubah data user yang sudah ada (nama, email, role)
> **So that** informasi user selalu akurat dan role sesuai tanggung jawab terkini

**Expected Behavior / Acceptance Criteria:**
- Admin memilih user dari daftar dan membuka form edit
- Admin dapat mengubah nama, email, dan/atau role
- Sistem memvalidasi email tidak bentrok dengan user lain; jika bentrok, tampilkan pesan error
- Setelah disimpan, perubahan langsung tercermin di daftar user dan berlaku untuk sesi user berikutnya
- Perubahan role berlaku segera pada hak akses user di seluruh sistem

---

### US-04: Menonaktifkan / Mengaktifkan User

> **As** Admin
> **I want** menonaktifkan atau mengaktifkan kembali akun user
> **So that** user yang sudah tidak aktif tidak dapat mengakses sistem, namun data historisnya tetap terjaga

**Expected Behavior / Acceptance Criteria:**
- Admin dapat menonaktifkan user aktif dari halaman daftar atau detail user
- User yang dinonaktifkan tidak dapat login; sesi aktif yang sedang berjalan segera dihentikan
- Data dan riwayat user yang dinonaktifkan tetap tersimpan dan dapat dilihat Admin
- Admin dapat mengaktifkan kembali user yang nonaktif; user tersebut dapat login kembali setelah diaktifkan
- Status user (Aktif/Nonaktif) ditampilkan dengan jelas di daftar user

---

### US-05: Mengubah Role User

> **As** Admin
> **I want** mengubah role user yang sudah ada
> **So that** hak akses user sesuai dengan perubahan tanggung jawab atau struktur organisasi

**Expected Behavior / Acceptance Criteria:**
- Admin dapat mengubah role user melalui form edit user
- Role yang tersedia: Admin, Manager, Sales
- Perubahan role berlaku segera untuk sesi berikutnya user yang bersangkutan
- Admin tidak dapat mengubah role akun Admin terakhir yang aktif (mencegah sistem tanpa Admin)

---

## 4. Business Rules

| Rule ID | Rule | Description |
|---------|------|-------------|
| BR-01 | Email unik | Setiap user harus memiliki email yang unik di seluruh sistem |
| BR-02 | Role wajib | Setiap user harus memiliki tepat satu role (Admin, Manager, atau Sales) |
| BR-03 | Minimal satu Admin aktif | Sistem harus selalu memiliki minimal satu user dengan role Admin yang berstatus aktif |
| BR-04 | Hanya Admin yang membuat user | User tidak dapat mendaftar sendiri; hanya Admin yang dapat membuat akun baru |
| BR-05 | Data historis terjaga | Menonaktifkan user tidak menghapus data; seluruh riwayat aktivitas tetap tersimpan |

---

## 5. Open Questions

| # | Question | Asked To | Status | Answer |
|---|----------|----------|--------|--------|
| 1 | Apakah Admin perlu menerima notifikasi email saat akun user baru dibuat? | PM / Tech Lead | Answered | Tidak perlu |
| 2 | Apakah user baru harus mengganti password sementara saat pertama login? | PM | Answered | Tidak perlu |
| 3 | Berapa batas maksimum jumlah user dalam sistem (untuk pertimbangan paginasi)? | Tech Lead | Answered | Default 10 per halaman; user dapat mengubah jumlah tampilan via dropdown |

---

## 6. Definition of Done

- [ ] Testing spec created (QA) — `01-user-management---testing.md`
- [ ] Technical spec created (Dev) — `01-user-management---technical.md`
- [ ] Design spec created (Designer) — `01-user-management---design.md`
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
FILE LOCATION: docs/features/001-admin-panel/01-user-management---business.md

RELATED FILES:
- 01-user-management---testing.md (QA: Gherkin AC, test cases, validation rules)
- 01-user-management---technical.md (Dev: API, data model, implementation)
- 01-user-management---design.md (Design: UI/UX specifications)
- 00-admin-panel---overview.md (Epic context)
-->
