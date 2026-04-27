# TESTING SPECIFICATION
## [FEAT-002] Photo Evidence Capture

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-002 |
| Nama Fitur | Photo Evidence Capture |
| Epic | EPIC-003: Visit Verification |
| Business Spec | [02-photo-evidence---business.md](./02-photo-evidence---business.md) |
| Status | Draft |
| Owner | Arina Saffanah Zakiyyah |
| Dibuat | 2026-04-23 |
| Terakhir Diperbarui | 2026-04-23 |

---

## 1. Test Strategy

### 1.1 Scope of Testing

| Type | In Scope | Out of Scope |
|------|----------|--------------|
| Functional | Photo upload during check-in, EXIF validation, gallery detection, watermark, compression, photo gallery, supervisor review | Video evidence, advanced photo editing, social media sharing |
| Security | Gallery bypass detection, EXIF manipulation detection, authorization (worker/supervisor), file type validation | Penetration testing, malware scanning |
| Performance | Upload time, compression time, image loading performance, thumbnail generation | Load testing (1000+ concurrent uploads), stress testing |
| Usability | Camera interface clarity, error message clarity, gallery usability, review dashboard usability | Accessibility (WCAG compliance), A/B testing |

### 1.2 Risk-Based Priority

| Risk Area | Impact | Likelihood | Test Priority |
|-----------|--------|------------|---------------|
| Gallery/photo library bypass | High | High | P1 - Critical |
| EXIF data manipulation | High | Medium | P1 - Critical |
| Missing EXIF data acceptance | High | Medium | P1 - Critical |
| Large file upload performance | Medium | Medium | P2 - Important |
| Watermark not applied | Medium | Low | P2 - Important |
| Multiple photo uploads confusion | Low | Low | P3 - Nice to have |

### 1.3 Environment Requirements

| Environment | URL | Purpose |
|-------------|-----|---------|
| Staging | https://staging.example.com | QA testing dengan mock camera dan test images |
| UAT | https://uat.example.com | Client acceptance dengan real camera dan photos |
| Device Lab | Physical Android/iOS devices | Real camera testing untuk Maestro |

---

## 2. Acceptance Criteria

<!--
Formalized dari user stories di Business Spec.
Setiap user story mendapat Gherkin scenarios yang mencakup happy path,
validation, error handling, dan edge cases.
-->

### US-01: Upload Foto Saat Check-In

```gherkin
Feature: FEAT-002-US-01 Photo Upload During Check-In
  As Field Worker
  I want mengunggah foto saat melakukan check-in di lokasi kunjungan
  So that ada bukti visual kehadiran saya di lokasi tersebut

  Background:
    Given user is logged in as "Field Worker"
    And user has successfully checked-in to "Toko ABC"
    And check-in photo upload screen is displayed

  @happy-path @maestro
  Scenario: Successful photo upload with watermark
    Given user opens camera interface
    And user captures a photo
    And photo preview is displayed with watermark
    And watermark contains:
      | Timestamp check-in |
      | Nama lokasi kunjungan |
      | Koordinat GPS |
      | Nama field worker |
    When user taps "Upload Foto" button
    Then progress bar should be displayed during upload
    And user should see "Foto Berhasil Diunggah" message
    And photo should be marked as "Uploaded"
    And user should be able to proceed to next step

  @validation @maestro
  Scenario: Cannot proceed without uploading photo
    Given user is on photo upload screen
    And no photo has been uploaded
    When user attempts to proceed without uploading
    Then user should see error "Foto wajib diunggah"
    And user should remain on photo upload screen
    And proceed button should be disabled

  @happy-path @maestro
  Scenario: Retake photo before upload
    Given user has captured a photo
    And photo preview is displayed
    When user taps "Ambil Ulang" button
    Then camera interface should reopen
    And previous photo should be discarded
    And user should be able to capture new photo

  @error-handling @maestro
  Scenario: Handle photo capture failure
    Given user opens camera interface
    And camera hardware is unavailable or blocked
    When user attempts to capture photo
    Then user should see error "Kamera tidak tersedia. Pastikan izin kamera diberikan."
    And user should be prompted to grant camera permission
    When user grants permission
    Then camera should become available

  @edge-case @maestro
  Scenario: Upload multiple photos (max 3)
    Given user has uploaded 1 photo
    When user captures and uploads second photo
    Then photo count should display "2/3"
    When user captures and uploads third photo
    Then photo count should display "3/3"
    And "Tambah Foto" button should be disabled
    And user should see message "Maksimal 3 foto tercapai"
```

---

### US-02: Validasi EXIF Data

