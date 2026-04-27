# TESTING SPECIFICATION
## [FEAT-001] Location Check-In/Out System

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-001 |
| Nama Fitur | Location Check-In/Out System |
| Epic | EPIC-003: Visit Verification |
| Business Spec | [01-location-checkin---business.md](./01-location-checkin---business.md) |
| Status | Draft |
| Owner | Arina Saffanah Zakiyyah |
| Dibuat | 2026-04-23 |
| Terakhir Diperbarui | 2026-04-23 |

---

## 1. Test Strategy

### 1.1 Scope of Testing

| Type | In Scope | Out of Scope |
|------|----------|--------------|
| Functional | Check-in/check-out flow, geofence validation, GPS validation, real-time tracking, history view, notifications | Schedule/route validation (FEAT-003), photo evidence (FEAT-002) |
| Security | GPS spoofing detection, authorization (worker/supervisor/manager), data integrity | Penetration testing, advanced security audits |
| Performance | GPS location retrieval time, check-in/check-out response time, real-time tracking update interval | Load testing (1000+ concurrent users), stress testing |
| Usability | Check-in/check-out UI clarity, error message clarity, dashboard usability for supervisors | Accessibility (WCAG compliance), A/B testing |

### 1.2 Risk-Based Priority

| Risk Area | Impact | Likelihood | Test Priority |
|-----------|--------|------------|---------------|
| GPS/Location validation failure | High | High | P1 - Critical |
| Geofence bypass (cheating) | High | Medium | P1 - Critical |
| Check-in without GPS permission | High | Medium | P1 - Critical |
| Real-time tracking inaccuracy | Medium | Medium | P2 - Important |
| Multiple concurrent check-ins | Medium | Low | P2 - Important |
| History view data inconsistency | Low | Low | P3 - Nice to have |

### 1.3 Environment Requirements

| Environment | URL | Purpose |
|-------------|-----|---------|
| Staging | https://staging.example.com | QA testing dengan mock GPS |
| UAT | https://uat.example.com | Client acceptance dengan real GPS |
| Device Lab | Physical Android/iOS devices | Real GPS testing untuk Maestro |

---

## 2. Acceptance Criteria

<!--
Formalized dari user stories di Business Spec.
Setiap user story mendapat Gherkin scenarios yang mencakup happy path,
validation, error handling, dan edge cases.
-->

### US-01: Check-In di Lokasi Kunjungan

```gherkin
Feature: FEAT-001-US-01 Location Check-In
  As Field Worker
  I want melakukan check-in saat tiba di lokasi kunjungan
  So that kehadiran saya tercatat dan terverifikasi bahwa saya berada di lokasi yang benar

  Background:
    Given user is logged in as "Field Worker"
    And user has granted GPS permission
    And user has 1 scheduled visit for today at "Toko ABC" (lat: -6.2088, lng: 106.8456)

  @happy-path @playwright @maestro
  Scenario: Successful check-in within geofence
    Given user is located within 100 meters of "Toko ABC"
    When user selects "Toko ABC" visit from the dashboard
    And user taps "Check-In" button
    Then user should see "Check-In Berhasil" message
    And system should record timestamp and GPS coordinates
    And visit status should change to "Checked-In"
    And user should not be able to check-in again for this visit

  @validation @playwright @maestro
  Scenario: Check-in rejected when GPS is disabled
    Given user has disabled GPS on device
    When user selects "Toko ABC" visit from the dashboard
    And user taps "Check-In" button
    Then user should see error message "GPS harus aktif untuk check-in"
    And check-in should not be recorded
    And user should be prompted to enable GPS

  @validation @playwright @maestro
  Scenario: Check-in rejected when outside geofence
    Given user is located 150 meters away from "Toko ABC"
    When user selects "Toko ABC" visit from the dashboard
    And user taps "Check-In" button
    Then user should see error message "Lokasi check-in Anda berada di luar area yang ditentukan. Jarak Anda: 150 meter dari lokasi kunjungan."
    And system should display map with current location and target location
    And check-in should not be recorded

  @error-handling @playwright @maestro
  Scenario: Check-in rejected when location permission denied
    Given user has denied GPS permission
    When user opens the app
    Then user should see prompt "Izin lokasi diperlukan untuk check-in"
    And user should not be able to access check-in feature
    When user grants GPS permission
    Then check-in feature should become available

  @edge-case @playwright @maestro
  Scenario: Check-in at exact geofence boundary (100 meters)
    Given user is located exactly 100 meters from "Toko ABC"
    When user selects "Toko ABC" visit from the dashboard
    And user taps "Check-In" button
    Then check-in should be successful
    And user should see "Check-In Berhasil" message

  @edge-case @playwright @maestro
  Scenario: Check-in when GPS signal is weak
    Given user is in area with weak GPS signal
    And GPS accuracy is low (>50 meters)
    When user attempts to check-in
    Then user should see warning "Akurasi GPS rendah. Pindah ke area dengan sinyal lebih baik."
    And check-in should be allowed but flagged as "Low GPS Accuracy"
```

