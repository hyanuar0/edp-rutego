# BUSINESS SPECIFICATION
## [FEAT-001] Location Check-In/Out System

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-001 |
| Nama Fitur | Location Check-In/Out System |
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
> Saat ini tidak ada mekanisme validasi untuk memastikan field worker benar-benar berada di lokasi yang ditentukan saat melakukan kunjungan. Hal ini menyebabkan potensi kecurangan check-in, ketidakakuratan data kehadiran, dan kesulitan dalam memverifikasi kepatuhan terhadap jadwal kunjungan.

**Solution:**
> Sistem check-in/check-out berbasis GPS dengan validasi geofencing untuk memverifikasi keberadaan field worker di lokasi kunjungan yang ditentukan, mencatat timestamp dan koordinat lokasi secara otomatis.

### 1.2 Target Users

| Peran Pengguna | Primary/Secondary | Kebutuhan |
|-----------|-------------------|-------|
| Field Worker | Primary | Melakukan check-in saat tiba dan check-out saat selesai di lokasi kunjungan |
| Supervisor | Secondary | Memantau status kehadiran dan lokasi tim lapangan secara real-time |
| Manager/Admin | Secondary | Melihat laporan kehadiran untuk analisis dan keperluan komplain |

### 1.3 Success Metrics

| Metrik | Current | Target | Pengukuran |
|--------|---------|--------|-------------|
| Akurasi lokasi check-in | 0% (tidak ada validasi) | 95% check-in valid dalam radius geofence | Persentase check-in yang valid (dalam lokasi yang benar) vs total check-in |
| Completion rate | Tidak terukur | 90% kunjungan memiliki check-in dan check-out lengkap | Persentase kunjungan dengan check-in dan check-out lengkap |
| Deteksi kecurangan | Tidak terdeteksi | Deteksi 100% check-in di luar lokasi | Jumlah check-in di luar geofence yang berhasil ditandai |

---

## 2. Ruang Lingkup

### 2.1 Dalam Ruang Lingkup

| # | Kapabilitas | Deskripsi |
|---|------------|-------------|
| 1 | Check-in/Check-out Kunjungan | Field worker melakukan check-in saat tiba dan check-out saat selesai di lokasi kunjungan |
| 2 | Validasi Geofence | Sistem memvalidasi bahwa worker berada dalam radius geofence yang ditentukan saat check-in |
| 3 | Real-time Tracking | Supervisor dapat melihat lokasi worker yang sedang check-in secara real-time |
| 4 | Riwayat Check-In/Out | Pengguna dapat melihat riwayat check-in/check-out mereka dengan timestamp dan lokasi |
| 5 | Notifikasi Lokasi Invalid | Sistem memberi notifikasi jika worker mencoba check-in di luar lokasi yang ditentukan |

### 2.2 Di Luar Ruang Lingkup

| # | Item | Alasan |
|---|------|--------|
| 1 | Schedule & Route Validation | Validasi kesesuaian jadwal dan rute akan ditangani dalam fitur FEAT-003 |
| 2 | Photo Validation | Validasi berbasis foto akan ditangani dalam fitur FEAT-002 Photo Evidence Capture |

---

## 3. User Stories

<!--
Plain language user stories. PM defines WHAT the user needs.
QA akan memformalkan ini menjadi Gherkin AC di Testing Spec.
Dev akan menurunkan API/data model di Technical Spec.
-->

### US-01: Check-In di Lokasi Kunjungan

> **Sebagai** Field Worker
> **Saya ingin** melakukan check-in saat tiba di lokasi kunjungan
> **Agar** kehadiran saya tercatat dan terverifikasi bahwa saya berada di lokasi yang benar

**Expected Behavior:**
- Field worker membuka aplikasi dan memilih kunjungan yang akan dilakukan
- Sistem memeriksa apakah GPS aktif dan izin lokasi diberikan
- Jika GPS non-aktif, sistem menolak check-in dan meminta user mengaktifkan GPS
- Sistem mendapatkan lokasi saat ini dan memvalidasi terhadap geofence lokasi kunjungan
- Jika worker berada dalam radius geofence, check-in berhasil dan sistem mencatat timestamp serta koordinat GPS
- Jika worker berada di luar radius geofence, sistem menolak check-in dan memberikan notifikasi lokasi invalid
- Sistem menampilkan konfirmasi check-in berhasil dengan timestamp dan lokasi tercatat

---

### US-02: Check-Out Selesai Kunjungan

> **Sebagai** Field Worker
> **Saya ingin** melakukan check-out saat menyelesaikan kunjungan
> **Agara** durasi kunjungan tercatat dan saya dapat melanjutkan ke kunjungan berikutnya

**Expected Behavior:**
- Field worker membuka aplikasi dan memilih kunjungan yang sedang berlangsung
- Sistem mendapatkan lokasi saat ini dan mencatatnya sebagai lokasi check-out
- Sistem mencatat timestamp check-out
- Sistem menghitung durasi kunjungan (check-out timestamp minus check-in timestamp)
- Worker tidak dapat melakukan check-in baru untuk kunjungan lain tanpa check-out dari kunjungan saat ini
- Sistem menampilkan konfirmasi check-out berhasil dengan durasi kunjungan

