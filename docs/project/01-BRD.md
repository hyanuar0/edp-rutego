# BUSINESS REQUIREMENTS DOCUMENT
## RuteGo – Sales Route Planning & Execution System

---

| Atribut | Nilai |
|---------|-------|
| Document ID | BRD-RUTEGO-001 |
| Versi | 1.2 |
| Status | Draft |
| Author | Product Manager |
| Dibuat | 2026-04-23 |
| Terakhir Diperbarui | 2026-04-23 |

---

## Changelog

| Versi | Tanggal | Author | Perubahan |
|-------|---------|--------|-----------|
| 1.0 | 2026-04-23 | Product Manager | Versi awal |
| 1.1 | 2026-04-23 | Product Manager | Koreksi scope: aplikasi difokuskan ke Web App (API + Web) untuk MVP 1; mobile app menjadi out of scope; tambah Admin Panel sebagai epic; hapus fitur navigasi turn-by-turn |
| 1.2 | 2026-04-23 | Product Manager | Pemisahan Web App menjadi dua aplikasi: Admin App (untuk peran Admin) dan User App (untuk peran Manager dan Sales) |

---

## 1. Executive Summary

### 1.1 Latar Belakang

Distributor FMCG (Fast-Moving Consumer Goods) saat ini menghadapi berbagai kendala operasional pada aktivitas tim sales di lapangan. Rute kunjungan yang tidak teroptimasi menyebabkan pemborosan waktu dan biaya perjalanan, serta membatasi jumlah outlet yang dapat dikunjungi setiap harinya. Selain itu, proses pelaporan kunjungan yang belum tervalidasi secara sistem membuka peluang terjadinya laporan fiktif, sehingga mengurangi akurasi data dan menyulitkan manajemen dalam pengambilan keputusan.

Di sisi lain, proses pencatatan order yang masih dilakukan secara manual meningkatkan risiko kesalahan input dan kehilangan data, yang pada akhirnya berdampak langsung pada potensi kehilangan revenue. Ketiga masalah ini secara kolektif menurunkan produktivitas tim sales dan menghambat pertumbuhan bisnis.

Proyek RuteGo hadir untuk menjawab tantangan tersebut melalui digitalisasi menyeluruh pada proses perencanaan rute, verifikasi aktivitas kunjungan, dan pencatatan order sales, dimulai dengan pengembangan berbasis Web App agar dapat segera dioperasikan oleh seluruh peran pengguna.

### 1.2 Solusi yang Diusulkan

RuteGo adalah sistem digital yang terdiri dari RESTful API dan dua aplikasi web terpisah: **Admin App** untuk pengelolaan data master sistem, dan **User App** untuk aktivitas operasional harian Sales dan pengawasan Manager. Sistem ini mengintegrasikan teknologi GPS berbasis browser untuk optimasi rute kunjungan dan verifikasi aktivitas sales, serta digitalisasi proses order. Pemisahan aplikasi ini memungkinkan pengelolaan akses dan antarmuka yang lebih sesuai dengan kebutuhan masing-masing peran pengguna.

### 1.3 Aplikasi yang Dibangun

| Komponen | Deskripsi | Pengguna |
|----------|-----------|---------|
| **API** | RESTful API sebagai backend utama sistem | Semua client (Admin App, User App, integrasi pihak ketiga) |
| **Admin App** | Web App untuk pengelolaan data master dan konfigurasi sistem | Admin |
| **User App** | Web App responsif untuk aktivitas operasional lapangan dan monitoring | Manager, Head of Sales, Sales |

### 1.4 Manfaat Utama

| # | Manfaat | Deskripsi |
|---|---------|-----------|
| 1 | Efisiensi Rute | Rute kunjungan yang optimal mengurangi waktu perjalanan dan biaya operasional |
| 2 | Integritas Data | Verifikasi GPS, foto, dan timestamp mencegah laporan kunjungan fiktif |
| 3 | Akurasi Order | Digitalisasi proses order menghilangkan risiko kesalahan pencatatan manual |
| 4 | Visibilitas Manajemen | Dashboard real-time memungkinkan monitoring dan pengambilan keputusan berbasis data |
| 5 | Kontrol Data Master | Admin dapat mengelola user, outlet, dan produk dari satu panel terpusat |