---

### US-02: Check-Out Selesai Kunjungan

```gherkin
Feature: FEAT-001-US-02 Location Check-Out
  As Field Worker
  I want melakukan check-out saat menyelesaikan kunjungan
  So that durasi kunjungan tercatat dan saya dapat melanjutkan ke kunjungan berikutnya

  Background:
    Given user is logged in as "Field Worker"
    And user has checked-in to "Toko ABC" at 09:00
    And visit status is "Checked-In"

  @happy-path @playwright @maestro
  Scenario: Successful check-out with duration calculation
    Given user has completed the visit
    When user opens current visit dashboard
    And user taps "Check-Out" button
    Then user should see "Check-Out Berhasil" message
    And system should record check-out timestamp
    And system should calculate and display visit duration
    And visit status should change to "Completed"
    And user should be able to check-in to next visit

  @validation @playwright @maestro
  Scenario: Cannot check-in to new visit without check-out
    Given user is currently checked-in to "Toko ABC"
    And user has another scheduled visit "Toko DEF"
    When user selects "Toko DEF" visit
    And user attempts to check-in
    Then user should see error message "Anda harus check-out dari kunjungan saat ini terlebih dahulu"
    And check-in to "Toko DEF" should be prevented

  @edge-case @playwright @maestro
  Scenario: Check-out after minimum visit duration (5 minutes)
    Given user has checked-in at 09:00
    And user attempts to check-out at 09:04 (4 minutes)
    When user taps "Check-Out" button
    Then check-out should be allowed
    And visit should be flagged as "Kunjungan Mencurigakan (Durasi < 5 menit)"
    And supervisor should be notified

  @edge-case @playwright @maestro
  Scenario: Check-out after extended visit (8+ hours)
    Given user has checked-in at 09:00
    And user attempts to check-out at 17:30 (8.5 hours)
    When user taps "Check-Out" button
    Then check-out should be allowed
    And visit should be flagged as "Kunjungan Terbuka"
    And supervisor should receive notification

  @error-handling @playwright @maestro
  Scenario: Check-out when GPS is unavailable
    Given user is currently checked-in
    And GPS becomes unavailable during visit
    When user taps "Check-Out" button
    Then check-out should be allowed
    And system should use last known location
    And user should see warning "Lokasi check-out menggunakan data terakhir"
```

---

### US-03: Monitoring Kehadiran Real-Time