---

### US-03: Monitoring Kehadiran Real-Time

> **Sebagai** Supervisor
> **Saya ingin** melihat lokasi worker yang sedang check-in secara real-time
> **Agar** saya dapat memantau kehadiran tim dan merespons jika ada masalah

**Expected Behavior:**
- Supervisor membuka dashboard monitoring kehadiran
- Sistem menampilkan daftar worker yang sedang check-in dengan lokasi mereka
- Sistem memperbarui lokasi secara real-time (sesuai interval yang ditentukan)
- Supervisor dapat melihat detail kunjungan (lokasi tujuan, waktu check-in, durasi)
- Sistem menandai worker yang check-in di luar geofence dengan indikator visual
- Supervisor dapat memfilter berdasarkan status, wilayah, atau tim

---

### US-04: Melihat Riwayat Check-In/Out

> **Sebagai** Field Worker / Supervisor / Manager
> **Saya ingin** melihat riwayat check-in/check-out dengan timestamp dan lokasi
> **Agar** saya dapat memverifikasi kehadiran dan menindaklanjuti jika ada perbedaan

**Expected Behavior:**
- Pengguna membuka halaman riwayat kehadiran
- Sistem menampilkan daftar kunjungan dengan timestamp check-in, check-out, dan lokasi
- Pengguna dapat memfilter berdasarkan rentang tanggal, status, atau lokasi
- Pengguna dapat mengklik setiap kunjungan untuk melihat detail lengkap (koordinat GPS, peta, durasi)
- Field worker hanya dapat melihat riwayat mereka sendiri
- Supervisor dan Manager dapat melihat riwayat tim mereka

---

### US-05: Notifikasi Lokasi Invalid

> **Sebagai** Field Worker
> **Saya ingin** menerima notifikasi jika check-in saya ditolak karena lokasi invalid
> **Agar** saya memahami masalah dan dapat mengambil tindakan yang tepat

**Expected Behavior:**
- Worker mencoba check-in di luar radius geofence
- Sistem menolak check-in dan menampilkan notifikasi error
- Notifikasi berisi pesan: "Lokasi check-in Anda berada di luar area yang ditentukan. Jarak Anda: X meter dari lokasi kunjungan."
- Sistem menampilkan peta dengan lokasi saat ini dan lokasi yang ditentukan
- Worker diberikan opsi untuk:
  - Mencoba kembali setelah pindah ke lokasi yang benar
  - Melaporkan masalah lokasi jika geofence salah
- Notifikasi juga dikirim ke supervisor jika worker mencoba check-in di luar lokasi lebih dari 3 kali

---

## 4. Business Rules

<!--
Policy-level rules. "Apa yang bisnis butuhkan."
Validasi level field milik Testing Spec atau Technical Spec.
-->

| Rule ID | Rule | Deskripsi |
|---------|------|-------------|
| BR-01 | Wajib GPS Aktif | Field worker wajib mengaktifkan GPS dan memberikan izin lokasi sebelum dapat melakukan check-in |
| BR-02 | Validasi Geofence | Check-in hanya diizinkan jika worker berada dalam radius geofence yang ditentukan (default: 100 meter) |
| BR-03 | Kewajiban Check-Out | Field worker wajib melakukan check-out sebelum dapat melakukan check-in untuk kunjungan berikutnya |
| BR-04 | Pencataan Lokasi & Timestamp | Semua check-in dan check-out wajib mencatat timestamp dan koordinat GPS |
| BR-05 | Batas Percobaan | Jika worker mencoba check-in di luar geofence lebih dari 3 kali, sistem memberi notifikasi ke supervisor |
| BR-06 | Durasi Minimum Kunjungan | Kunjungan dengan durasi kurang dari 5 menit ditandai sebagai kunjungan mencurigakan |
| BR-07 | Deteksi Kunjungan Terbuka | Sistem menandai kunjungan yang belum check-out setelah 8 jam sebagai kunjungan terbuka dan memberi notifikasi ke worker dan supervisor |

---

## 5. Open Questions

| # | Pertanyaan | Ditanya Kepada | Status | Jawaban |
|---|----------|----------|--------|--------|
| 1 | Berapa radius geofence yang akan digunakan? (default 100m) | Business Owner | Pending | - |
| 2 | Apakah ada pengecualian untuk check-in di luar geofence (misal: force majeure)? | Business Owner | Pending | - |
| 3 | Berapa interval update untuk real-time tracking? | Technical Team | Pending | - |
| 4 | Apakah worker dapat mengedit check-in/check-out setelah fakta? | Business Owner | Pending | - |

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
FILE LOCATION: docs/features/001-visit-verification/01-location-checkin---business.md

RELATED FILES:
- 01-location-checkin---testing.md (QA: AC, test cases, validation)
- 01-location-checkin---technical.md (Dev: API, data model, implementation)
- 01-location-checkin---design.md (Design: UI/UX specifications)
- 00-visit-verification---overview.md (Epic context)
-->
