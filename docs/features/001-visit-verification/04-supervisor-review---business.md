# BUSINESS SPECIFICATION
## [FEAT-004] Supervisor Review & Approval

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-004 |
| Nama Fitur | Supervisor Review & Approval |
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
> Sistem secara otomatis menandai kunjungan yang mencurigakan atau tidak sesuai (GPS invalid, photo suspicious, schedule mismatch, route deviation), namun tidak ada mekanisme formal untuk supervisor meninjau dan memberikan keputusan akhir. Hal ini menyebabkan ketidakjelasan status kunjungan dan tidak adanya proses appeal untuk worker yang merasa keputusan sistem tidak adil.

**Solution:**
> Dashboard tinjauan dan persetujuan manual untuk supervisor meninjau kunjungan yang ditandai, memberikan keputusan (Valid/Flagged/Invalid) dengan catatan, dan mengomunikasikan keputusan kembali ke worker. Sistem juga memungkinkan revisi keputusan jika diperlukan.

### 1.2 Target Users

| Peran Pengguna | Primary/Secondary | Kebutuhan |
|-----------|-------------------|-------|
| Supervisor | Primary | Meninjau kunjungan yang ditandai, memberikan keputusan, memantau kepatuhan tim |
| Field Worker | Secondary | Menerima notifikasi keputusan supervisor, mengetahui status kunjungan final |
| Manager | Secondary | Melihat laporan approval rate, review response time, dan kepatuhan tim |

### 1.3 Success Metrics

| Metrik | Current | Target | Pengukuran |
|--------|---------|--------|-------------|
| Review response time | Tidak terukur | 90% flagged visits ditinjau dalam 24 jam | Persentase kunjungan flagged yang ditinjau supervisor dalam 24 jam |
| Approval accuracy | Tidak terukur | 95% keputusan supervisor tidak berubah | Persentase keputusan supervisor yang valid dan tidak direvisi |
| Worker satisfaction | Tidak terukur | <5% appeal rate | Persentase worker yang mengajukan appeal terhadap keputusan supervisor |

---

## 2. Ruang Lingkup

### 2.1 Dalam Ruang Lingkup

| # | Kapabilitas | Deskripsi |
|---|------------|-------------|
| 1 | Review Dashboard | Dashboard untuk melihat, memfilter, dan meninjau kunjungan yang ditandai dari berbagai kategori (GPS, photo, schedule, route) |
| 2 | Keputusan Approval dengan Catatan | Supervisor dapat memberikan keputusan: ✅ Valid, ⚠️ Flagged, ❌ Invalid dengan catatan wajib untuk Flagged/Invalid |
| 3 | Notifikasi ke Worker | Notifikasi otomatis ke worker tentang keputusan supervisor beserta alasan dan catatan |
| 4 | Revisi Keputusan | Supervisor dapat mengubah keputusan sebelumnya jika ada informasi baru atau kesalahan terdeteksi |

### 2.2 Di Luar Ruang Lingkup

| # | Item | Alasan |
|---|------|--------|
| - | Tidak ada | Semua kapabilitas terkait review dan approval termasuk dalam ruang lingkup fitur ini |

---

## 3. User Stories

<!--
Plain language user stories. PM defines WHAT the user needs.
QA akan memformalkan ini menjadi Gherkin AC di Testing Spec.
Dev akan menurunkan API/data model di Technical Spec.
-->

### US-01: Dashboard Kunjungan Flagged

> **Sebagai** Supervisor
> **Saya ingin** melihat dashboard dengan semua kunjungan yang ditandai sistem
> **Agara** saya dapat meninjau dan memberikan keputusan untuk setiap kunjungan yang mencurigakan

**Expected Behavior:**
- Supervisor membuka menu "Review Kunjungan"
- Sistem menampilkan dashboard dengan daftar kunjungan yang ditandai
- Setiap kunjungan menampilkan informasi ringkas:
  - Nama worker
  - Tanggal dan waktu check-in
  - Lokasi kunjungan
  - Kategori flag: GPS Invalid / Photo Suspicious / Schedule Mismatch / Route Deviation
  - Status review: Pending / Reviewed
  - Badge prioritas: High / Medium / Low