```gherkin
Feature: FEAT-001-US-03 Real-Time Attendance Monitoring
  As Supervisor
  I want melihat lokasi worker yang sedang check-in secara real-time
  So that saya dapat memantau kehadiran tim dan merespons jika ada masalah

  Background:
    Given user is logged in as "Supervisor"
    And supervisor has 5 field workers in their team
    And 2 workers are currently checked-in

  @happy-path @playwright
  Scenario: View real-time dashboard with active workers
    Given supervisor opens "Monitoring Kehadiran" dashboard
    Then supervisor should see list of all 5 workers
    And 2 workers should be displayed as "Checked-In" with green indicator
    And 3 workers should be displayed as "Not Checked-In" with gray indicator
    And each checked-in worker should display:
      | Worker Name | Location | Check-In Time | Duration |
      | John Doe    | Toko ABC | 09:15         | 25 min   |

  @happy-path @playwright
  Scenario: Real-time location updates
    Given supervisor is viewing monitoring dashboard
    And "John Doe" checks in at "Toko GHI"
    Then dashboard should update within 30 seconds
    And "John Doe" should appear in checked-in list
    And supervisor should see notification "John Doe checked-in at Toko GHI"

  @validation @playwright
  Scenario: Filter workers by status
    Given supervisor is viewing monitoring dashboard
    When supervisor applies filter "Checked-In Only"
    Then only 2 checked-in workers should be displayed
    When supervisor applies filter "Not Checked-In"
    Then only 3 workers not checked-in should be displayed

  @validation @playwright
  Scenario: Identify workers checked-in outside geofence
    Given "Jane Smith" is checked-in 150m outside target location
    When supervisor views monitoring dashboard
    Then "Jane Smith" should have red indicator
    And "Jane Smith" location should show "Outside Geofence" badge
    And supervisor should see alert "Jane Smith di luar lokasi yang ditentukan"

  @edge-case @playwright
  Scenario: Real-time update when worker checks-out
    Given supervisor is viewing monitoring dashboard
    And "John Doe" is currently checked-in
    When "John Doe" checks-out
    Then "John Doe" should be removed from checked-in list
    And "John Doe" status should change to "Completed"
    And dashboard should update within 30 seconds

  @edge-case @playwright
  Scenario: View worker visit details
    Given supervisor is viewing monitoring dashboard
    When supervisor clicks on "John Doe"
    Then supervisor should see detailed visit information:
      | Field           | Value                    |
      | Worker          | John Doe                 |
      | Location        | Toko ABC                 |
      | Check-In Time   | 09:15                    |
      | Check-Out Time  | -                        |
      | Duration        | 25 minutes               |
      | GPS Coordinates | -6.2088, 106.8456        |
      | Status          | Checked-In (Valid)       |
```

---

### US-04: Melihat Riwayat Check-In/Out

```gherkin
Feature: FEAT-001-US-04 Attendance History View
  As Field Worker / Supervisor / Manager
  I want melihat riwayat check-in/check-out dengan timestamp dan lokasi
  So that saya dapat memverifikasi kehadiran dan menindaklanjuti jika ada perbedaan

  Background:
    Given user is logged in
    And user has completed 3 visits in the past week

  @happy-path @playwright @maestro
  Scenario: Field worker views own history
    Given user is logged in as "Field Worker"
    When user opens "Riwayat Kunjungan" page
    Then user should see list of their 3 completed visits
    And each visit should display:
      | Date       | Location  | Check-In | Check-Out | Duration   |
      | 2026-04-20 | Toko ABC  | 09:15    | 10:30     | 1h 15m     |
      | 2026-04-21 | Toko DEF  | 14:00    | 15:10     | 1h 10m     |
      | 2026-04-22 | Toko GHI  | 10:00    | 11:00     | 1h 00m     |

  @happy-path @playwright
  Scenario: Supervisor views team history
    Given user is logged in as "Supervisor"
    And supervisor has 5 workers in their team
    When supervisor opens "Riwayat Kunjungan Tim" page
    Then supervisor should see all visits from their 5 workers
    And supervisor should be able to filter by worker name
    And supervisor should be able to filter by date range

  @validation @playwright @maestro
  Scenario: Filter history by date range
    Given user has 10 visits across 3 months
    When user applies date filter "01/04/2026 to 20/04/2026"
    Then only visits within April 1-20 should be displayed
    And other visits should be hidden

  @validation @playwright @maestro
  Scenario: Filter history by location
    Given user has visited 3 different locations
    When user applies location filter "Toko ABC"
    Then only visits to "Toko ABC" should be displayed
    And visits to other locations should be hidden

  @happy-path @playwright @maestro
  Scenario: View detailed visit information
    Given user is viewing visit history
    When user clicks on visit to "Toko ABC" on 2026-04-20
    Then user should see detailed information:
      | Field           | Value                    |
      | Visit Date      | 2026-04-20               |
      | Location        | Toko ABC                 |
      | Check-In Time   | 09:15                    |
      | Check-Out Time  | 10:30                    |
      | Duration        | 1h 15m                   |
      | Check-In GPS    | -6.2088, 106.8456        |
      | Check-Out GPS   | -6.2085, 106.8458        |
      | Status          | Valid (Within Geofence)  |
    And user should see map with check-in and check-out locations

  @edge-case @playwright @maestro
  Scenario: View visit with invalid location
    Given user has a visit flagged as "Outside Geofence"
    When user clicks on that visit
    Then visit should show "Invalid Location" badge
    And map should display both user location and target location
    And distance from target should be displayed (e.g., "150m from location")

  @authorization @playwright
  Scenario: Field worker cannot view other workers' history
    Given user is logged in as "Field Worker"
    When user attempts to access history of another worker
    Then access should be denied
    And user should see error "Anda tidak memiliki akses ke riwayat worker lain"
```

