# BUSINESS SPECIFICATION
## [FEAT-003] Schedule & Route Validation

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-003 |
| Nama Fitur | Schedule & Route Validation |
| Epic | EPIC-003: Visit Verification |
| Prioritas | Should Have |
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
> Saat ini field worker dapat melakukan kunjungan dalam urutan apa pun dan pada waktu apa pun tanpa validasi terhadap jadwal dan rute yang ditugaskan. Hal ini menyebabkan inefisiensi operasional, penggunaan waktu dan bahan bakar yang tidak optimal, serta kesulitan dalam memantau kepatuhan terhadap rencana kerja yang telah disepakati.

**Solution:**
> Sistem validasi jadwal dan rute yang memastikan field worker mengikuti jadwal dan urutan kunjungan yang optimal, dengan kemampuan pembuatan rute otomatis berdasarkan koordinat GPS, validasi kesesuaian jadwal, dan penandaan otomatis ketika terjadi penyimpangan.

### 1.2 Target Users

| Peran Pengguna | Primary/Secondary | Kebutuhan |
|-----------|-------------------|-------|
| Field Worker | Primary | Melihat jadwal dan rute harian, mengetahui kunjungan berikutnya, mengikuti urutan yang optimal |
| Scheduler | Primary | Membuat jadwal dan rute optimal untuk tim lapangan berdasarkan lokasi geografis |
| Supervisor | Secondary | Memantau kepatuhan tim terhadap jadwal dan rute yang ditugaskan, menerima notifikasi penyimpangan |

### 1.3 Success Metrics

| Metrik | Current | Target | Pengukuran |
|--------|---------|--------|-------------|
| Schedule adherence rate | Tidak terukur | 90% kunjungan sesuai jadwal (hari/waktu) | Persentase kunjungan yang dilakukan sesuai jadwal yang ditentukan |
| Route compliance rate | Tidak terukur | 85% mengikuti urutan rute | Persentase kunjungan yang mengikuti urutan rute yang optimal |
| Route efficiency improvement | Tidak terukur | 20% penurunan jarak/waktu | Persentase penurunan jarak dan waktu perjalanan dengan rute optimal vs aktual |

---

## 2. Ruang Lingkup

### 2.1 Dalam Ruang Lingkup

| # | Kapabilitas | Deskripsi |
|---|------------|-------------|
| 1 | Optimasi Rute Otomatis | Sistem membuat rute optimal secara otomatis berdasarkan algoritma (TSP, nearest neighbor) menggunakan koordinat GPS lokasi kunjungan |
| 2 | Validasi Jadwal | Sistem memvalidasi bahwa kunjungan dilakukan pada hari dan waktu yang sesuai dengan jadwal yang ditentukan |
| 3 | Validasi Urutan Rute | Sistem memvalidasi bahwa worker mengikuti urutan kunjungan sesuai rute yang ditugaskan |
| 4 | Notifikasi Penyimpangan | Sistem memberikan notifikasi ke supervisor jika worker melakukan penyimpangan dari jadwal atau rute |

### 2.2 Di Luar Ruang Lingkup

| # | Item | Alasan |
|---|------|--------|
| - | Tidak ada | Semua kapabilitas terkait validasi jadwal dan rute termasuk dalam ruang lingkup fitur ini |

---

## 3. User Stories

<!--
Plain language user stories. PM defines WHAT the user needs.
QA akan memformalkan ini menjadi Gherkin AC di Testing Spec.
Dev akan menurunkan API/data model di Technical Spec.
-->

### US-01: Membuat Rute Optimal

> **Sebagai** Scheduler
> **Saya ingin** membuat rute optimal secara otomatis untuk field worker
> **Agar** worker dapat mengunjungi lokasi dengan urutan yang paling efisien

**Expected Behavior:**
- Scheduler memilih worker dan tanggal untuk pembuatan rute
- Scheduler memilih lokasi kunjungan yang akan ditugaskan
- Sistem mengambil koordinat GPS dari setiap lokasi
- Sistem menerapkan algoritma optimasi (misal: Traveling Salesman Problem, Nearest Neighbor)
- Sistem menghasilkan urutan kunjungan yang optimal berdasarkan:
  - Jarak terpendek total
  - Waktu perjalanan minimum
  - Memperhitungkan kondisi lalu lintas (jika data tersedia)
- Sistem menampilkan rute di peta dengan urutan yang jelas
- Sistem menampilkan estimasi:
  - Total jarak perjalanan
  - Total waktu perjalanan
  - Waktu tempuh antar lokasi