---

## 2. Tujuan Bisnis (Business Objectives)

### 2.1 Tujuan Bisnis (SMART)

| ID | Tujuan | Target | Pengukuran |
|----|--------|--------|------------|
| BO-01 | Mengimplementasikan sistem digital (API + Web App) untuk rute, laporan, dan order sales | Go-live MVP 1 dalam 15 minggu sejak kickoff | Tanggal deployment ke production |
| BO-02 | Mengurangi waktu perjalanan sales | ≥20% pengurangan rata-rata waktu perjalanan per hari | Perbandingan rata-rata durasi perjalanan sebelum dan sesudah implementasi |
| BO-03 | Mengurangi laporan kunjungan fiktif | ≥80% pengurangan laporan fiktif | Persentase kunjungan terverifikasi (GPS + foto + timestamp) vs total laporan |
| BO-04 | Mengurangi kesalahan input order | ≥50% pengurangan error order | Perbandingan jumlah order bermasalah sebelum dan sesudah implementasi |

### 2.2 Kriteria Keberhasilan (Success Criteria)

Proyek dinyatakan berhasil apabila:
1. Sistem RuteGo (API + Web App) berhasil go-live sesuai jadwal 15 minggu
2. Admin dapat mengelola data master (user, outlet, produk) melalui Admin Panel
3. Rata-rata waktu perjalanan sales berkurang minimal 20% dalam 30 hari setelah go-live
4. Minimal 80% kunjungan sales tercatat dengan bukti verifikasi valid (GPS + foto + timestamp)
5. Jumlah kesalahan input order berkurang minimal 50% dalam 30 hari setelah go-live
6. Tingkat adopsi Web App oleh Sales Representative mencapai minimal 80% dalam 2 minggu pertama

---

## 3. Stakeholder

| Stakeholder | Peran | Kepentingan | Kontak |
|-------------|-------|-------------|--------|
| TBD | Executive Sponsor | Tinggi | TBD |
| TBD | Department Sponsor | Tinggi | TBD |
| Product Manager | Penanggung jawab produk & koordinasi tim | Tinggi | — |
| Head of Sales | Business owner; penerima manfaat utama dashboard monitoring | Tinggi | — |
| Sales Manager | Pengguna dashboard Web App; pengawas aktivitas tim sales | Tinggi | — |
| Admin | Pengelola data master sistem (user, outlet, produk) | Tinggi | — |
| Sales Representative | Pengguna utama Web App untuk rute, kunjungan, dan order | Tinggi | — |
| Tech Lead | Penanggung jawab arsitektur dan pengambilan keputusan teknis | Sedang | — |
| UI/UX Designer | Perancang pengalaman pengguna Web App | Sedang | — |
| Backend Engineer | Pengembang API dan logika bisnis server-side | Sedang | — |
| Frontend Engineer | Pengembang antarmuka Web App | Sedang | — |
| QA Engineer | Pengujian kualitas sistem sebelum release | Sedang | — |

---

## 4. Kondisi Saat Ini vs Kondisi yang Diharapkan

### 4.1 Kondisi Saat Ini (As-Is)

**Permasalahan Utama:**

| # | Masalah | Dampak Bisnis | Frekuensi |
|---|---------|---------------|-----------|
| 1 | Rute kunjungan ditentukan secara manual atau berdasarkan kebiasaan, bukan optimasi | Waktu perjalanan tidak efisien, jumlah outlet yang dikunjungi tidak maksimal | Harian |
| 2 | Laporan kunjungan tidak diverifikasi secara sistem (tidak ada GPS/foto/timestamp) | Laporan berpotensi fiktif; manajemen tidak dapat memvalidasi aktivitas lapangan | Harian |
| 3 | Proses pencatatan order dilakukan secara manual (kertas atau spreadsheet) | Rawan kesalahan input, kehilangan data, dan keterlambatan pemrosesan order | Harian |
| 4 | Tidak ada dashboard terpusat untuk monitoring aktivitas sales | Manajemen kesulitan mendapatkan visibilitas real-time terhadap performa tim | Harian |
| 5 | Data master (outlet, produk) dikelola secara manual dan tersebar | Inkonsistensi data; Sales tidak memiliki referensi outlet dan produk yang akurat | Harian |