---

### US-05: Notifikasi Lokasi Invalid

```gherkin
Feature: FEAT-001-US-05 Invalid Location Notifications
  As Field Worker
  I want menerima notifikasi jika check-in saya ditolak karena lokasi invalid
  So that saya memahami masalah dan dapat mengambil tindakan yang tepat

  Background:
    Given user is logged in as "Field Worker"
    And user has scheduled visit at "Toko ABC" (lat: -6.2088, lng: 106.8456)

  @happy-path @playwright @maestro
  Scenario: Receive clear error message when outside geofence
    Given user is located 150m away from "Toko ABC"
    When user attempts to check-in
    Then user should see error notification:
      """
      Lokasi check-in Anda berada di luar area yang ditentukan.
      Jarak Anda: 150 meter dari lokasi kunjungan.
      """
    And notification should remain visible until dismissed
    And user should see map showing:
      | Current location marker (red) |
      | Target location marker (green) |
      | Distance indicator: 150m       |

  @happy-path @playwright @maestro
  Scenario: Retry after moving to correct location
    Given user is 150m away and has received invalid location error
    When user moves to within 50m of target location
    And user taps "Coba Lagi" button
    Then check-in should be successful
    And user should see "Check-In Berhasil" message

  @error-handling @playwright @maestro
  Scenario: Report incorrect geofence
    Given user is at correct location but geofence appears incorrect
    And user has received invalid location error
    When user taps "Laporkan Masalah Lokasi" button
    Then user should see "Laporan Masalah" form
    And form should include:
      | Field              | Type    |
      | Keterangan         | Text    |
      | Foto Lokasi        | Upload  |
      | Koordinat Saat Ini | Display |
    When user submits report
    Then supervisor should receive notification
    And report should be marked for review

  @validation @playwright @maestro
  Scenario: Supervisor notified after 3 failed attempts
    Given user has attempted to check-in outside geofence 3 times
    When user attempts 4th check-in outside geofence
    Then check-in should be rejected
    And supervisor should receive notification:
      """
      Worker: John Doe
      Location: Toko ABC
      Issue: 4x check-in attempts outside geofence
      """
    And notification should include timestamp and location details

  @edge-case @playwright @maestro
  Scenario: No notification when within geofence
    Given user is within 50m of target location
    When user successfully checks-in
    Then no error notification should be displayed
    And only success confirmation should be shown

  @edge-case @playwright @maestro
  Scenario: Multiple notifications for different visits
    Given user has 2 scheduled visits
    And user attempts check-in at wrong location for first visit
    When user switches to second visit
    And attempts check-in at wrong location
    Then user should receive 2 separate notifications
    And notifications should be distinguishable by location name
```

---

## 3. Validation Rules

<!--
Field-level validation. Derived from business rules and data requirements.
Dev uses this for implementation, QA uses this for test cases.
-->

| Field | Rule | Error Message |
|-------|------|---------------|
| GPS Permission | Required before check-in | "GPS harus aktif untuk check-in" |
| GPS Status | Must be enabled/high accuracy | "Akurasi GPS rendah. Pindah ke area dengan sinyal lebih baik." |
| Geofence Radius | Within 100 meters of target | "Lokasi check-in Anda berada di luar area yang ditentukan. Jarak Anda: [X] meter dari lokasi kunjungan." |
| Check-In Timestamp | Must not be in future | "Waktu check-in tidak valid" |
| Check-In Timestamp | Must be on scheduled date | "Kunjungan ini dijadwalkan untuk tanggal [date]" |
| Check-Out Before Check-In | Not allowed | "Anda harus check-in terlebih dahulu" |
| Multiple Check-Ins | Not allowed without check-out | "Anda harus check-out dari kunjungan saat ini terlebih dahulu" |
| Visit Duration | < 5 minutes triggers warning | "Kunjungan mencurigakan (durasi < 5 menit)" |
| Visit Duration | > 8 hours triggers warning | "Kunjungan terbuka (durasi > 8 jam)" |
| Location Coordinates | Required format: decimal degrees | "Format koordinat tidak valid" |
| Failed Attempts | > 3 outside geofence notifies supervisor | "Terlalu banyak percobaan check-in di luar lokasi" |