```gherkin
Feature: FEAT-002-US-02 EXIF Data Validation
  As Sistem
  I want memvalidasi EXIF data pada foto yang diunggah
  So that hanya foto asli dengan metadata valid yang diterima

  Background:
    Given user is logged in as "Field Worker"
    And user has captured a photo

  @happy-path @maestro
  Scenario: Accept photo with valid EXIF data
    Given photo has complete EXIF data:
      | DateTimeOriginal  | 2026-04-23 09:15:30 |
      | GPSLatitude      | -6.2088             |
      | GPSLongitude     | 106.8456            |
    And photo timestamp is within 1 hour of check-in time
    When user uploads photo
    Then photo should be accepted
    And EXIF validation should pass
    And photo should be marked as "EXIF Valid"

  @validation @maestro
  Scenario: Reject photo with missing EXIF data
    Given photo does not contain DateTimeOriginal in EXIF
    Or photo does not contain GPS coordinates in EXIF
    When user uploads photo
    Then photo should be rejected
    And user should see error "Foto tidak memiliki data lokasi/waktu. Pastikan GPS aktif saat mengambil foto."
    And user should be prompted to capture new photo

  @validation @maestro
  Scenario: Flag photo with timestamp >1 hour from check-in
    Given photo DateTimeOriginal is 2 hours before check-in time
    When user uploads photo
    Then photo should be accepted with warning
    And photo should be flagged as "Mencurigakan"
    And warning should say: "Waktu foto berbeda 2 jam dari waktu check-in"
    And supervisor should be notified of suspicious photo

  @edge-case @maestro
  Scenario: Handle corrupted EXIF data
    Given photo has corrupted or unreadable EXIF data
    When user attempts to upload photo
    Then photo should be rejected
    And user should see error "Foto rusak atau tidak valid. Silakan ambil foto baru."

  @edge-case @maestro
  Scenario: Store original EXIF for audit
    Given photo has valid EXIF data
    When user uploads photo
    Then original EXIF data should be stored separately
    And EXIF data should be accessible for supervisor review
    And EXIF data should not be modified
```

---

### US-03: Deteksi Foto dari Galeri

```gherkin
Feature: FEAT-002-US-03 Gallery Photo Detection
  As Sistem
  I want mendeteksi dan menolak foto yang diambil dari galeri
  So that worker tidak dapat mengunggah foto lama atau bukti palsu

  Background:
    Given user is logged in as "Field Worker"
    And user is on photo upload screen

  @validation @maestro
  Scenario: Force camera interface (no gallery option)
    Given user taps upload button
    Then system should open camera interface directly
    And system should NOT display file picker or gallery option
    And user should only be able to capture from camera

  @validation @maestro
  Scenario: Detect and reject gallery photo
    Given user attempts to upload from gallery (via file picker exploit)
    When system detects gallery photo through:
      | File path contains /gallery/ or /photos/ |
      | Missing camera-specific metadata         |
      | Manipulation indicators detected        |
    Then photo should be rejected
    And user should see error "Harap ambil foto langsung dari kamera. Foto dari galeri tidak diizinkan."
    And camera interface should open automatically

  @error-handling @maestro
  Scenario: Notify supervisor after 3 gallery attempts
    Given user has attempted to upload from gallery 3 times
    When user attempts 4th gallery upload
    Then upload should be rejected
    And supervisor should receive notification:
      """
      Worker: John Doe
      Location: Toko ABC
      Issue: 4x percobaan upload dari galeri
      """
    And notification should include timestamp and worker details

  @edge-case @maestro
  Scenario: Handle legitimate photo with similar characteristics
    Given user captures photo from camera
    And photo has characteristics similar to gallery photo
    When user uploads photo
    Then system should validate based on camera metadata
    And photo should NOT be falsely flagged as gallery photo
    And upload should proceed normally

  @edge-case @maestro
  Scenario: Log all gallery detection attempts
    Given user attempts to upload from gallery
    Then system should log attempt with:
      | Timestamp |
      | Worker ID  |
      | Detection method |
      | File characteristics |
    And log should be accessible for audit
```

---

### US-04: Watermark Otomatis