- Supervisor dapat memfilter berdasarkan:
  - Kategori flag
  - Status review
  - Rentang tanggal
  - Worker tertentu
  - Lokasi
- Supervisor dapat mengurutkan berdasarkan:
  - Tanggal check-in (terbaru/terlama)
  - Prioritas (High → Low)
  - Status review (Pending → Reviewed)
- Sistem menampilkan counter:
  - Total kunjungan flagged
  - Pending review
  - Reviewed hari ini
  - High priority pending
- Supervisor dapat mengekspor laporan kunjungan flagged ke CSV/PDF
- Dashboard diperbarui secara real-time saat kunjungan baru ditandai

---

### US-02: Meninjau Detail Kunjungan

> **Sebagai** Supervisor
> **Saya ingin** melihat detail lengkap kunjungan yang ditandai
> **Agara** saya dapat membuat keputusan yang informasi dan akurat

**Expected Behavior:**
- Supervisor mengklik kunjungan flagged dari dashboard
- Sistem menampilkan halaman detail kunjungan dengan informasi lengkap:

**Informasi Worker:**
- Nama worker
- ID worker
- Tim/divisi
- Foto profil

**Informasi Kunjungan:**
- Nama lokasi
- Alamat lengkap
- Koordinat GPS
- Tanggal dan waktu check-in
- Tanggal dan waktu check-out
- Durasi kunjungan
- Status jadwal (sesuai/tidak sesuai)

**Flag Details (berdasarkan kategori):**
- **GPS Invalid:**
  - Lokasi check-in (koordinat, peta)
  - Lokasi target (koordinat, peta)
  - Jarak dari target
  - Radius geofence
- **Photo Suspicious:**
  - Foto dengan watermark
  - EXIF data lengkap
  - Status validasi EXIF
  - Alasan kecurangan (misal: timestamp lama)
- **Schedule Mismatch:**
  - Jadwal yang seharusnya
  - Kunjungan yang dilakukan
  - Selisih waktu/hari
- **Route Deviation:**
  - Rute yang ditugaskan
  - Rute aktual
  - Tambahan jarak/waktu
  - Lokasi yang dilompati

**Riwayat Kunjungan:**
- Riwayat check-in/check-out ke lokasi ini
- Kunjungan sebelumnya yang serupa
- Performance worker di lokasi ini

**Action Buttons:**
- ✅ Setujui (Valid)
- ⚠️ Tandai Mencurigakan (Flagged)
- ❌ Tolak (Invalid)
- ↻ Revisi Keputusan (jika sudah direview)

- Supervisor dapat melihat keputusan supervisor lain untuk kunjungan yang sama (jika ada)
- Supervisor dapat melihat riwayat revisi keputusan

---

### US-03: Memberikan Keputusan Approval

> **Sebagai** Supervisor
> **Saya ingin** memberikan keputusan (Valid/Flagged/Invalid) dengan catatan
> **Agara** status kunjungan final dan worker menerima kejelasan

**Expected Behavior:**
- Supervisor memilih tombol keputusan dari halaman detail kunjungan

**Opsi 1: ✅ Valid**
- Supervisor mengklik "Setujui (Valid)"
- Sistem menampilkan konfirmasi: "Anda yakin ingin menyetujui kunjungan ini?"
- Jika supervisor konfirmasi:
  - Status kunjungan berubah menjadi "Valid"
  - Flag dihapus
  - Kunjungan dianggap sah
  - Catatan opsional dapat ditambahkan

**Opsi 2: ⚠️ Flagged (Mencurigakan)**
- Supervisor mengklik "Tandai Mencurigakan"
- Sistem menampilkan form wajib:
  - Alasan penandaan (dropdown atau text):
    - Pilih alasan: Lokasi tidak sesuai foto / Waktu kunjungan terlalu singkat / GPS tidak akurat / Lainnya
  - Catatan tambahan (text field, wajib diisi)