---

## 4. Test Cases

<!--
Detailed test cases expanded from acceptance criteria.
ID format: TC-001-SEQ
-->

### 4.1 Functional Tests

| ID | Scenario | Preconditions | Steps | Expected Result | Priority | Automated |
|----|----------|---------------|-------|-----------------|----------|-----------|
| TC-001-001 | Successful check-in within geofence | GPS enabled, within 100m | 1. Open app<br>2. Select visit<br>3. Tap Check-In | Check-in successful, timestamp/GPS recorded, status changed | P1 | Yes (Playwright + Maestro) |
| TC-001-002 | Check-in rejected when GPS disabled | GPS disabled | 1. Open app<br>2. Select visit<br>3. Tap Check-In | Error message shown, check-in blocked | P1 | Yes (Playwright + Maestro) |
| TC-001-003 | Check-in rejected outside geofence | >100m from location | 1. Open app<br>2. Select visit<br>3. Tap Check-In | Error with distance shown, map displayed | P1 | Yes (Playwright + Maestro) |
| TC-001-004 | Check-in at exact boundary (100m) | Exactly 100m away | 1. Open app<br>2. Select visit<br>3. Tap Check-In | Check-in successful | P2 | Yes (Maestro) |
| TC-001-005 | Check-in with weak GPS signal | Low accuracy GPS | 1. Open app<br>2. Select visit<br>3. Tap Check-In | Warning shown, check-in allowed with flag | P2 | Yes (Maestro) |
| TC-001-006 | Successful check-out with duration | Checked-in visit | 1. Open dashboard<br>2. Tap Check-Out | Check-out successful, duration calculated | P1 | Yes (Playwright + Maestro) |
| TC-001-007 | Cannot check-in without check-out | Active check-in | 1. Select new visit<br>2. Attempt check-in | Error message, new check-in blocked | P1 | Yes (Playwright + Maestro) |
| TC-001-008 | Check-out after minimum duration | Checked-in 4 min ago | 1. Tap Check-Out | Check-out allowed, flagged as suspicious | P2 | Yes (Playwright + Maestro) |
| TC-001-009 | Check-out after extended duration | Checked-in 8+ hours ago | 1. Tap Check-Out | Check-out allowed, flagged as open visit | P2 | Yes (Playwright + Maestro) |
| TC-001-010 | Supervisor views real-time dashboard | 2 workers checked-in | 1. Open dashboard | Workers displayed with locations, status, duration | P1 | Yes (Playwright) |
| TC-001-011 | Dashboard updates in real-time | Worker checks-in | 1. Monitor dashboard | Dashboard updates within 30s, notification shown | P1 | Yes (Playwright) |
| TC-001-012 | Dashboard shows out-of-geofence workers | Worker outside location | 1. Open dashboard | Worker shown with red indicator | P1 | Yes (Playwright) |
| TC-001-013 | Worker views own history | 3 completed visits | 1. Open history page | All visits shown with timestamps, locations, duration | P1 | Yes (Playwright + Maestro) |
| TC-001-014 | Supervisor views team history | Team with 5 workers | 1. Open team history | All team visits shown, filterable by worker | P1 | Yes (Playwright) |
| TC-001-015 | Filter history by date range | Visits across 3 months | 1. Apply date filter | Only visits in range shown | P2 | Yes (Playwright + Maestro) |
| TC-001-016 | View detailed visit information | Completed visit | 1. Click on visit | Details shown including GPS coords, map | P2 | Yes (Playwright + Maestro) |
| TC-001-017 | Invalid location error notification | 150m from location | 1. Attempt check-in | Error with distance, map with markers shown | P1 | Yes (Playwright + Maestro) |
| TC-001-018 | Report incorrect geofence | Wrong location error | 1. Tap "Report Issue" | Form displayed, supervisor notified | P2 | Yes (Playwright + Maestro) |
| TC-001-019 | Supervisor notified after 3 failed attempts | 3 failed check-ins | 1. Attempt 4th check-in | Check-in rejected, supervisor notified | P1 | Yes (Playwright + Maestro) |
| TC-001-020 | Retry after moving to correct location | Failed check-in | 1. Move to location<br>2. Tap Retry | Check-in successful | P2 | Yes (Maestro) |