### 4.2 Kondisi yang Diharapkan (To-Be)

**Peningkatan yang Diharapkan:**

| # | Peningkatan | Manfaat yang Diharapkan |
|---|-------------|------------------------|
| 1 | Sistem rekomendasi rute otomatis berbasis Google Maps, diakses melalui Web App | Pengurangan waktu perjalanan ≥20%; Sales dapat merencanakan hari kerja lebih efisien |
| 2 | Verifikasi kunjungan via GPS (Geolocation API), foto kamera browser, dan timestamp | Eliminasi laporan fiktif; data kunjungan akurat dan dapat diaudit |
| 3 | Sistem order digital terintegrasi dalam Web App | Eliminasi kesalahan input manual; order langsung tercatat di sistem |
| 4 | Dashboard monitoring real-time untuk Manager dan Head of Sales | Visibilitas penuh terhadap aktivitas lapangan; pengambilan keputusan berbasis data |
| 5 | Admin Panel untuk pengelolaan data master terpusat | Data user, outlet, dan produk selalu akurat dan konsisten |

---

## 5. Daftar Epic & Fitur (MVP 1)

<!--
CATATAN: User Stories dan Acceptance Criteria terperinci terdapat di dokumen SPEC.
Bagian ini hanya memuat daftar high-level untuk MVP 1.
-->

### 5.1 Daftar Epic

| Epic ID | Nama Epic | Deskripsi | Prioritas |
|---------|-----------|-----------|-----------|
| EPIC-001 | Admin Panel | Pengelolaan data master sistem: user, outlet, dan katalog produk | Must Have |
| EPIC-002 | Route Planning | Perencanaan dan optimasi rute kunjungan sales harian berbasis Google Maps | Must Have |
| EPIC-003 | Visit Verification | Verifikasi aktivitas kunjungan sales via GPS, foto kamera, dan timestamp otomatis | Must Have |
| EPIC-004 | Digital Order Management | Digitalisasi proses pencatatan dan pengelolaan order sales | Must Have |
| EPIC-005 | Management Dashboard | Dashboard monitoring terpusat untuk Manager dan Head of Sales | Must Have |

### 5.2 Ringkasan Fitur per Epic

#### EPIC-001: Admin Panel *(Admin App)*

| Feature ID | Nama Fitur | Role | Prioritas | Referensi SPEC |
|------------|-----------|------|-----------|----------------|
| FEAT-001 | Manajemen user & role (CRUD + assign role) | Admin | Must Have | `docs/features/001-admin-panel/01-user-management---business.md` |
| FEAT-002 | Manajemen outlet (CRUD + koordinat GPS) | Admin | Must Have | `docs/features/001-admin-panel/02-outlet-management---business.md` |
| FEAT-003 | Manajemen katalog produk (CRUD) | Admin | Must Have | `docs/features/001-admin-panel/03-product-catalog---business.md` |

#### EPIC-002: Route Planning *(User App)*

| Feature ID | Nama Fitur | Role | Prioritas | Referensi SPEC |
|------------|-----------|------|-----------|----------------|
| FEAT-004 | Perencanaan rute harian otomatis (urutan outlet optimal) | Sales | Must Have | `docs/features/002-route-planning/01-route-planning---business.md` |
| FEAT-005 | Penyesuaian urutan rute secara manual | Sales | Should Have | `docs/features/002-route-planning/02-route-adjustment---business.md` |

#### EPIC-003: Visit Verification *(User App)*