- Scheduler dapat menyesuaikan urutan secara manual jika diperlukan
- Scheduler dapat menyimpan dan menugaskan rute ke worker

---

### US-02: Melihat Jadwal & Rute Harian

> **Sebagai** Field Worker
> **Saya ingin** melihat jadwal dan rute kunjungan untuk hari ini
> **Agara** saya tahu lokasi mana yang harus dikunjungi terlebih dahulu dan bagaimana urutannya

**Expected Behavior:**
- Worker membuka aplikasi dan melihat dashboard jadwal
- Sistem menampilkan rute harian dengan:
  - Daftar lokasi yang akan dikunjungi
  - Urutan kunjungan (1, 2, 3, ...)
  - Waktu yang dijadwalkan untuk setiap kunjungan
  - Estimasi waktu perjalanan antar lokasi
  - Total jarak dan waktu untuk hari tersebut
- Sistem menampilkan peta dengan rute visual:
  - Garis rute yang menghubungkan lokasi
  - Marker untuk setiap lokasi dengan nomor urutan
  - Warna berbeda untuk lokasi yang sudah dikunjungi vs yang belum
- Worker dapat mengklik setiap lokasi untuk melihat detail:
  - Nama lokasi
  - Alamat lengkap
  - Waktu jadwal
  - Estimasi kedatangan
  - Navigasi ke lokasi (integrasi dengan Google Maps/Maps)
- Sistem menampilkan lokasi berikutnya yang harus dikunjungi
- Sistem memberikan notifikasi jika worker terlambat dari jadwal

---

### US-03: Penandaan Otomatis Jadwal Mismatch

> **Sebagai** Sistem
> **Saya ingin** menandai secara otomatis kunjungan yang dilakukan di luar jadwal
> **Agara** supervisor dapat mengetahui penyimpangan dan mengambil tindakan jika perlu

**Expected Behavior:**
- Sistem memantau setiap check-in yang dilakukan worker
- Sistem memvalidasi check-in terhadap jadwal yang ditugaskan:
  - Hari yang sesuai
  - Waktu dalam rentang yang diizinkan (misal: ±30 menit dari jadwal)
  - Lokasi yang sesuai dengan jadwal hari tersebut
- Jika check-in dilakukan pada hari yang salah:
  - Sistem menandai kunjungan sebagai "Wrong Day"
  - Sistem mencatat: "Kunjungan dijadwalkan untuk hari [X], tetapi dilakukan pada hari [Y]"
- Jika check-in dilakukan di luar rentang waktu:
  - Sistem menandai kunjungan sebagai "Wrong Time"
  - Sistem mencatat: "Dijadwalkan: [HH:MM], Dilakukan: [HH:MM], Selisih: [X] menit"
- Jika check-in dilakukan di lokasi yang tidak ada dalam jadwal hari tersebut:
  - Sistem menandai kunjungan sebagai "Unscheduled Visit"
  - Sistem mencatat: "Lokasi tidak ada dalam jadwal untuk tanggal ini"
- Supervisor dapat melihat semua kunjungan yang ditandai
- Supervisor dapat memfilter berdasarkan jenis mismatch
- Supervisor dapat mengubah status jika ada penjelasan valid (force majeure)

---

### US-04: Validasi Urutan Rute

> **Sebagai** Sistem
> **Saya ingin** memvalidasi bahwa worker mengikuti urutan rute yang ditugaskan
> **Agara** efisiensi operasional terjaga dan supervisor dapat memantau kepatuhan

**Expected Behavior:**
- Sistem mencatat urutan kunjungan aktual berdasarkan timestamp check-in
- Sistem membandingkan urutan aktual dengan urutan rute yang ditugaskan
- Jika worker mengunjungi lokasi dalam urutan yang berbeda:
  - Sistem menandai sebagai "Route Deviation"
  - Sistem mencatat:
    - Urutan yang ditugaskan: 1 → 2 → 3 → 4
    - Urutan aktual: 1 → 3 → 2 → 4
    - Lokasi yang dilompati: Lokasi #2
    - Lokasi yang dikunjungi di luar urutan: Lokasi #3 dikunjungi sebelum #2
- Sistem menghitung dampak penyimpangan:
  - Tambahan jarak yang ditempuh (dalam km)
  - Tambahan waktu perjalanan (dalam menit)
  - Persentase penurunan efisiensi
- Jika penyimpangan melebihi batas toleransi (misal: >5 km atau >15 menit):
  - Sistem memberikan notifikasi ke supervisor
  - Notifikasi berisi detail penyimpangan dan dampaknya
