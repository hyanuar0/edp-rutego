# BUSINESS SPECIFICATION
## [FEAT-002] Photo Evidence Capture

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-002 |
| Nama Fitur | Photo Evidence Capture |
| Epic | EPIC-003: Visit Verification |
| Prioritas | Must Have |
| Status | Draft |
| Owner | Arina Saffanah Zakiyyah |
| Dibuat | 2026-04-23 |
| Terakhir Diperbarui | 2026-04-23 |
| Taiga Sync | Not synced |

<!-- Taiga Sync: Updated automatically when this spec is pushed to Taiga via /pm:feature. No manual editing needed. -->

---

## 1. Feature Overview

### 1.1 Business Context

**Problem:**
> Check-in berbasis lokasi saja tidak cukup untuk memverifikasi keaslian kunjungan. Field worker dapat memanipulasi lokasi GPS atau check-in tanpa benar-benar berada di lokasi. Diperlukan bukti visual untuk memverifikasi kehadiran fisik di lokasi kunjungan.

**Solution:**
> Sistem capture photo evidence yang mengharuskan field worker mengunggah foto saat check-in, dengan validasi EXIF data, watermark otomatis, dan deteksi kecurangan untuk memastikan keaslian bukti kehadiran.

### 1.2 Target Users

| Peran Pengguna | Primary/Secondary | Kebutuhan |
|-----------|-------------------|-------|
| Field Worker | Primary | Mengunggah foto sebagai bukti kehadiran saat check-in di lokasi kunjungan |
| Supervisor | Secondary | Melihat dan memverifikasi foto evidence yang diunggah oleh tim |
| Manager/Admin | Secondary | Melihat foto evidence untuk keperluan audit dan penyelesaian komplain |

### 1.3 Success Metrics

| Metrik | Current | Target | Pengukuran |
|--------|---------|--------|-------------|
| Photo attachment rate | 0% (tidak ada requirement foto) | 95% check-in memiliki foto evidence | Persentase check-in dengan foto terlampir vs total check-in |
| Valid photo rate | Tidak terukur | 90% foto lulus validasi (EXIF valid, bukan dari galeri) | Persentase foto yang lulus validasi otomatis |
| Flagged photo rate | Tidak terukur | <5% foto ditandai mencurigakan | Persentase foto yang ditandai supervisor sebagai mencurigakan |

---

## 2. Ruang Lingkup

### 2.1 Dalam Ruang Lingkup

| # | Kapabilitas | Deskripsi |
|---|------------|-------------|
| 1 | Upload Foto Saat Check-In | Field worker wajib mengunggah foto saat melakukan check-in di lokasi kunjungan |
| 2 | Validasi EXIF Data | Sistem memvalidasi EXIF data (timestamp, GPS koordinat) pada foto yang diunggah |
| 3 | Watermark Otomatis | Sistem menambahkan watermark otomatis pada foto dengan timestamp dan lokasi |
| 4 | Deteksi Kecurangan | Sistem mendeteksi dan menolak foto yang diambil dari galeri atau tanpa EXIF data |
| 5 | Galeri Foto Worker | Field worker dapat melihat riwayat foto yang pernah diunggah |
| 6 | Review Foto oleh Supervisor | Supervisor dapat melihat dan memverifikasi semua foto evidence dari tim |
| 7 | Kompresi Otomatis | Sistem mengompresi foto secara otomatis untuk hemat storage |

### 2.2 Di Luar Ruang Lingkup

| # | Item | Alasan |
|---|------|--------|
| 1 | Video evidence | Video capture akan dipertimbangkan sebagai enhancement di masa depan |
| 2 | Advanced photo editing | Fitur edit foto (crop, filter) tidak diperlukan untuk use case verifikasi |
| 3 | Photo sharing ke media sosial | Foto evidence hanya untuk keperluan internal verifikasi |

---

## 3. User Stories

<!--
Plain language user stories. PM defines WHAT the user needs.
QA akan memformalkan ini menjadi Gherkin AC di Testing Spec.
Dev akan menurunkan API/data model di Technical Spec.
-->

### US-01: Upload Foto Saat Check-In

> **Sebagai** Field Worker
> **Saya ingin** mengunggah foto saat melakukan check-in di lokasi kunjungan
> **Agar** ada bukti visual kehadiran saya di lokasi tersebut

**Expected Behavior:**
- Setelah berhasil check-in, sistem menampilkan layar upload foto
- Worker harus mengambil foto langsung dari kamera (tidak bisa dari galeri)
- Sistem menampilkan preview foto yang akan diunggah
- Worker dapat mengambil ulang foto jika tidak puas
- Sistem menambahkan watermark otomatis pada foto dengan:
  - Timestamp check-in
  - Nama lokasi kunjungan
  - Koordinat GPS (singkat)