```gherkin
Feature: FEAT-002-US-04 Automatic Watermark
  As Sistem
  I want menambahkan watermark otomatis pada foto yang diunggah
  So that foto evidence terlindungi dari manipulasi dan mudah diidentifikasi

  Background:
    Given user is logged in as "Field Worker"
    And user has captured a photo

  @happy-path @maestro
  Scenario: Watermark applied automatically
    Given photo is captured with check-in details:
      | Timestamp    | 2026-04-23 09:15:30 |
      | Location     | Toko ABC             |
      | GPS         | -6.2088, 106.8456   |
      | Worker Name | John Doe             |
    When photo is captured
    Then watermark should be automatically applied
    And watermark should be positioned in bottom-right corner
    And watermark should have semi-transparent background
    And watermark text should be:
      | 23/04/2026 09:15 |
      | Toko ABC          |
      | -6.2088, 106.8456 |
      | John Doe          |
    And watermark should be readable on various photo backgrounds

  @validation @maestro
  Scenario: Watermark applied after compression
    Given photo is captured at 5 MB
    When photo is compressed to 500 KB
    Then watermark should still be clearly visible
    And watermark quality should not be degraded by compression

  @edge-case @maestro
  Scenario: Watermark on dark/light backgrounds
    Given photo has very dark background
    When watermark is applied
    Then watermark text should use light color for contrast
    Given photo has very light background
    When watermark is applied
    Then watermark text should use dark color for contrast

  @edge-case @maestro
  Scenario: Store original photo without watermark
    Given photo is captured with watermark
    When photo is uploaded
    Then system should store:
      | Version 1: Photo with watermark (for display) |
      | Version 2: Original photo without watermark (for audit) |
    And supervisor should be able to access original version
```

---

### US-05: Melihat Galeri Foto Worker

```gherkin
Feature: FEAT-002-US-02 Worker Photo Gallery
  As Field Worker
  I want melihat riwayat foto yang pernah saya unggah
  So that saya dapat memverifikasi foto-foto saya dan melihat bukti kehadiran saya

  Background:
    Given user is logged in as "Field Worker"
    And user has uploaded 5 photos across different visits

  @happy-path @maestro @playwright
  Scenario: View personal photo gallery
    Given user opens "Foto Saya" page
    Then user should see gallery of 5 photos with thumbnails
    And each photo should display:
      | Tanggal check-in |
      | Nama lokasi kunjungan |
      | Status (valid/invalid/flagged) |
    And photos should be ordered by date (newest first)

  @happy-path @maestro @playwright
  Scenario: View full-size photo
    Given user is viewing photo gallery
    When user taps on a photo thumbnail
    Then full-size photo should be displayed
    And photo should include watermark
    And photo details should be shown:
      | Tanggal check-in    |
      | Lokasi kunjungan    |
      | GPS koordinat       |
      | Status              |
    And user should be able to download watermarked photo

  @validation @maestro @playwright
  Scenario: Filter photos by date range
    Given user has photos across 3 months
    When user applies date filter "01/04/2026 to 20/04/2026"
    Then only photos within April 1-20 should be displayed
    And other photos should be hidden

  @validation @maestro @playwright
  Scenario: Filter photos by location
    Given user has visited 3 different locations
    When user applies location filter "Toko ABC"
    Then only photos from "Toko ABC" should be displayed
    And photos from other locations should be hidden

  @authorization @maestro @playwright
  Scenario: Worker cannot view other workers' photos
    Given user is logged in as "Field Worker"
    When user attempts to access another worker's photo gallery
    Then access should be denied
    And user should see error "Anda tidak memiliki akses ke foto worker lain"
```

---

### US-06: Review Foto oleh Supervisor

```gherkin
Feature: FEAT-002-US-06 Supervisor Photo Review
  As Supervisor
  I want melihat dan memverifikasi semua foto evidence dari tim saya
  So that saya dapat memastikan kepatuhan dan menandai foto mencurigakan

  Background:
    Given user is logged in as "Supervisor"
    And supervisor has 5 workers in their team
    And workers have uploaded 20 photos total

  @happy-path @playwright
  Scenario: View team photo gallery
    Given supervisor opens "Review Foto" dashboard
    Then supervisor should see gallery of 20 photos from all workers
    And photos should show thumbnails with worker names
    And each photo should display:
      | Worker Name |
      | Tanggal check-in |
      | Nama lokasi kunjungan |
      | Status (valid/invalid/flagged) |

  @happy-path @playwright
  Scenario: Filter photos by worker
    Given supervisor is viewing photo gallery
    When supervisor applies filter "Worker: John Doe"
    Then only John Doe's photos should be displayed
    And other workers' photos should be hidden

  @validation @playwright
  Scenario: Filter photos by status
    Given supervisor is viewing photo gallery
    When supervisor applies filter "Status: Flagged"
    Then only flagged photos should be displayed
    And valid and invalid photos should be hidden

  @happy-path @playwright
  Scenario: View photo details with EXIF
    Given supervisor clicks on a photo
    Then supervisor should see:
      | Full-size photo with watermark |
      | EXIF data lengkap:
        | DateTimeOriginal |
        | GPSLatitude |
        | GPSLongitude |
        | Camera model |
        | Image dimensions |
      | Detail kunjungan:
        | Timestamp check-in |
        | Lokasi kunjungan |
        | Worker name |

  @happy-path @playwright
  Scenario: Mark photo as Valid
    Given supervisor is reviewing a photo
    And photo status is "Pending"
    When supervisor taps "✅ Valid" button
    Then photo status should change to "Valid"
    And photo should be marked as "Kunjungan Terverifikasi"

  @happy-path @playwright
  Scenario: Mark photo as Suspicious with notes
    Given supervisor is reviewing a photo
    When supervisor taps "⚠️ Mencurigakan" button
    And supervisor enters note: "Lokasi tidak sesuai dengan toko"
    And supervisor submits
    Then photo status should change to "Flagged"
    And note should be saved with photo
    And worker should receive notification:
      """
      Foto Anda untuk kunjungan di Toko ABC pada 23/04/2026 ditandai sebagai mencurigakan.
      Catatan: Lokasi tidak sesuai dengan toko
      """

  @error-handling @playwright
  Scenario: Mark photo as Invalid
    Given supervisor is reviewing a photo
    When supervisor taps "❌ Invalid" button
    And supervisor enters reason: "Foto dari galeri"
    And supervisor submits
    Then photo status should change to "Invalid"
    And worker should receive notification of invalid photo
    And incident should be logged for audit
```