- Jika supervisor mengirim:
  - Status kunjungan berubah menjadi "Flagged"
  - Kunjungan tetap dihitung tetapi ditandai untuk monitoring lebih lanjut
  - Worker menerima notifikasi dengan alasan dan catatan

**Opsi 3: ❌ Invalid (Ditolak)**
- Supervisor mengklik "Tolak (Invalid)"
- Sistem menampilkan form wajib:
  - Alasan penolakan (dropdown atau text):
    - Pilih alasan: Foto dari galeri / GPS palsu / Kunjungan fiktif / Lainnya
  - Catatan tambahan (text field, wajib diisi)
  - Tingkat keparahan: Low / Medium / High
- Jika supervisor mengirim:
  - Status kunjungan berubah menjadi "Invalid"
  - Kunjungan tidak dihitung
  - Worker menerima notifikasi dengan alasan dan catatan
  - Jika tingkat keparahan High, notifikasi juga dikirim ke Manager/HR

**Umum:**
- Sistem mencatat siapa supervisor yang memberikan keputusan
- Sistem mencatat timestamp keputusan
- Sistem menyimpan riwayat semua keputusan

---

### US-04: Notifikasi Keputusan ke Worker

> **Sebagai** Field Worker
> **Saya ingin** menerima notifikasi tentang keputusan supervisor
> **Agara** saya mengetahui status final kunjungan dan dapat mengambil tindakan jika perlu

**Expected Behavior:**
- Worker menerima notifikasi ketika supervisor memberikan keputusan

**Jika Keputusan: ✅ Valid**
- Notifikasi berisi:
  - "Kunjungan Anda di [Lokasi] pada [Tanggal] telah disetujui."
  - Status: Valid
  - Tidak ada tindakan lanjutan yang diperlukan

**Jika Keputusan: ⚠️ Flagged**
- Notifikasi berisi:
  - "Kunjungan Anda di [Lokasi] pada [Tanggal] ditandai sebagai mencurigakan."
  - Alasan: [Alasan supervisor]
  - Catatan: [Catatan supervisor]
  - Status: Flagged
  - "Kunjungan ini tetap dihitung tetapi akan dipantau lebih lanjut."
  - Worker dapat mengajukan klarifikasi jika diperlukan

**Jika Keputusan: ❌ Invalid**
- Notifikasi berisi:
  - "Kunjungan Anda di [Lokasi] pada [Tanggal] ditolak."
  - Alasan: [Alasan supervisor]
  - Catatan: [Catatan supervisor]
  - Status: Invalid
  - "Kunjungan ini tidak dihitung. Silakan ulangi kunjungan jika perlu."
  - Worker dapat mengajukan appeal jika merasa keputusan tidak adil

**Umum:**
- Notifikasi dikirim melalui:
  - In-app notification
  - Push notification (jika diizinkan)
- Notifikasi dapat diklik untuk melihat detail lengkap
- Worker dapat melihat riwayat semua notifikasi keputusan
- Worker dapat memfilter notifikasi berdasarkan status (Valid/Flagged/Invalid)

---

### US-05: Revisi Keputusan Supervisor

> **Sebagai** Supervisor
> **Saya ingin** mengubah keputusan sebelumnya jika ada informasi baru atau kesalahan
> **Agara** keputusan yang adil dapat diperbaiki dan worker tidak dirugikan

**Expected Behavior:**
- Supervisor dapat melihat tombol "Revisi Keputusan" pada kunjungan yang sudah direview
- Supervisor mengklik tombol revisi
- Sistem menampilkan form revisi:
  - Keputusan saat ini: [Valid/Flagged/Invalid]
  - Alasan revisi (wajib diisi):
    - Pilih alasan: Informasi baru / Kesalahan / Worker memberikan penjelasan / Lainnya
    - Catatan revisi (text field, wajib diisi)
  - Keputusan baru: pilih ✅ Valid / ⚠️ Flagged / ❌ Invalid
  - Jika keputusan baru Flagged/Invalid, alasan dan catatan baru wajib diisi