| Feature ID | Nama Fitur | Role | Prioritas | Referensi SPEC |
|------------|-----------|------|-----------|----------------|
| FEAT-006 | Check-in kunjungan dengan verifikasi GPS (Geolocation API) | Sales | Must Have | `docs/features/003-visit-verification/01-gps-checkin---business.md` |
| FEAT-007 | Pengambilan foto kunjungan via kamera browser | Sales | Must Have | `docs/features/003-visit-verification/02-visit-photo---business.md` |
| FEAT-008 | Timestamp otomatis & riwayat kunjungan | Sales | Must Have | `docs/features/003-visit-verification/03-visit-history---business.md` |

#### EPIC-004: Digital Order Management *(User App)*

| Feature ID | Nama Fitur | Role | Prioritas | Referensi SPEC |
|------------|-----------|------|-----------|----------------|
| FEAT-009 | Form order digital (produk, kuantitas, catatan) | Sales | Must Have | `docs/features/004-digital-order/01-order-form---business.md` |
| FEAT-010 | Riwayat dan status order | Sales, Manager | Must Have | `docs/features/004-digital-order/02-order-history---business.md` |

#### EPIC-005: Management Dashboard *(User App)*

| Feature ID | Nama Fitur | Role | Prioritas | Referensi SPEC |
|------------|-----------|------|-----------|----------------|
| FEAT-011 | Dashboard monitoring aktivitas sales (real-time) | Manager, Head of Sales | Must Have | `docs/features/005-dashboard/01-activity-monitor---business.md` |
| FEAT-012 | Laporan kinerja sales (harian/mingguan) | Manager, Head of Sales | Should Have | `docs/features/005-dashboard/02-performance-report---business.md` |

### 5.3 Fitur di Luar MVP 1 (Fase Berikutnya)

| Fitur | Alasan Ditunda |
|-------|---------------|
| Navigasi turn-by-turn in-app | Lebih optimal di aplikasi mobile; tidak dikembangkan di Web App |
| Mobile App (Android/iOS) | Di luar scope proyek ini; dapat dijadwalkan sebagai Fase 2 |

---

## 6. Non-Functional Requirements

| Kategori | Kebutuhan | Target |
|----------|-----------|--------|
| **Performance** | Waktu loading halaman Web App | < 3 detik |
| **Performance** | Response time API | < 500ms (persentil ke-95) |
| **Availability** | Uptime sistem | ≥99.5% |
| **Security** | Autentikasi pengguna | Username & password dengan sesi terenkripsi (JWT) |
| **Security** | Otorisasi berbasis peran | Role-based access control (Admin, Manager, Sales) |
| **Security** | Transmisi data | HTTPS untuk seluruh komunikasi client-server |
| **Scalability** | Pengguna bersamaan | Mendukung seluruh tim sales aktif secara bersamaan |
| **Usability** | Dukungan browser (Admin App & User App) | Chrome, Firefox, Safari, Edge (2 versi terakhir) |
| **Usability** | Responsive Web (User App) | Dapat digunakan dari smartphone, tablet, dan desktop via browser |
| **Usability** | Admin App | Dioptimalkan untuk desktop/tablet; tidak wajib optimal di smartphone |
| **Offline** | Toleransi koneksi | Notifikasi jelas saat offline; data tersimpan lokal saat koneksi terputus (best effort) |

---

## 7. Batasan & Asumsi

### 7.1 Batasan (Constraints)

| # | Batasan | Dampak |
|---|---------|--------|
| 1 | Timeline: 15 minggu sejak kickoff | Fitur Must Have diprioritaskan; fitur Should Have dapat dijadwalkan ulang |
| 2 | Platform: dua Web App terpisah (Admin App dan User App); tidak ada mobile app native di MVP 1 | Sales dan Manager mengakses User App melalui browser; fitur hardware (GPS, kamera) mengandalkan browser API |
| 3 | Tim pengembangan sesuai struktur yang telah ditetapkan | Kapasitas pengembangan terbatas; tidak ada penambahan resource di luar tim yang ada |
| 4 | Integrasi ERP kompleks di luar scope | Data order tidak tersinkronisasi otomatis dengan sistem back-office existing |

### 7.2 Asumsi Utama