---

### US-07: Kompresi Otomatis

```gherkin
Feature: FEAT-002-US-07 Automatic Compression
  As Sistem
  I want mengompresi foto secara otomatis sebelum diunggah
  So that penggunaan storage dan bandwidth efisien

  Background:
    Given user is logged in as "Field Worker"
    And device camera captures high-resolution photos

  @happy-path @maestro
  Scenario: Compress photo to target size
    Given user captures photo at 5 MB
    When photo is compressed
    Then compressed photo should be ≤ 500 KB
    And photo quality should still be suitable for verification
    And aspect ratio should be maintained
    And orientation should be maintained

  @happy-path @maestro
  Scenario: Display compression info to user
    Given user captures photo at 5 MB
    When compression is complete
    Then user should see compression info:
      | Original size: 5 MB |
      | Compressed size: 450 KB |
      | Reduction: 91% |
    And progress should be displayed during compression

  @edge-case @maestro
  Scenario: Compress large photo (10 MB)
    Given user captures photo at 10 MB
    When photo is compressed
    Then compressed photo should be ≤ 500 KB
    And compression should complete within 10 seconds
    And user should see compression progress

  @edge-case @maestro
  Scenario: Handle photo already under 500 KB
    Given user captures photo at 300 KB
    When compression is attempted
    Then photo should NOT be compressed
    And original photo should be used
    Or compression should be minimal (maintain quality)

  @edge-case @maestro
  Scenario: Store original high-quality version
    Given photo is captured and compressed to 500 KB
    When photo is uploaded
    Then system should store:
      | Version 1: Compressed version (500 KB) for display |
      | Version 2: Original high-quality version for audit |
    And supervisor should be able to download original version
```

---

## 3. Validation Rules

<!--
Field-level validation. Derived from business rules and data requirements.
Dev uses this for implementation, QA uses this for test cases.
-->

| Field | Rule | Error Message |
|-------|------|---------------|
| Photo Upload | Required, cannot proceed without photo | "Foto wajib diunggah" |
| Photo Source | Must be from camera (gallery rejected) | "Harap ambil foto langsung dari kamera. Foto dari galeri tidak diizinkan." |
| EXIF DateTimeOriginal | Required | "Foto tidak memiliki data waktu. Pastikan GPS aktif." |
| EXIF GPS Coordinates | Required (latitude, longitude) | "Foto tidak memiliki data lokasi. Pastikan GPS aktif." |
| EXIF Timestamp Diff | Must be within 1 hour of check-in | "Waktu foto berbeda lebih dari 1 jam dari waktu check-in" |
| File Size (Before Compression) | Maximum 10 MB | "Ukuran foto terlalu besar. Maksimal 10 MB." |
| File Size (After Compression) | Target ≤ 500 KB | - (internal validation) |
| Number of Photos | Maximum 3 per check-in | "Maksimal 3 foto tercapai" |
| Gallery Detection | File path, metadata, manipulation indicators | "Foto terdeteksi dari galeri" |
| Watermark | Automatically applied to all photos | - (system validation) |
| Compression | Automatically applied to all photos | - (system validation) |
| Retention | Photos stored minimum 6 months | - (system validation) |

---

## 4. Test Cases

<!--
Detailed test cases expanded from acceptance criteria.
ID format: TC-002-SEQ
-->

### 4.1 Functional Tests