- Sistem mengompresi foto sebelum diunggah
- Sistem menampilkan progress bar selama upload
- Setelah berhasil, worker melihat konfirmasi foto terunggah
- Worker tidak dapat melanjutkan tanpa mengunggah foto

---

### US-02: Validasi EXIF Data

> **Sebagai** Sistem
> **Saya ingin** memvalidasi EXIF data pada foto yang diunggah
> **Agar** hanya foto asli dengan metadata valid yang diterima

**Expected Behavior:**
- Sistem mengecek keberadaan EXIF data pada foto
- Sistem memvalidasi bahwa EXIF data berisi:
  - Timestamp foto (DateTimeOriginal)
  - GPS koordinat (GPSLatitude, GPSLongitude)
- Jika EXIF data tidak lengkap atau tidak valid:
  - Sistem menolak foto dan menampilkan pesan error
  - Sistem memberi tahu worker untuk mengambil foto baru
- Sistem mencatat hasil validasi EXIF (valid/invalid) untuk audit
- Jika timestamp foto berbeda jauh dari waktu check-in (>1 jam), sistem menandai sebagai mencurigakan
- Sistem menyimpan EXIF data asli untuk keperluan audit

---

### US-03: Deteksi Foto dari Galeri

> **Sebagai** Sistem
> **Saya ingin** mendeteksi dan menolak foto yang diambil dari galeri
> **Agara** worker tidak dapat mengunggah foto lama atau bukti palsu

**Expected Behavior:**
- Sistem memaksa penggunaan kamera langsung (buka interface kamera, bukan file picker)
- Sistem mendeteksi jika foto berasal dari galeri (melalui file path, metadata tambahan, atau manipulation indicators)
- Jika terdeteksi dari galeri:
  - Sistem menolak foto
  - Menampilkan pesan: "Harap ambil foto langsung dari kamera. Foto dari galeri tidak diizinkan."
  - Membuka kamera secara otomatis
- Sistem mencatat upaya upload dari galeri untuk keperluan monitoring kecurangan
- Jika worker berulang kali mencoba upload dari galeri (>3 kali), sistem memberi notifikasi ke supervisor

---

### US-04: Watermark Otomatis

> **Sebagai** Sistem
> **Saya ingin** menambahkan watermark otomatis pada foto yang diunggah
> **Agar** foto evidence terlindungi dari manipulasi dan mudah diidentifikasi

**Expected Behavior:**
- Sistem menambahkan watermark secara otomatis setelah foto diambil
- Watermark berisi informasi:
  - Timestamp check-in (format: DD/MM/YYYY HH:MM)
  - Nama lokasi kunjungan
  - Koordinat GPS (format singkat: -6.2088, 106.8456)
  - Nama field worker
- Watermark ditempatkan di pojok kanan bawah dengan background semi-transparan
- Font dan warna watermark dipilih agar mudah dibaca di berbagai background foto
- Watermark ditambahkan setelah kompresi foto untuk menjaga kualitas
- Sistem menyimpan versi asli foto (tanpa watermark) untuk audit internal

---

### US-05: Melihat Galeri Foto Worker

> **Sebagai** Field Worker
> **Saya ingin** melihat riwayat foto yang pernah saya unggah
> **Agar** saya dapat memverifikasi foto-foto saya dan melihat bukti kehadiran saya

**Expected Behavior:**
- Worker membuka halaman "Foto Saya"
- Sistem menampilkan galeri foto dengan thumbnail
- Setiap foto menampilkan informasi:
  - Tanggal check-in
  - Nama lokasi kunjungan
  - Status (valid/invalid/flagged)
- Worker dapat mengklik foto untuk melihat versi full-size
- Worker dapat memfilter berdasarkan rentang tanggal atau lokasi
- Worker dapat mengunduh foto dengan watermark
- Worker hanya dapat melihat foto mereka sendiri, bukan foto worker lain

---

### US-06: Review Foto oleh Supervisor

> **Sebagai** Supervisor
> **Saya ingin** melihat dan memverifikasi semua foto evidence dari tim saya
> **Agar** saya dapat memastikan kepatuhan dan menandai foto mencurigakan

**Expected Behavior:**
- Supervisor membuka dashboard review foto
- Sistem menampilkan galeri foto dari semua worker dalam tim
- Supervisor dapat memfilter berdasarkan:
  - Rentang tanggal
  - Worker tertentu
  - Lokasi kunjungan
  - Status (valid/invalid/flagged)
- Supervisor dapat mengklik foto untuk melihat:
  - Versi full-size dengan watermark
  - EXIF data lengkap
  - Detail kunjungan (timestamp, lokasi, worker)