- Sistem menampilkan riwayat keputusan sebelumnya:
  - Keputusan lama
  - Supervisor yang memberikan keputusan lama
  - Tanggal keputusan lama
  - Alasan keputusan lama
- Jika supervisor mengirim revisi:
  - Status kunjungan diperbarui
  - Riwayat revisi dicatat:
    - Keputusan lama → Keputusan baru
    - Supervisor yang merevisi
    - Timestamp revisi
    - Alasan revisi
  - Worker menerima notifikasi tentang perubahan keputusan:
    - "Keputusan untuk kunjungan di [Lokasi] pada [Tanggal] telah diperbarui."
    - Keputusan lama: [Status lama]
    - Keputusan baru: [Status baru]
    - Alasan: [Alasan revisi]
- Manager dapat melihat semua revisi keputusan untuk audit
- Sistem mencatat statistik revisi:
  - Berapa kali keputusan diubah
  - Supervisor mana yang paling sering merevisi
  - Alasan revisi paling umum

---

## 4. Business Rules

<!--
Policy-level rules. "Apa yang bisnis butuhkan."
Validasi level field milik Testing Spec atau Technical Spec.
-->

| Rule ID | Rule | Deskripsi |
|---------|------|-------------|
| BR-01 | Wajib Review Flagged Visits | Semua kunjungan yang ditandai sistem harus ditinjau supervisor sebelum dianggap final |
| BR-02 | Catatan Wajib untuk Flagged/Invalid | Supervisor wajib memberikan alasan dan catatan saat menandai kunjungan sebagai Flagged atau Invalid |
| BR-03 | Revisi Keputusan Diperbolehkan | Supervisor dapat merevisi keputusan sebelumnya jika ada informasi baru atau kesalahan terdeteksi |
| BR-04 | Riwayat Revisi Dicatat | Semua revisi keputusan harus dicatat dengan supervisor, timestamp, dan alasan untuk audit |
| BR-05 | Notifikasi Otomatis | Worker menerima notifikasi otomatis setiap kali supervisor memberikan atau merevisi keputusan |
| BR-06 | Valid Tidak Perlu Catatan | Keputusan Valid tidak memerlukan catatan wajib (opsional) |
| BR-07 | High Priority Notification | Jika keputusan Invalid dengan tingkat keparahan High, Manager/HR diberi notifikasi |
| BR-08 | Batas Waktu Review | Target: 90% flagged visits ditinjau dalam 24 jam (bukan aturan keras, tapi target kinerja) |

---

## 5. Open Questions

| # | Pertanyaan | Ditanya Kepada | Status | Jawaban |
|---|----------|----------|--------|--------|
| 1 | Apakah worker dapat mengajukan appeal jika keputusan supervisor dianggap tidak adil? | Business Owner | Pending | - |
| 2 | Berapa batas waktu untuk merevisi keputusan? (misal: 7 hari, 30 hari, atau unlimited) | Business Owner | Pending | - |
| 3 | Apakah perlu sistem eskalasi jika worker dan supervisor tidak sepakat? | Business Owner | Pending | - |
| 4 | Apakah supervisor A dapat merevisi keputusan supervisor B? | Business Owner | Pending | - |
| 5 | Bagaimana jika supervisor tidak merespons kunjungan flagged dalam waktu lama? | Business Owner | Pending | - |

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
FILE LOCATION: docs/features/001-visit-verification/04-supervisor-review---business.md

RELATED FILES:
- 04-supervisor-review---testing.md (QA: AC, test cases, validation)
- 04-supervisor-review---technical.md (Dev: API, data model, implementation)
- 04-supervisor-review---design.md (Design: UI/UX specifications)
- 00-visit-verification---overview.md (Epic context)
-->