| ID | Scenario | Preconditions | Steps | Expected Result | Priority | Automated |
|----|----------|---------------|-------|-----------------|----------|-----------|
| TC-002-001 | Successful photo upload with watermark | Checked-in, camera available | 1. Capture photo<br>2. Upload | Photo uploaded, watermark applied, success message | P1 | Yes (Maestro) |
| TC-002-002 | Cannot proceed without photo | Checked-in, no photo | 1. Attempt to proceed | Error shown, blocked from proceeding | P1 | Yes (Maestro) |
| TC-002-003 | Retake photo before upload | Photo captured | 1. Tap "Ambil Ulang"<br>2. Capture new photo | Previous photo discarded, new photo captured | P2 | Yes (Maestro) |
| TC-002-004 | Camera permission denied | No camera permission | 1. Attempt to capture photo | Error message, prompt to grant permission | P1 | Yes (Maestro) |
| TC-002-005 | Upload max 3 photos | 2 photos uploaded | 1. Upload 3rd photo | Count shows "3/3", add button disabled | P2 | Yes (Maestro) |
| TC-002-006 | Accept photo with valid EXIF | Photo with complete EXIF | 1. Upload photo | Photo accepted, marked "EXIF Valid" | P1 | Yes (Maestro) |
| TC-002-007 | Reject photo with missing EXIF | Photo without GPS/time EXIF | 1. Upload photo | Photo rejected, error message shown | P1 | Yes (Maestro) |
| TC-002-008 | Flag photo with old timestamp | Photo timestamp 2h old | 1. Upload photo | Photo accepted, flagged "Mencurigakan", supervisor notified | P2 | Yes (Maestro) |
| TC-002-009 | Corrupted EXIF data | Photo with corrupted EXIF | 1. Upload photo | Photo rejected, error message | P2 | Yes (Maestro) |
| TC-002-010 | Store original EXIF for audit | Valid EXIF photo | 1. Upload photo<br>2. Supervisor reviews | Original EXIF accessible to supervisor | P2 | Yes (Playwright) |
| TC-002-011 | Force camera interface | On upload screen | 1. Tap upload | Camera opens, no gallery option | P1 | Yes (Maestro) |
| TC-002-012 | Detect and reject gallery photo | Gallery upload attempted | 1. Upload from gallery | Photo rejected, error shown, camera opens | P1 | Yes (Maestro) |
| TC-002-013 | Notify after 3 gallery attempts | 3 gallery attempts | 1. Attempt 4th gallery upload | Upload rejected, supervisor notified | P1 | Yes (Maestro) |
| TC-002-014 | Log gallery detection attempts | Gallery attempt | 1. Attempt gallery upload | Attempt logged with details | P2 | Yes (Maestro) |
| TC-002-015 | Watermark applied automatically | Photo captured | 1. Capture photo | Watermark visible in correct position | P1 | Yes (Maestro) |
| TC-002-016 | Watermark after compression | Photo compressed | 1. Compress photo | Watermark still clear and visible | P2 | Yes (Maestro) |
| TC-002-017 | Store original without watermark | Photo with watermark | 1. Upload photo | Both versions stored | P2 | Yes (Maestro) |
| TC-002-018 | View personal photo gallery | 5 photos uploaded | 1. Open "Foto Saya" | Gallery shown with thumbnails and metadata | P1 | Yes (Maestro + Playwright) |
| TC-002-019 | View full-size photo | In gallery | 1. Tap photo thumbnail | Full-size shown, details displayed | P2 | Yes (Maestro + Playwright) |
| TC-002-020 | Filter by date range | Photos across 3 months | 1. Apply date filter | Only photos in range shown | P2 | Yes (Maestro + Playwright) |
| TC-002-021 | Filter by location | 3 locations visited | 1. Apply location filter | Only matching location shown | P2 | Yes (Maestro + Playwright) |
| TC-002-022 | Worker cannot view others' photos | Logged as worker | 1. Access other worker gallery | Access denied (403) | P1 | Yes (Maestro + Playwright) |
| TC-002-023 | View team photo gallery | Supervisor, 20 photos | 1. Open "Review Foto" | All photos shown with worker names | P1 | Yes (Playwright) |
| TC-002-024 | Filter by worker | Supervisor viewing | 1. Apply worker filter | Only that worker's photos shown | P2 | Yes (Playwright) |
| TC-002-025 | Filter by status | Supervisor viewing | 1. Apply status filter | Only matching status shown | P2 | Yes (Playwright) |
| TC-002-026 | View photo details with EXIF | Supervisor reviewing | 1. Click photo | Details shown with full EXIF data | P1 | Yes (Playwright) |
| TC-002-027 | Mark photo as Valid | Supervisor, pending photo | 1. Mark "Valid" | Status changes to "Valid" | P1 | Yes (Playwright) |
| TC-002-028 | Mark photo as Suspicious | Supervisor reviewing | 1. Mark "Mencurigakan"<br>2. Add note | Status changes, note saved, worker notified | P1 | Yes (Playwright) |
| TC-002-029 | Mark photo as Invalid | Supervisor reviewing | 1. Mark "Invalid"<br>2. Add reason | Status changes, worker notified, logged | P1 | Yes (Playwright) |
| TC-002-030 | Compress to 500 KB | 5 MB photo | 1. Capture and compress | Compressed to ≤500 KB, quality maintained | P1 | Yes (Maestro) |
| TC-002-031 | Display compression info | Photo compressed | 1. View compression result | Original/compressed sizes shown | P2 | Yes (Maestro) |
| TC-002-032 | Compress large photo (10 MB) | 10 MB photo | 1. Capture and compress | Compressed to ≤500 KB, within 10s | P2 | Yes (Maestro) |
| TC-002-033 | Photo already under 500 KB | 300 KB photo | 1. Capture photo | No compression or minimal compression | P3 | Yes (Maestro) |
| TC-002-034 | Store original high-quality | Photo compressed | 1. Upload photo | Both versions stored | P2 | Yes (Maestro) |