| # | Asumsi | Risiko jika Tidak Terpenuhi |
|---|--------|---------------------------|
| 1 | Sales Representative memiliki akses smartphone dengan browser modern (Chrome/Safari) yang mendukung Geolocation API dan akses kamera | Fitur verifikasi kunjungan tidak dapat berfungsi optimal |
| 2 | Koneksi internet tersedia di area kunjungan sales (setidaknya 3G/4G) | Fitur real-time (rute, order) tidak dapat digunakan |
| 3 | Data outlet/pelanggan (nama, alamat, koordinat GPS) tersedia dan akurat sebagai input sistem | Kualitas optimasi rute sangat bergantung pada akurasi data outlet |
| 4 | Pengguna bersedia mengikuti pelatihan singkat sebelum go-live | Tingkat adopsi rendah dapat menghambat pencapaian target KPI |
| 5 | Admin akan menginput dan memvalidasi data master sebelum sistem digunakan oleh Sales | Sistem tidak dapat berfungsi tanpa data outlet dan produk yang tersedia |

### 7.3 Risiko Utama

| # | Risiko | Tingkat | Mitigasi |
|---|--------|---------|----------|
| 1 | Akurasi GPS browser (Geolocation API) lebih rendah dibanding GPS native di aplikasi mobile | Tinggi | Toleransi radius GPS yang dapat dikonfigurasi; fallback ke konfirmasi manual dengan approval manager |
| 2 | Resistensi adopsi oleh Sales Representative yang terbiasa dengan proses manual | Tinggi | Program onboarding dan pelatihan terstruktur; desain UX yang intuitif dan minimal friction |
| 3 | Kualitas koneksi internet tidak stabil di area lapangan | Sedang | Notifikasi offline yang jelas; simpan data draft lokal untuk disubmit saat online kembali |
| 4 | Data outlet tidak lengkap atau tidak akurat saat go-live | Sedang | Proses validasi dan pembersihan data outlet sebelum go-live; Admin dapat update data outlet via panel |
| 5 | Browser lama di perangkat Sales tidak mendukung Geolocation/Camera API | Sedang | Persyaratan browser minimum dikomunikasikan sejak awal pelatihan |

---

## 8. Glossary

| Istilah | Definisi |
|---------|----------|
| FMCG | Fast-Moving Consumer Goods — produk konsumen yang terjual cepat dengan harga relatif rendah |
| Outlet | Toko atau gerai yang menjadi tujuan kunjungan sales |
| Route Optimization | Proses penentuan urutan kunjungan outlet yang paling efisien berdasarkan jarak dan waktu tempuh |
| Check-in | Proses pencatatan kedatangan sales di suatu outlet, disertai verifikasi GPS dan foto |
| Timestamp | Catatan waktu otomatis yang direkam sistem saat suatu aktivitas terjadi |
| Dashboard | Antarmuka visual yang menampilkan ringkasan data dan metrik kinerja secara real-time |
| GPS / Geolocation API | Teknologi penentuan lokasi; di Web App menggunakan browser Geolocation API |
| JWT | JSON Web Token — mekanisme autentikasi berbasis token untuk API |
| RBAC | Role-Based Access Control — sistem otorisasi berdasarkan peran pengguna (Admin, Manager, Sales) |
| Admin | Peran pengguna dengan akses penuh ke pengelolaan data master sistem |
| MVP 1 | Minimum Viable Product fase pertama — versi Web App yang siap digunakan pengguna |
| API | Application Programming Interface — backend RESTful yang melayani semua request dari Web App |

---

## 9. Persetujuan (Approval)

| Peran | Nama | Tanda Tangan | Tanggal |
|-------|------|--------------|---------|
| Executive Sponsor | TBD | | |
| Department Sponsor | TBD | | |
| Product Manager | | | |

---

<!--
CATATAN DOKUMEN:
- User Stories & Acceptance Criteria → Lihat docs/features/XXX-epic/XX-feature---business.md
- Timeline Proyek → Lihat docs/project/02-PEP.md
- Arsitektur Teknis → Lihat docs/project/03-ITA.md
- Data Model → Lihat docs/project/03-ITA.md Section 6
-->
