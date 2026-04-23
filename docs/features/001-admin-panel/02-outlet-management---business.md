# BUSINESS SPECIFICATION
## [FEAT-002] Manajemen Outlet

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-002 |
| Feature Name | Manajemen Outlet |
| Epic | EPIC-001: Admin Panel |
| Priority | Must Have |
| Status | Draft |
| Owner | Product Manager |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Taiga Sync | Synced (US #3) |

---

## 1. Feature Overview

### 1.1 Business Context

**Problem:**
> Data outlet (toko/gerai tujuan kunjungan sales) saat ini dikelola secara manual dan tersebar, menyebabkan inkonsistensi data. Sales tidak memiliki referensi outlet yang akurat, dan kualitas optimasi rute sangat bergantung pada ketersediaan koordinat GPS outlet yang valid.

**Solution:**
> Menyediakan halaman manajemen outlet di Admin Panel yang memungkinkan Admin untuk membuat, melihat, mengedit, dan menonaktifkan data outlet lengkap dengan koordinat GPS, sehingga Sales selalu bekerja dengan data referensi yang akurat.

### 1.2 Target Users

| User Role | Primary/Secondary | Needs |
|-----------|-------------------|-------|
| Admin | Primary | Kelola data outlet (buat, ubah, nonaktifkan) termasuk koordinat GPS |
| Sales | Secondary | Menggunakan data outlet sebagai tujuan kunjungan pada fitur Route Planning |

### 1.3 Success Metrics

| Metric | Current | Target | Measurement |
|--------|---------|--------|-------------|
| Kelengkapan data outlet (termasuk koordinat GPS) | Tidak terukur | ≥95% outlet memiliki koordinat GPS valid sebelum go-live | Persentase outlet dengan lat/lng terisi |
| Waktu penambahan outlet baru | Manual (tidak terstandar) | < 3 menit per outlet | Durasi pengisian form hingga data tersimpan |
| Akurasi data outlet (validitas GPS) | Tidak terukur | 0 kunjungan gagal karena data outlet tidak valid | Laporan error kunjungan terkait data outlet |

---

## 2. Scope

### 2.1 In Scope

| # | Capability | Description |
|---|------------|-------------|
| 1 | Buat outlet baru | Admin mengisi data outlet (nama, alamat, koordinat GPS, kontak) |
| 2 | Lihat daftar outlet | Admin melihat semua outlet dengan filter dan pencarian |
| 3 | Edit data outlet | Admin mengubah data outlet yang sudah ada |
| 4 | Nonaktifkan / aktifkan outlet | Admin menonaktifkan outlet yang sudah tidak aktif secara bisnis |
| 5 | Input koordinat GPS | Admin memasukkan latitude/longitude untuk setiap outlet |

### 2.2 Out of Scope

| # | Item | Reason |
|---|------|--------|
| 1 | Import massal outlet via file CSV/Excel | Out of scope MVP 1; dapat dijadwalkan ke fase berikutnya |
| 2 | Peta interaktif untuk memilih koordinat GPS | Out of scope MVP 1; koordinat diinput manual |
| 3 | Integrasi data outlet dengan sistem ERP | Integrasi ERP di luar scope MVP 1 |
| 4 | Pengelompokan outlet ke wilayah/territory | Out of scope MVP 1 |

---

## 3. User Stories

### US-01: Membuat Outlet Baru

> **As** Admin
> **I want** menambahkan outlet baru ke dalam sistem beserta koordinat GPS-nya
> **So that** Sales dapat menemukan dan mengunjungi outlet tersebut, dan sistem dapat mengoptimalkan rute kunjungan

**Expected Behavior / Acceptance Criteria:**
- Admin mengisi form dengan: nama outlet, alamat lengkap, latitude, longitude, nama kontak (opsional), dan nomor telepon (opsional)
- Latitude dan longitude adalah field wajib; sistem memvalidasi format angka desimal yang valid (contoh: -6.2088, 106.8456)
- Setelah disimpan, outlet baru muncul di daftar outlet dengan status "Aktif"
- Outlet yang aktif langsung tersedia sebagai tujuan kunjungan di fitur Route Planning untuk Sales
- Jika data wajib tidak lengkap atau format koordinat tidak valid, tampilkan pesan error pada field terkait dan tidak menyimpan data

---

### US-02: Melihat Daftar Outlet

> **As** Admin
> **I want** melihat semua outlet yang terdaftar di sistem beserta status dan data kuncinya
> **So that** saya dapat memantau kelengkapan data dan mengetahui outlet mana yang aktif atau nonaktif

**Expected Behavior / Acceptance Criteria:**
- Halaman menampilkan daftar outlet dalam format tabel dengan kolom: nama outlet, alamat, status (Aktif/Nonaktif), dan indikator GPS (sudah/belum ada koordinat)
- Admin dapat mencari outlet berdasarkan nama atau alamat
- Admin dapat memfilter daftar berdasarkan status (Aktif/Nonaktif)
- Daftar mendukung paginasi jika jumlah outlet melebihi batas tampilan satu halaman

---

### US-03: Mengedit Data Outlet

> **As** Admin
> **I want** mengubah data outlet yang sudah ada, termasuk memperbaiki koordinat GPS
> **So that** data outlet selalu akurat dan kualitas optimasi rute terjaga

**Expected Behavior / Acceptance Criteria:**
- Admin memilih outlet dari daftar dan membuka form edit
- Admin dapat mengubah semua field: nama, alamat, latitude, longitude, kontak, telepon
- Sistem memvalidasi format koordinat GPS saat form disimpan
- Perubahan koordinat GPS berlaku untuk perhitungan rute pada sesi Sales berikutnya
- Setelah disimpan, perubahan langsung tercermin di daftar outlet

---

### US-04: Menonaktifkan / Mengaktifkan Outlet

> **As** Admin
> **I want** menonaktifkan outlet yang sudah tidak relevan secara bisnis atau mengaktifkan kembali outlet yang sebelumnya nonaktif
> **So that** Sales hanya melihat outlet yang aktif sebagai tujuan kunjungan, tanpa perlu menghapus data historis

**Expected Behavior / Acceptance Criteria:**
- Admin dapat menonaktifkan outlet aktif dari halaman daftar atau detail outlet
- Outlet yang dinonaktifkan tidak muncul sebagai pilihan tujuan kunjungan di fitur Route Planning
- Riwayat kunjungan ke outlet yang dinonaktifkan tetap tersimpan dan dapat dilihat di laporan
- Admin dapat mengaktifkan kembali outlet yang nonaktif; outlet tersebut langsung tersedia kembali di Route Planning
- Status outlet (Aktif/Nonaktif) ditampilkan dengan jelas di daftar outlet

---

## 4. Business Rules

| Rule ID | Rule | Description |
|---------|------|-------------|
| BR-01 | Koordinat GPS wajib | Setiap outlet harus memiliki koordinat GPS (latitude & longitude) yang valid untuk mendukung optimasi rute |
| BR-02 | Outlet aktif saja untuk rute | Hanya outlet berstatus Aktif yang muncul sebagai pilihan tujuan kunjungan di Route Planning |
| BR-03 | Data historis terjaga | Menonaktifkan outlet tidak menghapus riwayat kunjungan yang sudah tercatat |
| BR-04 | Nama outlet unik per area | Direkomendasikan nama outlet unik untuk menghindari kebingungan Sales (bukan hard constraint teknis) |

---

## 5. Open Questions

| # | Question | Asked To | Status | Answer |
|---|----------|----------|--------|--------|
| 1 | Apakah diperlukan validasi radius koordinat GPS (misal: harus berada dalam batas geografis area operasional)? | Tech Lead / PM | Pending | - |
| 2 | Apakah Admin perlu bisa melihat outlet di peta pada MVP 1? | PM | Pending | - |
| 3 | Apakah ada data outlet awal yang perlu dimigrasikan dari sistem lama sebelum go-live? | PM / Ops | Pending | - |
| 4 | Berapa estimasi jumlah outlet maksimal (untuk pertimbangan paginasi dan performa)? | PM / Tech Lead | Pending | - |

---

## 6. Definition of Done

- [ ] Testing spec created (QA) — `02-outlet-management---testing.md`
- [ ] Technical spec created (Dev) — `02-outlet-management---technical.md`
- [ ] Design spec created (Designer) — `02-outlet-management---design.md`
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
FILE LOCATION: docs/features/001-admin-panel/02-outlet-management---business.md

RELATED FILES:
- 02-outlet-management---testing.md (QA: Gherkin AC, test cases, validation rules)
- 02-outlet-management---technical.md (Dev: API, data model, implementation)
- 02-outlet-management---design.md (Design: UI/UX specifications)
- 00-admin-panel---overview.md (Epic context)
-->