### 4.2 Security Tests

| ID | Scenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-002-S01 | Gallery bypass via file picker | 1. Attempt to access file picker directly | System blocks access, forces camera | P1 |
| TC-002-S02 | EXIF manipulation | 1. Modify EXIF data<br>2. Upload photo | System detects manipulation, rejects photo | P1 |
| TC-002-S03 | Upload non-image file | 1. Attempt to upload .txt file | File type validation rejects upload | P1 |
| TC-002-S04 | Upload malicious image | 1. Upload image with embedded malware | Malware scan blocks upload | P1 |
| TC-002-S05 | Supervisor authorization | 1. Worker attempts to access supervisor endpoint | Access denied (403) | P1 |

### 4.3 Usability Tests

| ID | Scenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-002-U01 | Camera interface clarity | 1. Open camera | Clear capture button, preview visible | P2 |
| TC-002-U02 | Watermark readability | 1. View various photos with watermarks | Watermark readable on all backgrounds | P2 |
| TC-002-U03 | Gallery thumbnail quality | 1. View photo gallery | Thumbnails load quickly, are recognizable | P2 |
| TC-002-U04 | Error message clarity | 1. Trigger various errors | Messages clear, actionable, Indonesian | P2 |
| TC-002-U05 | Review dashboard usability | 1. Supervisor reviews photos | Easy to filter, view details, mark status | P2 |

---

## 5. Test Data

<!--
Specific test accounts, seed data, and fixtures needed.
-->

### 5.1 Test Accounts

| Account | Role | Credentials | Purpose |
|---------|------|-------------|---------|
| test_worker_01 | Field Worker | worker01@test.com / Test123! | Worker testing |
| test_worker_02 | Field Worker | worker02@test.com / Test123! | Multiple worker scenarios |
| test_supervisor | Supervisor | supervisor@test.com / Test123! | Dashboard and review |
| test_manager | Manager | manager@test.com / Test123! | Audit and reporting |

### 5.2 Test Data Sets

| Dataset | Description | Location |
|---------|-------------|----------|
| photos_with_exif.json | Test photos with valid EXIF data | tests/mobile/fixtures/photos/ |
| photos_without_exif.json | Test photos missing EXIF | tests/mobile/fixtures/photos/ |
| photos_compressed.json | Various sizes for compression testing | tests/mobile/fixtures/photos/ |

### 5.3 Test Images

| Image Name | Size | EXIF | Purpose |
|------------|------|------|---------|
| valid_photo_with_gps.jpg | 2 MB | Complete | Happy path testing |
| photo_no_exif.jpg | 1.5 MB | Missing | EXIF validation testing |
| old_timestamp.jpg | 3 MB | Old (2h) | Timestamp validation |
| gallery_photo.jpg | 500 KB | Complete | Gallery detection |
| large_photo.jpg | 10 MB | Complete | Compression testing |
| dark_background.jpg | 2 MB | Complete | Watermark contrast |
| light_background.jpg | 2 MB | Complete | Watermark contrast |

---

## 6. Automation Plan

### 6.1 Coverage

| Category | Total | Automated | Manual | Reason for Manual |
|----------|-------|-----------|--------|-------------------|
| Functional | 34 | 30 | 4 | TC-002-004, TC-002-032, TC-002-033 require physical device testing |
| Security | 5 | 4 | 1 | TC-002-S04 requires malware scan setup |
| Usability | 5 | 0 | 5 | Visual/subjective assessment required |