- Worker dapat melihat urutan yang telah mereka selesaikan dan urutan berikutnya
- Sistem menampilkan progress bar rute (misal: "3 dari 5 lokasi selesai")

---

### US-05: Notifikasi Penyimpangan ke Supervisor

> **Sebagai** Supervisor
> **Saya ingin** menerima notifikasi jika worker melakukan penyimpangan dari jadwal atau rute
> **Agara** saya dapat segera menindaklanjuti dan memberikan bimbingan jika diperlukan

**Expected Behavior:**
- Sistem memantau penyimpangan jadwal dan rute secara real-time
- Jika terjadi penyimpangan jadwal:
  - Supervisor menerima notifikasi dengan detail:
    - Nama worker
    - Jenis penyimpangan (Wrong Day/Time/Location)
    - Jadwal yang seharusnya
    - Kunjungan yang dilakukan
    - Timestamp penyimpangan
- Jika terjadi penyimpangan rute:
  - Supervisor menerima notifikasi dengan detail:
    - Nama worker
    - Jenis penyimpangan (Route Deviation)
    - Urutan yang ditugaskan vs aktual
    - Tambahan jarak/waktu
    - Lokasi yang terdampak
- Notifikasi dikirim melalui:
  - In-app notification
  - Email (opsional, berdasarkan preferensi)
  - Dashboard dengan daftar penyimpangan
- Supervisor dapat mengklik notifikasi untuk melihat detail lengkap
- Supervisor dapat memberikan catatan atau tindak lanjut pada setiap penyimpangan
- Supervisor dapat menandai penyimpangan sebagai:
  - ✅ Tolerable (masih dalam batas wajar)
  - ⚠️ Needs Attention (perlu pembahasan dengan worker)
  - ❌ Unacceptable (perlu tindakan disipliner)

---

## 4. Business Rules

<!--
Policy-level rules. "Apa yang bisnis butuhkan."
Validasi level field milik Testing Spec atau Technical Spec.
-->

| Rule ID | Rule | Deskripsi |
|---------|------|-------------|
| BR-01 | Kewajiban Mengikuti Urutan | Field worker wajib mengikuti urutan rute yang ditugaskan kecuali ada izin explicit dari supervisor |
| BR-02 | Penandaan Otomatis Mismatch | Kunjungan yang dilakukan di luar jadwal (hari/waktu salah) otomatis ditandai sebagai mismatch |
| BR-03 | Toleransi Waktu | Check-in dalam ±30 menit dari jadwal dianggap sesuai jadwal |
| BR-04 | Perubahan Jadwal Diperbolehkan | Scheduler dapat mengubah jadwal dan rute jika ada perubahan mendadak atau kondisi darurat |
| BR-05 | Batas Toleransi Penyimpangan Rute | Penyimpangan rute ≤5 km atau ≤15 menit tidak diberi notifikasi ke supervisor |
| BR-06 | Kunjungan Tambahan | Kunjungan di luar jadwal (unscheduled) ditandai tetapi tidak secara otomatis ditolak |
| BR-07 | Validasi Real-Time | Validasi jadwal dan rute dilakukan secara real-time saat check-in |
| BR-08 | Riwayat Penyimpangan | Semua penyimpangan dicatat dalam riwayat untuk keperluan evaluasi kinerja |

---

## 5. Open Questions

| # | Pertanyaan | Ditanya Kepada | Status | Jawaban |
|---|----------|----------|--------|--------|
| 1 | Berapa toleransi waktu untuk check-in sesuai jadwal? (default: ±30 menit) | Business Owner | Pending | - |
| 2 | Berapa batas toleransi penyimpangan rute sebelum notifikasi? (default: 5 km atau 15 menit) | Business Owner | Pending | - |
| 3 | Algoritma optimasi apa yang akan digunakan? (TSP, Nearest Neighbor, atau lainnya) | Technical Team | Pending | - |
| 4 | Apakah worker dapat meminta perubahan rute saat sedang bekerja? | Business Owner | Pending | - |
| 5 | Apakah ada penalti untuk pelanggaran berulang terhadap jadwal/rute? | Business Owner | Pending | - |

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
FILE LOCATION: docs/features/001-visit-verification/03-schedule-validation---business.md

RELATED FILES:
- 03-schedule-validation---testing.md (QA: AC, test cases, validation)
- 03-schedule-validation---technical.md (Dev: API, data model, implementation)
- 03-schedule-validation---design.md (Design: UI/UX specifications)
- 00-visit-verification---overview.md (Epic context)
-->