### 4.2 Security Tests

| ID | Scenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-001-S01 | GPS spoofing detection | 1. Mock GPS location<br>2. Attempt check-in | Check-in rejected or flagged as suspicious | P1 |
| TC-001-S02 | Authorization - worker cannot access supervisor dashboard | 1. Login as worker<br>2. Access /supervisor endpoint | Access denied (403) | P1 |
| TC-001-S03 | Authorization - worker cannot view other workers' history | 1. Login as worker<br>2. Access other worker's history | Access denied (403) | P1 |
| TC-001-S04 | SQL injection in location search | 1. Enter location: "Toko' OR '1'='1" | Input sanitized, no error | P1 |
| TC-001-S05 | Timestamp manipulation | 1. Modify device time<br>2. Attempt check-in | Server-side validation rejects invalid timestamp | P1 |

### 4.3 Usability Tests

| ID | Scenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-001-U01 | Check-in button visibility | 1. Open app with active visit | Check-in button prominent and accessible | P2 |
| TC-001-U02 | Error message clarity | 1. Trigger error (GPS off) | Message clear, actionable, Indonesian language | P2 |
| TC-001-U03 | Dashboard readability | 1. Open supervisor dashboard | Information organized, colors indicate status | P2 |
| TC-001-U04 | Map usability | 1. Trigger location error<br>2. View map | Map clear, markers distinguishable, distance readable | P2 |
| TC-001-U05 | History filtering ease | 1. Open history<br>2. Apply filters | Filters intuitive, results update immediately | P3 |

---

## 5. Test Data

<!--
Specific test accounts, seed data, and fixtures needed.
-->

### 5.1 Test Accounts

| Account | Role | Credentials | Purpose |
|---------|------|-------------|---------|
| test_worker_01 | Field Worker | worker01@test.com / Test123! | Field worker testing |
| test_worker_02 | Field Worker | worker02@test.com / Test123! | Multiple worker scenarios |
| test_supervisor | Supervisor | supervisor@test.com / Test123! | Dashboard and monitoring |
| test_manager | Manager | manager@test.com / Test123! | History and reporting |

### 5.2 Test Data Sets

| Dataset | Description | Location |
|---------|-------------|----------|
| locations.json | Test locations with GPS coordinates | tests/web/fixtures/locations.json |
| visits_scheduled.json | Scheduled visits for testing | tests/web/fixtures/visits_scheduled.json |
| visits_completed.json | Completed visits for history testing | tests/web/fixtures/visits_completed.json |

### 5.3 GPS Test Locations

| Location Name | Latitude | Longitude | Purpose |
|---------------|----------|------------|---------|
| Toko ABC (target) | -6.2088 | 106.8456 | Target location for check-in |
| Inside Geofence | -6.2088 | 106.8457 | Valid check-in (50m) |
| Outside Geofence | -6.2100 | 106.8470 | Invalid check-in (150m) |
| Boundary Exact | -6.2098 | 106.8469 | Edge case (100m exactly) |

---

## 6. Automation Plan

### 6.1 Coverage

| Category | Total | Automated | Manual | Reason for Manual |
|----------|-------|-----------|--------|-------------------|
| Functional | 20 | 18 | 2 | TC-001-004, TC-001-005 require physical device testing |
| Security | 5 | 3 | 2 | GPS spoofing requires device lab setup |
| Usability | 5 | 0 | 5 | Visual/subjective assessment required |

**Total Automation Coverage: 70% (21/30 test cases)**

### 6.2 Playwright Mapping (Web)