**Total Automation Coverage: 77% (34/44 test cases)**

### 6.2 Playwright Mapping (Web - Supervisor Dashboard)

| Test Case | Playwright File | Page Object | Status |
|-----------|-----------------|-------------|--------|
| TC-002-018 to TC-002-022 | features/worker-gallery.spec.ts | pages/WorkerGalleryPage.ts | Pending |
| TC-002-023 to TC-002-029 | features/supervisor-review.spec.ts | pages/SupervisorReviewPage.ts<br>pages/PhotoDetailPage.ts | Pending |
| TC-002-010 | features/exif-display.spec.ts | pages/PhotoDetailPage.ts | Pending |

### 6.3 Maestro Mapping (Mobile - Field Worker App)

| Test Case | Maestro File | Status |
|-----------|--------------|--------|
| TC-002-001 to TC-002-005 | flows/photo-upload-happy.yaml<br>flows/photo-no-upload.yaml<br>flows/retake-photo.yaml<br>flows/camera-permission.yaml<br>flows/max-3-photos.yaml | Pending |
| TC-002-006 to TC-002-010 | flows/exif-valid.yaml<br>flows/exif-missing.yaml<br>flows/exif-old-timestamp.yaml<br>flows/exif-corrupted.yaml<br>flows/exif-storage.yaml | Pending |
| TC-002-011 to TC-002-014 | flows/force-camera.yaml<br>flows/gallery-detection.yaml<br>flows/gallery-notify.yaml<br>flows/gallery-logging.yaml | Pending |
| TC-002-015 to TC-002-017 | flows/watermark-auto.yaml<br>flows/watermark-compression.yaml<br>flows/watermark-orig-storage.yaml | Pending |
| TC-002-030 to TC-002-034 | flows/compress-target.yaml<br>flows/compress-info.yaml<br>flows/compress-large.yaml<br>flows/compress-small.yaml<br>flows/compress-orig-storage.yaml | Pending |

### 6.4 Selector Strategy

| Element | Selector | Type | Priority |
|---------|----------|------|----------|
| Upload photo button | data-testid="upload-photo-button" | data-testid | Highest |
| Capture button | data-testid="capture-button" | data-testid | Highest |
| Retake button | data-testid="retake-button" | data-testid | Highest |
| Photo thumbnail | data-testid="photo-thumbnail-{id}" | data-testid | Highest |
| Status badge (valid/invalid/flagged) | data-testid="status-badge" | data-testid | High |
| Filter dropdown | aria-label="Filter photos" | aria-label | High |
| Mark as valid button | data-testid="mark-valid-btn" | data-testid | Highest |
| Mark as suspicious button | data-testid="mark-suspicious-btn" | data-testid | Highest |
| Mark as invalid button | data-testid="mark-invalid-btn" | data-testid | Highest |
| EXIF data display | data-testid="exif-data" | data-testid | Medium |
| Compression info | data-testid="compression-info" | data-testid | Medium |

**Selector Priority:** data-testid > aria-label/role > CSS class > text

---

## 7. UAT Scenarios

<!--
Client-facing test scripts. Ditulis dalam Bahasa Indonesia sesuai konfigurasi project.
Generated dari acceptance criteria untuk stakeholder non-technical.
-->

### UAT Scenario 01: Upload Foto Berhasil dengan Watermark

**Objective:** Memastikan field worker dapat mengunggah foto dengan watermark otomatis saat check-in.

**Prerequisites:**
- Field worker sudah login ke aplikasi
- Worker sudah berhasil check-in di lokasi kunjungan
- Kamera tersedia dan izin kamera diberikan
- GPS aktif

**Steps:**
1. Buka aplikasi di smartphone
2. Setelah check-in berhasil, layar upload foto akan muncul
3. Tap tombol "Ambil Foto"
4. Arahkan kamera ke lokasi kunjungan
5. Tap tombol capture
6. Lihat preview foto dengan watermark
7. Tap "Upload Foto"
8. Tunggu proses upload selesai

**Expected Results:**
- Preview foto menampilkan watermark dengan:
  - Timestamp check-in
  - Nama lokasi kunjungan
  - Koordinat GPS
  - Nama worker
- Progress bar ditampilkan selama upload
- Pesan "Foto Berhasil Diunggah" muncul
- Worker dapat melanjutkan ke langkah berikutnya

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 02: Penolakan Foto Tanpa Data EXIF

**Objective:** Memastikan sistem menolak foto yang tidak memiliki data EXIF (GPS dan timestamp).