- Supervisor dapat menandai foto sebagai:
  - ✅ Valid (kunjungan terverifikasi)
  - ⚠️ Mencurigakan (perlu ditinjau lebih lanjut)
  - ❌ Invalid (kecurangan terdeteksi)
- Supervisor dapat memberikan catatan pada foto yang ditandai
- Sistem mengirim notifikasi ke worker jika foto mereka ditandai sebagai mencurigakan atau invalid

---

### US-07: Kompresi Otomatis

> **Sebagai** Sistem
> **Saya ingin** mengompresi foto secara otomatis sebelum diunggah
> **Agar** penggunaan storage dan bandwidth efisien

**Expected Behavior:**
- Sistem menerima foto dari kamera (biasanya 2-10 MB)
- Sistem mengompresi foto dengan kualitas yang masih baik untuk verifikasi
- Target ukuran setelah kompresi: maksimal 500 KB per foto
- Kompresi dilakukan client-side sebelum upload untuk menghemat bandwidth
- Sistem menjaga aspect ratio dan orientasi foto
- Sistem menyimpan foto asli (sebelum watermark) dalam kualitas lebih tinggi untuk audit (opsional, berdasarkan kebijakan storage)
- Sistem menampilkan ukuran foto sebelum dan sesudah kompresi kepada user
- Sistem memberikan opsi untuk mengunduh versi kompresi (untuk sharing) atau asli (untuk audit)

---

## 4. Business Rules

<!--
Policy-level rules. "Apa yang bisnis butuhkan."
Validasi level field milik Testing Spec atau Technical Spec.
-->

| Rule ID | Rule | Deskripsi |
|---------|------|-------------|
| BR-01 | Wajib Upload Foto | Field worker wajib mengunggah foto saat check-in. Tidak dapat melanjutkan tanpa foto. |
| BR-02 | Kamera Langsung | Foto harus diambil langsung dari kamera device. Tidak boleh dari galeri atau file lain. |
| BR-03 | Wajib EXIF Data | Foto harus memiliki EXIF data lengkap (timestamp dan GPS koordinat). Foto tanpa EXIF akan ditolak. |
| BR-04 | Batas Ukuran Foto | Ukuran foto maksimal sebelum kompresi adalah 10 MB. Foto lebih besar akan ditolak. |
| BR-05 | Watermark Otomatis | Semua foto yang diunggah wajib memiliki watermark dengan timestamp dan lokasi. |
| BR-06 | Kompresi Otomatis | Sistem mengompresi foto otomatis hingga maksimal 500 KB sebelum diunggah. |
| BR-07 | Validasi Timestamp | Timestamp foto dalam EXIF tidak boleh berbeda lebih dari 1 jam dari waktu check-in. |
| BR-08 | Deteksi Kecurangan | Jika worker 3 kali mencoba upload dari galeri, supervisor diberi notifikasi. |
| BR-09 | Batas Jumlah Foto | Maksimal 3 foto per check-in. Worker dapat mengganti foto sebelum mengirim final. |
| BR-10 | Retensi Foto | Foto evidence disimpan minimal 6 bulan untuk keperluan audit dan komplain. |

---

## 5. Open Questions

| # | Pertanyaan | Ditanya Kepada | Status | Jawaban |
|---|----------|----------|--------|--------|
| 1 | Berapa ukuran maksimal foto sebelum kompresi? (default: 10 MB) | Business Owner | Pending | - |
| 2 | Berapa target ukuran setelah kompresi? (default: 500 KB) | Technical Team | Pending | - |
| 3 | Berapa lama retensi foto evidence? (default: 6 bulan) | Business Owner | Pending | - |
| 4 | Apakah perlu menyimpan foto asli (tanpa watermark) untuk audit? | Business Owner | Pending | - |
| 5 | Apakah ada opsi untuk mengizinkan upload dari galeri dengan alasan tertentu? (misal: kamera rusak) | Business Owner | Pending | - |

---

## 6. Definition of Done

- [ ] Testing spec created (QA)
- [ ] Technical spec created (Dev)
- [ ] Design spec created (Designer)
- [ ] All specs reviewed and approved
- [ ] Implemented and deployed to staging
- [ ] QA sign-off
- [ ] Design QA passed

---

## 7. Approval

| Role | Nama | Tanggal | Status |
|------|------|------|--------|
| PM | | | Pending |
| QA Lead | | | Pending |
| Tech Lead | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-visit-verification/02-photo-evidence---business.md

RELATED FILES:
- 02-photo-evidence---testing.md (QA: AC, test cases, validation)
- 02-photo-evidence---technical.md (Dev: API, data model, implementation)
- 02-photo-evidence---design.md (Design: UI/UX specifications)
- 00-visit-verification---overview.md (Epic context)
-->