| Test Case | Playwright File | Page Object | Status |
|-----------|-----------------|-------------|--------|
| TC-001-001 to TC-001-003 | features/location-checkin.spec.ts | pages/DashboardPage.ts<br>pages/CheckInPage.ts | Pending |
| TC-001-006 to TC-001-009 | features/location-checkout.spec.ts | pages/DashboardPage.ts<br>pages/CheckOutPage.ts | Pending |
| TC-001-010 to TC-001-012 | features/supervisor-dashboard.spec.ts | pages/SupervisorDashboardPage.ts | Pending |
| TC-001-013, TC-001-015, TC-001-016 | features/visit-history.spec.ts | pages/HistoryPage.ts<br>pages/VisitDetailPage.ts | Pending |
| TC-001-014 | features/team-history.spec.ts | pages/TeamHistoryPage.ts | Pending |
| TC-001-017 to TC-001-019 | features/location-validation.spec.ts | pages/CheckInPage.ts<br>pages/ErrorPage.ts | Pending |

### 6.3 Maestro Mapping (Mobile)

| Test Case | Maestro File | Status |
|-----------|--------------|--------|
| TC-001-001, TC-001-002, TC-001-003 | flows/checkin-successful.yaml<br>flows/checkin-gps-disabled.yaml<br>flows/checkin-outside-geofence.yaml | Pending |
| TC-001-006, TC-001-007, TC-001-008, TC-001-009 | flows/checkout-successful.yaml<br>flows/checkout-without-checkin.yaml<br>flows/checkout-min-duration.yaml | Pending |
| TC-001-013, TC-001-015, TC-001-016 | flows/history-view.yaml<br>flows/history-filter.yaml<br>flows/history-detail.yaml | Pending |
| TC-001-017, TC-001-018, TC-001-019, TC-001-020 | flows/location-error-notification.yaml<br>flows/report-geofence-issue.yaml<br>flows/retry-after-move.yaml | Pending |

### 6.4 Selector Strategy

| Element | Selector | Type | Priority |
|---------|----------|------|----------|
| Check-In button | data-testid="checkin-button" | data-testid | Highest |
| Check-Out button | data-testid="checkout-button" | data-testid | Highest |
| Visit list item | data-testid="visit-item-{id}" | data-testid | Highest |
| Dashboard | data-testid="dashboard" | data-testid | Highest |
| Error notification | role="alert" | aria-label | High |
| Map container | data-testid="location-map" | data-testid | High |
| GPS coordinates | data-testid="gps-coordinates" | data-testid | Medium |
| History filter | aria-label="Filter history" | aria-label | Medium |
| Status badge | class="status-badge" | CSS class | Low (if no data-testid) |

**Selector Priority:** data-testid > aria-label/role > CSS class > text

---

## 7. UAT Scenarios

<!--
Client-facing test scripts. Ditulis dalam Bahasa Indonesia sesuai konfigurasi project.
Generated dari acceptance criteria untuk stakeholder non-technical.
-->

### UAT Scenario 01: Check-In Berhasil di Lokasi yang Benar

**Objective:** Memastikan field worker dapat melakukan check-in ketika berada di lokasi kunjungan yang tepat.

**Prerequisites:**
- Field worker sudah login ke aplikasi
- GPS sudah aktif dan izin lokasi diberikan
- Worker memiliki jadwal kunjungan untuk hari ini
- Worker berada di lokasi kunjungan (dalam radius 100 meter)

**Steps:**
1. Buka aplikasi di smartphone
2. Pilih kunjungan "Toko ABC" dari dashboard
3. Pastikan lokasi saat ini ditampilkan dengan benar
4. Tekan tombol "Check-In"
5. Tunggu proses validasi lokasi
6. Lihat pesan konfirmasi

**Expected Results:**
- Pesan "Check-In Berhasil" ditampilkan
- Timestamp check-in tercatat
- Koordinat GPS tercatat
- Status kunjungan berubah menjadi "Checked-In"
- Tombol Check-In tidak dapat ditekan lagi untuk kunjungan ini

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 02: Check-In Gagal Karena GPS Non-Aktif

**Objective:** Memastikan sistem menolak check-in ketika GPS tidak aktif.

**Prerequisites:**
- Field worker sudah login ke aplikasi
- GPS dalam keadaan non-aktif
- Worker memiliki jadwal kunjungan

**Steps:**
1. Buka aplikasi di smartphone
2. Pilih kunjungan "Toko ABC" dari dashboard
3. Tekan tombol "Check-In"