**Prerequisites:**
- Field worker sudah login
- GPS non-aktif saat mengambil foto
- Layar upload foto terbuka

**Steps:**
1. Buka aplikasi
2. Matikan GPS di device
3. Ambil foto (tanpa GPS aktif)
4. Coba upload foto

**Expected Results:**
- Sistem menolak foto
- Pesan error ditampilkan: "Foto tidak memiliki data lokasi/waktu. Pastikan GPS aktif saat mengambil foto."
- Worker diminta mengambil foto baru
- Worker tidak dapat melanjutkan tanpa foto valid

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 03: Deteksi dan Penolakan Foto dari Galeri

**Objective:** Memastikan sistem mendeteksi dan menolak foto yang diambil dari galeri/photo library.

**Prerequisites:**
- Field worker sudah login
- Layar upload foto terbuka

**Steps:**
1. Buka aplikasi
2. Coba akses galeri/photo library device (jika memungkinkan)
3. Pilih foto dari galeri
4. Coba upload foto dari galeri

**Expected Results:**
- Sistem membuka kamera langsung (tidak ada opsi galeri)
- Jika ada cara mengakses galeri, foto ditolak
- Pesan error ditampilkan: "Harap ambil foto langsung dari kamera. Foto dari galeri tidak diizinkan."
- Kamera dibuka secara otomatis
- Upload dari galeri diblokir

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 04: Melihat Galeri Foto Pribadi

**Objective:** Memastikan worker dapat melihat riwayat foto yang pernah diunggah.

**Prerequisites:**
- Field worker sudah login
- Worker telah mengunggah minimal 3 foto dari kunjungan berbeda

**Steps:**
1. Buka aplikasi
2. Buka menu "Foto Saya"
3. Lihat galeri foto
4. Tap salah satu foto untuk melihat detail
5. Coba filter berdasarkan tanggal atau lokasi

**Expected Results:**
- Galeri menampilkan semua foto dengan thumbnail
- Setiap foto menampilkan: Tanggal, Lokasi, Status
- Tap foto menampilkan versi full-size dengan detail lengkap
- Filter berfungsi untuk mempersempit hasil
- Worker hanya melihat foto mereka sendiri

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 05: Supervisor Review dan Mark Foto

**Objective:** Memastikan supervisor dapat melihat, meninjau, dan menandai foto evidence.

**Prerequisites:**
- Supervisor login ke web dashboard
- Tim telah mengunggah minimal 10 foto

**Steps:**
1. Buka web dashboard supervisor
2. Buka menu "Review Foto"
3. Lihat galeri foto dari tim
4. Filter berdasarkan worker atau status
5. Klik salah satu foto untuk melihat detail
6. Lihat data EXIF lengkap
7. Tandai foto sebagai "Valid", "Mencurigakan", atau "Invalid"
8. Jika "Mencurigakan" atau "Invalid", tambahkan catatan

**Expected Results:**
- Supervisor melihat semua foto tim dengan thumbnail
- Filter berfungsi (worker, status, tanggal, lokasi)
- Detail foto menampilkan: Full-size foto, EXIF data, detail kunjungan
- Tombol mark berfungsi: ✅ Valid, ⚠️ Mencurigakan, ❌ Invalid
- Catatan dapat ditambahkan untuk foto yang ditandai
- Worker menerima notifikasi jika foto ditandai

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 06: Kompresi Foto Otomatis

**Objective:** Memastikan sistem mengompresi foto secara otomatis dan efisien.

**Prerequisites:**
- Field worker sudah login
- Layar upload foto terbuka
- Kamera device menghasilkan foto besar (3-5 MB)

**Steps:**
1. Buka aplikasi
2. Ambil foto (sekitar 5 MB)
3. Lihat informasi kompresi yang ditampilkan
4. Upload foto

**Expected Results:**
- Sistem menampilkan informasi kompresi:
  - Ukuran asli (misal: 5 MB)
  - Ukuran setelah kompresi (≤ 500 KB)
  - Persentase pengurangan
- Progress bar ditampilkan selama kompresi
- Kualitas foto setelah kompresi masih baik untuk verifikasi
- Upload berhasil dilakukan

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

## 8. Sign-off

| Role | Nama | Tanggal | Status |
|------|------|------|--------|
| QA Lead | | | Pending |
| PM | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-visit-verification/02-photo-evidence---testing.md

RELATED FILES:
- 02-photo-evidence---business.md (PM: user stories, business rules)
- 02-photo-evidence---technical.md (Dev: API, data model)
- 02-photo-evidence---design.md (Design: UI/UX specifications)

GENERATED BY: QA Engineer (mengikuti user stories dari Business Spec)
-->