**Expected Results:**
- Pesan error ditampilkan: "GPS harus aktif untuk check-in"
- Check-in tidak berhasil
- Worker diminta untuk mengaktifkan GPS
- Tombol "Aktifkan GPS" tersedia

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 03: Check-In Gagal Karena Di Luar Lokasi

**Objective:** Memastikan sistem menolak check-in ketika worker berada di luar radius geofence.

**Prerequisites:**
- Field worker sudah login ke aplikasi
- GPS aktif
- Worker berada lebih dari 100 meter dari lokasi kunjungan

**Steps:**
1. Buka aplikasi di smartphone
2. Pilih kunjungan "Toko ABC" dari dashboard
3. Tekan tombol "Check-In"
4. Lihat pesan error yang ditampilkan
5. Lihat peta yang ditampilkan

**Expected Results:**
- Pesan error ditampilkan dengan informasi jarak: "Lokasi check-in Anda berada di luar area yang ditentukan. Jarak Anda: [X] meter dari lokasi kunjungan."
- Peta menampilkan lokasi saat ini (marker merah) dan lokasi tujuan (marker hijau)
- Check-in tidak berhasil
- Tombol "Coba Lagi" tersedia

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 04: Check-Out Berhasil

**Objective:** Memastikan field worker dapat melakukan check-out dan durasi kunjungan tercatat.

**Prerequisites:**
- Field worker sudah check-in di lokasi kunjungan
- Kunjungan telah selesai dilaksanakan

**Steps:**
1. Buka aplikasi di smartphone
2. Buka dashboard kunjungan yang sedang berlangsung
3. Tekan tombol "Check-Out"
4. Lihat pesan konfirmasi

**Expected Results:**
- Pesan "Check-Out Berhasil" ditampilkan
- Timestamp check-out tercatat
- Durasi kunjungan dihitung dan ditampilkan (misal: "1 jam 15 menit")
- Status kunjungan berubah menjadi "Completed"
- Worker dapat melakukan check-in untuk kunjungan berikutnya

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 05: Supervisor Memantau Kehadiran Tim

**Objective:** Memastikan supervisor dapat melihat status kehadiran tim secara real-time.

**Prerequisites:**
- Supervisor sudah login ke web dashboard
- Beberapa worker sedang check-in
- Beberapa worker belum check-in

**Steps:**
1. Buka web dashboard supervisor
2. Buka menu "Monitoring Kehadiran"
3. Lihat daftar worker dan status mereka
4. Tunggu beberapa saat untuk melihat update real-time

**Expected Results:**
- Daftar semua worker ditampilkan
- Worker yang check-in memiliki indikator hijau
- Worker yang belum check-in memiliki indikator abu-abu
- Setiap worker menampilkan: Nama, Lokasi, Waktu Check-In, Durasi
- Dashboard diperbarui secara real-time (dalam 30 detik)
- Worker yang check-in di luar lokasi memiliki indikator merah

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 06: Melihat Riwayat Kunjungan

**Objective:** Memastikan worker dan supervisor dapat melihat riwayat kunjungan.

**Prerequisites:**
- User (worker atau supervisor) sudah login
- Terdapat beberapa kunjungan yang sudah selesai

**Steps:**
1. Buka aplikasi/web dashboard
2. Buka menu "Riwayat Kunjungan"
3. Lihat daftar kunjungan
4. Klik salah satu kunjungan untuk melihat detail
5. Gunakan filter untuk mencari kunjungan tertentu

**Expected Results:**
- Daftar kunjungan ditampilkan dengan: Tanggal, Lokasi, Waktu Check-In, Waktu Check-Out, Durasi
- Filter berfungsi (berdasarkan tanggal, lokasi, status)
- Detail kunjungan menampilkan: Koordinat GPS, Peta, Status
- Worker hanya dapat melihat riwayat mereka sendiri
- Supervisor dapat melihat riwayat seluruh tim

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
FILE LOCATION: docs/features/001-visit-verification/01-location-checkin---testing.md

RELATED FILES:
- 01-location-checkin---business.md (PM: user stories, business rules)
- 01-location-checkin---technical.md (Dev: API, data model)
- 01-location-checkin---design.md (Design: UI/UX specifications)

GENERATED BY: QA Engineer (mengikuti user stories dari Business Spec)
-->
