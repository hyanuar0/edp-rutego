# TESTING SPECIFICATION
## [FEAT-003] Schedule & Route Validation

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-003 |
| Nama Fitur | Schedule & Route Validation |
| Epic | EPIC-003: Visit Verification |
| Business Spec | [03-schedule-validation---business.md](./03-schedule-validation---business.md) |
| Status | Draft |
| Owner | Arina Saffanah Zakiyyah |
| Dibuat | 2026-04-23 |
| Terakhir Diperbarui | 2026-04-23 |

---

## 1. Test Strategy

### 1.1 Scope of Testing

| Type | In Scope | Out of Scope |
|------|----------|--------------|
| Functional | Route optimization, schedule validation, route sequence validation, deviation notifications | GPS validation (FEAT-001), photo evidence (FEAT-002) |
| Security | Authorization (scheduler/supervisor/worker), data integrity | Penetration testing |
| Performance | Route optimization time, real-time validation response | Load testing (1000+ concurrent routes) |
| Usability | Route visualization clarity, dashboard usability, notification clarity | Accessibility (WCAG compliance) |

### 1.2 Risk-Based Priority

| Risk Area | Impact | Likelihood | Test Priority |
|-----------|--------|------------|---------------|
| Route optimization failure | High | Medium | P1 - Critical |
| Schedule validation error | High | High | P1 - Critical |
| Route deviation not detected | Medium | Medium | P2 - Important |
| Notification failure | Medium | Low | P2 - Important |
| Manual route adjustment | Low | Low | P3 - Nice to have |

### 1.3 Environment Requirements

| Environment | URL | Purpose |
|-------------|-----|---------|
| Staging | https://staging.example.com | QA testing dengan mock GPS dan test locations |
| UAT | https://uat.example.com | Client acceptance dengan real locations |
| Device Lab | Physical Android/iOS devices | Real GPS testing untuk Maestro |

---

## 2. Acceptance Criteria

<!--
Formalized dari user stories di Business Spec.
Setiap user story mendapat Gherkin scenarios yang mencakup happy path,
validation, error handling, dan edge cases.
-->

### US-01: Membuat Rute Optimal

```gherkin
Feature: FEAT-003-US-01 Optimal Route Creation
  As Scheduler
  I want membuat rute optimal secara otomatis untuk field worker
  So that worker dapat mengunjungi lokasi dengan urutan yang paling efisien

  Background:
    Given user is logged in as "Scheduler"
    And user has access to route management interface

  @happy-path @playwright
  Scenario: Create optimal route automatically
    Given scheduler selects worker "John Doe"
    And scheduler selects date "2026-04-24"
    And scheduler selects 5 locations to visit:
      | Location 1 | Toko ABC  | -6.2088, 106.8456 |
      | Location 2 | Toko DEF  | -6.2100, 106.8500 |
      | Location 3 | Toko GHI  | -6.2150, 106.8550 |
      | Location 4 | Toko JKL  | -6.2200, 106.8600 |
      | Location 5 | Toko MNO  | -6.2250, 106.8650 |
    When scheduler clicks "Generate Optimal Route"
    Then system should apply route optimization algorithm
    And system should display route on map with clear sequence
    And system should display estimations:
      | Total distance | ~15 km |
      | Total travel time | ~45 minutes |
      | Inter-location times | Calculated for each leg |
    And route sequence should be optimized for shortest distance
    And scheduler can save and assign route to worker

  @validation @playwright
  Scenario: Validate route optimization quality
    Given scheduler has generated a route
    When system displays route sequence
    Then route should be mathematically valid (no duplicate locations)
    And route should include all selected locations
    And total distance should be reasonable (not significantly longer than straight-line distances)
    And route should respect traffic conditions if data available

  @happy-path @playwright
  Scenario: Manual adjustment of route sequence
    Given scheduler has generated optimal route
    And route sequence is: 1 → 2 → 3 → 4 → 5
    When scheduler drags location #3 to position #2
    Then route sequence should update to: 1 → 3 → 2 → 4 → 5
    And system should recalculate estimations:
      | New total distance |
      | New total travel time |
    And map should display updated route visually

  @edge-case @playwright
  Scenario: Handle single location route
    Given scheduler selects only 1 location
    When scheduler clicks "Generate Optimal Route"
    Then system should create route with single location
    And estimations should show minimal distance/time

  @edge-case @playwright
  Scenario: Handle locations without GPS coordinates
    Given scheduler selects location without GPS coordinates
    When scheduler attempts to generate route
    Then system should display error "Location tidak memiliki koordinat GPS"
    And route generation should fail
    And system should prompt scheduler to add GPS coordinates

  @error-handling @playwright
  Scenario: Handle route generation failure
    Given route optimization service is unavailable
    When scheduler attempts to generate route
    Then system should display error "Gagal membuat rute optimal. Silakan coba lagi."
    And scheduler should be offered option to create route manually
```

---

### US-02: Melihat Jadwal & Rute Harian

```gherkin
Feature: FEAT-003-US-02 Daily Schedule & Route View
  As Field Worker
  I want melihat jadwal dan rute kunjungan untuk hari ini
  So that saya tahu lokasi mana yang harus dikunjungi terlebih dahulu dan bagaimana urutannya

  Background:
    Given user is logged in as "Field Worker"
    And worker has been assigned 5 visits for today
    And route has been optimized with sequence: 1 → 2 → 3 → 4 → 5

  @happy-path @maestro @playwright
  Scenario: View daily route with all details
    Given worker opens schedule dashboard
    Then worker should see list of 5 locations with:
      | Location names |
      | Visit sequence (1, 2, 3, 4, 5) |
      | Scheduled times |
      | Estimated travel times |
      | Total distance and time for the day |
    And worker should see map with:
      | Route lines connecting locations |
      | Location markers with sequence numbers |
      | Different colors for visited vs upcoming locations |
    And worker should see "Next: Toko ABC" (location #2)

  @happy-path @maestro @playwright
  Scenario: View location details and navigate
    Given worker is viewing schedule dashboard
    When worker taps on "Toko ABC" (location #2)
    Then worker should see location details:
      | Location name | Toko ABC |
      | Full address | Jl. Contoh No. 123 |
      | Scheduled time | 09:30 |
      | Estimated arrival | 09:25 |
    And worker should see "Navigate" button
    When worker taps "Navigate"
    Then external maps app should open with directions to location

  @validation @maestro @playwright
  Scenario: Visual route progress indication
    Given worker has completed visits 1, 2, and 3
    When worker views route dashboard
    Then locations 1, 2, 3 should show as "Completed" (green)
    And locations 4, 5 should show as "Upcoming" (gray)
    And progress bar should display "3 dari 5 lokasi selesai"
    And next location should be highlighted

  @edge-case @maestro @playwright
  Scenario: Handle late arrival notification
    Given worker is scheduled at location #2 at 09:30
    And current time is 09:45
    And worker has not checked in
    When worker views dashboard
    Then worker should see notification "Anda terlambat untuk Toko ABC"
    And location should be highlighted in red
    And supervisor should be notified of delay

  @edge-case @maestro @playwright
  Scenario: No scheduled visits for today
    Given worker has no visits scheduled for today
    When worker opens schedule dashboard
    Then worker should see message "Tidak ada kunjungan terjadwal untuk hari ini"
    And worker should see option to view unscheduled visits or contact supervisor
```

---

### US-03: Penandaan Otomatis Jadwal Mismatch

```gherkin
Feature: FEAT-003-US-03 Automatic Schedule Mismatch Flagging
  As Sistem
  I want menandai secara otomatis kunjungan yang dilakukan di luar jadwal
  So that supervisor dapat mengetahui penyimpangan dan mengambil tindakan jika perlu

  Background:
    Given user is logged in as "Field Worker"
    And worker has scheduled visit at "Toko ABC" on 2026-04-24 at 09:30

  @happy-path @maestro @playwright
  Scenario: Check-in within scheduled time (valid)
    Given current date is 2026-04-24
    And current time is 09:25
    And worker is within geofence of "Toko ABC"
    When worker performs check-in
    Then check-in should be successful
    And visit should be marked as "Schedule Valid"
    And no mismatch flag should be created

  @validation @maestro @playwright
  Scenario: Check-in on wrong day (Wrong Day)
    Given visit is scheduled for 2026-04-24
    And worker performs check-in on 2026-04-25
    When check-in is validated
    Then visit should be flagged as "Wrong Day"
    And system should record: "Kunjungan dijadwalkan untuk hari 2026-04-24, tetapi dilakukan pada hari 2026-04-25"
    And supervisor should be able to see the flag

  @validation @maestro @playwright
  Scenario: Check-in outside time tolerance (Wrong Time)
    Given visit is scheduled for 09:30
    And tolerance is ±30 minutes
    And worker performs check-in at 10:15 (45 minutes late)
    When check-in is validated
    Then visit should be flagged as "Wrong Time"
    And system should record: "Dijadwalkan: 09:30, Dilakukan: 10:15, Selisih: 45 menit"
    And supervisor should be notified

  @validation @maestro @playwright
  Scenario: Check-in at unscheduled location (Unscheduled Visit)
    Given worker has no scheduled visit at "Toko XYZ"
    When worker performs check-in at "Toko XYZ"
    Then visit should be flagged as "Unscheduled Visit"
    And system should record: "Lokasi tidak ada dalam jadwal untuk tanggal ini"
    And check-in should be allowed but flagged

  @edge-case @maestro @playwright
  Scenario: Check-in at edge of time tolerance
    Given visit is scheduled for 09:30
    And tolerance is ±30 minutes
    When worker performs check-in at 10:00 (30 minutes late, at tolerance limit)
    Then check-in should be considered valid
    And visit should NOT be flagged as "Wrong Time"

  @error-handling @playwright
  Scenario: Supervisor modifies mismatch flag
    Given visit is flagged as "Wrong Day"
    And supervisor determines it was a valid exception
    When supervisor changes flag status
    Then visit should be updated to "Schedule Valid"
    And supervisor should provide reason for change
    And change should be logged in audit trail
```

---

### US-04: Validasi Urutan Rute

```gherkin
Feature: FEAT-003-US-04 Route Sequence Validation
  As Sistem
  I want memvalidasi bahwa worker mengikuti urutan rute yang ditugaskan
  So that efisiensi operasional terjaga dan supervisor dapat memantau kepatuhan

  Background:
    Given worker is assigned route sequence: 1 → 2 → 3 → 4 → 5
    And worker has completed check-in at locations 1, 2, and 3

  @happy-path @maestro @playwright
  Scenario: Worker follows assigned route sequence
    Given worker checks in at location #4 next
    When system validates route sequence
    Then sequence should match assigned route: 1 → 2 → 3 → 4
    And no route deviation flag should be created
    And worker should see "Correct sequence" indication

  @validation @maestro @playwright
  Scenario: Worker deviates from route sequence
    Given worker skips location #4
    And worker checks in at location #5 next
    When system validates route sequence
    Then visit should be flagged as "Route Deviation"
    And system should record:
      | Assigned sequence | 1 → 2 → 3 → 4 → 5 |
      | Actual sequence | 1 → 2 → 3 → 5 |
      | Skipped location | Location #4 |
      | Out-of-sequence visit | Location #5 visited before #4 |
    And system should calculate deviation impact:
      | Additional distance | X km |
      | Additional travel time | Y minutes |
      | Efficiency decrease | Z% |

  @validation @maestro @playwright
  Scenario: Route deviation exceeds tolerance
    Given route deviation adds 8 km to travel
    And tolerance threshold is 5 km
    When system calculates deviation impact
    Then supervisor should receive notification
    And notification should include:
      | Worker name |
      | Deviation type | Route Deviation |
      | Additional distance | 8 km |
      | Affected locations | #4, #5 |

  @happy-path @maestro @playwright
  Scenario: Worker views route progress
    Given worker has completed locations 1, 2, and 3
    When worker views route dashboard
    Then worker should see completed sequence: 1 ✅ 2 ✅ 3 ✅
    And worker should see upcoming sequence: 4 5
    And progress bar should display "3 dari 5 lokasi selesai"
    And next location should be clearly indicated

  @edge-case @maestro @playwright
  Scenario: Route deviation within tolerance
    Given route deviation adds 3 km to travel
    And tolerance threshold is 5 km
    When system calculates deviation impact
    Then visit should be flagged as "Route Deviation"
    But supervisor should NOT receive notification (within tolerance)
    And flag should be visible in supervisor dashboard for review

  @edge-case @maestro @playwright
  Scenario: Worker returns to skipped location later
    Given worker visited locations in sequence: 1 → 2 → 3 → 5
    And worker later returns to complete location #4
    When worker checks in at location #4
    Then system should update actual sequence: 1 → 2 → 3 → 5 → 4
    And deviation flag should remain but marked as "Corrected"
    And system should note that worker completed all locations
```

---

### US-05: Notifikasi Penyimpangan ke Supervisor

```gherkin
Feature: FEAT-003-US-05 Deviation Notification to Supervisor
  As Supervisor
  I want menerima notifikasi jika worker melakukan penyimpangan dari jadwal atau rute
  So that saya dapat segera menindaklanjuti dan memberikan bimbingan jika diperlukan

  Background:
    Given user is logged in as "Supervisor"
    And supervisor has 5 workers in their team
    And system monitors deviations in real-time

  @happy-path @playwright
  Scenario: Receive schedule deviation notification
    Given worker "John Doe" checks in at wrong time
    And deviation exceeds tolerance threshold
    When supervisor is on dashboard
    Then supervisor should receive notification with:
      | Worker name | John Doe |
      | Deviation type | Wrong Time |
      | Scheduled time | 09:30 |
      | Actual time | 10:15 |
      | Difference | 45 minutes |
    And notification should be clickable for details
    And notification should be timestamped

  @happy-path @playwright
  Scenario: Receive route deviation notification
    Given worker "Jane Smith" deviates from route sequence
    And deviation adds 8 km to travel
    When supervisor is on dashboard
    Then supervisor should receive notification with:
      | Worker name | Jane Smith |
      | Deviation type | Route Deviation |
      | Assigned sequence | 1 → 2 → 3 → 4 |
      | Actual sequence | 1 → 3 → 2 → 4 |
      | Additional distance | 8 km |
      | Additional time | 20 minutes |

  @happy-path @playwright
  Scenario: View deviation notifications in dashboard
    Given supervisor has received 5 deviation notifications
    When supervisor opens "Deviation Dashboard"
    Then supervisor should see list of all deviations with:
      | Worker names |
      | Deviation types |
      | Timestamps |
      | Severity indicators |
    And supervisor can filter by:
      | Deviation type |
      | Worker |
      | Date range |
      | Severity |

  @happy-path @playwright
  Scenario: Mark deviation with follow-up action
    Given supervisor clicks on deviation notification
    And deviation details are displayed
    When supervisor marks deviation as "Needs Attention"
    And supervisor adds note: "Discuss with worker during next meeting"
    Then deviation should be updated with follow-up action
    And note should be saved with deviation record
    And deviation should be removed from "Pending" list

  @validation @playwright
  Scenario: Notification delivery channels
    Given deviation occurs that exceeds threshold
    When notification is generated
    Then notification should be sent via:
      | In-app notification |
      | Dashboard notification list |
    And if supervisor has email enabled, notification should also be sent via email

  @edge-case @playwright
  Scenario: Low-severity deviations don't trigger notifications
    Given route deviation adds only 3 km
    And tolerance threshold is 5 km
    When deviation is calculated
    Then deviation should be flagged
    But supervisor should NOT receive immediate notification
    And deviation should appear in dashboard for review

  @error-handling @playwright
  Scenario: Handle notification delivery failure
    Given notification system is temporarily unavailable
    When deviation occurs requiring notification
    Then system should log notification for retry
    And notification should be sent when system recovers
    And notification should include original deviation timestamp
```

---

## 3. Validation Rules

<!--
Field-level validation. Derived from business rules and data requirements.
Dev uses this for implementation, QA uses this for test cases.
-->

| Field | Rule | Error Message |
|-------|------|---------------|
| Route Sequence | Must be unique, no duplicates | "Urutan rute tidak valid: lokasi duplikat" |
| GPS Coordinates | Required for all locations | "Lokasi harus memiliki koordinat GPS" |
| Scheduled Time | Required for each visit | "Waktu jadwal harus diisi" |
| Check-in Time | Must be within ±30 min of scheduled time | "Check-in di luar toleransi waktu" |
| Scheduled Day | Must match check-in day | "Kunjungan dijadwalkan untuk hari lain" |
| Route Deviation Distance | Flag if >5 km | "Penyimpangan rute melebihi batas toleransi" |
| Route Deviation Time | Flag if >15 minutes | "Penyimpangan waktu melebihi batas toleransi" |
| Unscheduled Visit | Flag if location not in schedule | "Kunjungan tidak dijadwalkan" |
| Supervisor Override | Requires reason | "Alasan harus diisi untuk mengubah flag" |

---

## 4. Test Cases

<!--
Detailed test cases expanded from acceptance criteria.
ID format: TC-003-SEQ
-->

### 4.1 Functional Tests

| ID | Scenario | Preconditions | Steps | Expected Result | Priority | Automated |
|----|----------|---------------|-------|-----------------|----------|-----------|
| TC-003-001 | Create optimal route | 5 locations with GPS | 1. Generate route | Optimal sequence created, estimations displayed | P1 | Yes (Playwright) |
| TC-003-002 | Validate route quality | Route generated | 1. Check sequence validity | All locations included, distance reasonable | P1 | Yes (Playwright) |
| TC-003-003 | Manual route adjustment | Route generated | 1. Drag location to new position | Sequence updates, estimations recalculate | P2 | Yes (Playwright) |
| TC-003-004 | Single location route | 1 location selected | 1. Generate route | Single location route created | P3 | Yes (Playwright) |
| TC-003-005 | Missing GPS coordinates | Location without GPS | 1. Attempt route generation | Error shown, generation fails | P1 | Yes (Playwright) |
| TC-003-006 | View daily route | 5 visits assigned | 1. Open dashboard | All details displayed, map shown | P1 | Yes (Maestro + Playwright) |
| TC-003-007 | View location details | On dashboard | 1. Tap location | Details shown, navigate option available | P1 | Yes (Maestro + Playwright) |
| TC-003-008 | Route progress indication | 3/5 completed | 1. View dashboard | Completed/upcoming shown, progress bar correct | P2 | Yes (Maestro + Playwright) |
| TC-003-009 | Late arrival notification | Late for visit | 1. View dashboard | Notification shown, location highlighted | P2 | Yes (Maestro + Playwright) |
| TC-003-010 | No scheduled visits | Empty schedule | 1. Open dashboard | Message shown, options provided | P3 | Yes (Maestro + Playwright) |
| TC-003-011 | Valid check-in (on time) | Within tolerance | 1. Check in | Successful, no flag | P1 | Yes (Maestro + Playwright) |
| TC-003-012 | Wrong day check-in | Wrong day | 1. Check in | Flagged "Wrong Day", supervisor notified | P1 | Yes (Maestro + Playwright) |
| TC-003-013 | Wrong time check-in | Outside tolerance | 1. Check in | Flagged "Wrong Time", details recorded | P1 | Yes (Maestro + Playwright) |
| TC-003-014 | Unscheduled visit | Wrong location | 1. Check in | Flagged "Unscheduled", allowed | P2 | Yes (Maestro + Playwright) |
| TC-003-015 | Edge of time tolerance | At ±30 min limit | 1. Check in | Valid, no flag | P2 | Yes (Maestro + Playwright) |
| TC-003-016 | Supervisor modifies flag | Flagged visit | 1. Change status | Updated to valid, reason logged | P2 | Yes (Playwright) |
| TC-003-017 | Correct route sequence | Follows sequence | 1. Check in order | No deviation flag | P1 | Yes (Maestro + Playwright) |
| TC-003-018 | Route deviation | Skips location | 1. Check out of sequence | Flagged, impact calculated | P1 | Yes (Maestro + Playwright) |
| TC-003-019 | Deviation exceeds tolerance | >5 km deviation | 1. Complete deviation | Supervisor notified | P1 | Yes (Maestro + Playwright) |
| TC-003-020 | View route progress | 3/5 completed | 1. View dashboard | Progress shown correctly | P2 | Yes (Maestro + Playwright) |
| TC-003-021 | Deviation within tolerance | <5 km deviation | 1. Complete deviation | Flagged, no notification | P2 | Yes (Maestro + Playwright) |
| TC-003-022 | Return to skipped location | Deviation then return | 1. Complete skipped location | Marked "Corrected", all done | P2 | Yes (Maestro + Playwright) |
| TC-003-023 | Schedule deviation notification | Wrong time check-in | 1. Supervisor views dashboard | Notification received with details | P1 | Yes (Playwright) |
| TC-003-024 | Route deviation notification | Sequence deviation | 1. Supervisor views dashboard | Notification received with details | P1 | Yes (Playwright) |
| TC-003-025 | View deviation dashboard | 5 deviations | 1. Open dashboard | All shown, filterable | P1 | Yes (Playwright) |
| TC-003-026 | Mark deviation with action | Deviation shown | 1. Mark + add note | Updated, note saved | P1 | Yes (Playwright) |
| TC-003-027 | Notification channels | Deviation occurs | 1. Check notifications | In-app + dashboard notifications | P2 | Yes (Playwright) |
| TC-003-028 | Low severity no notification | <5 km deviation | 1. Complete deviation | Flagged, no immediate notification | P2 | Yes (Maestro + Playwright) |
| TC-003-029 | Notification failure handling | System down | 1. Deviation occurs | Logged for retry, sent when recovered | P2 | Yes (Playwright) |

### 4.2 Security Tests

| ID | Scenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-003-S01 | Unauthorized route creation | 1. Worker attempts to create route | Access denied (403) | P1 |
| TC-003-S02 | Modify another's schedule | 1. Worker attempts to change schedule | Access denied (403) | P1 |
| TC-003-S03 | Supervisor override audit | 1. Review override logs | All changes logged with supervisor ID | P1 |
| TC-003-S04 | Route data integrity | 1. Check route data consistency | No data corruption | P1 |

### 4.3 Usability Tests

| ID | Scenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-003-U01 | Route map clarity | 1. View route map | Route lines clear, markers visible | P2 |
| TC-003-U02 | Notification clarity | 1. Receive deviation notification | Message clear, actionable | P2 |
| TC-003-U03 | Dashboard usability | 1. Use deviation dashboard | Easy to filter, view details | P2 |
| TC-003-U04 | Progress indication | 1. View route progress | Progress bar clear, accurate | P2 |
| TC-003-U05 | Manual adjustment ease | 1. Adjust route sequence | Drag-and-drop intuitive | P3 |

---

## 5. Test Data

<!--
Specific test accounts, seed data, and fixtures needed.
-->

### 5.1 Test Accounts

| Account | Role | Credentials | Purpose |
|---------|------|-------------|---------|
| test_scheduler | Scheduler | scheduler@test.com / Test123! | Route creation and management |
| test_worker_01 | Field Worker | worker01@test.com / Test123! | Route adherence testing |
| test_worker_02 | Field Worker | worker02@test.com / Test123! | Deviation scenarios |
| test_supervisor | Supervisor | supervisor@test.com / Test123! | Dashboard and notifications |

### 5.2 Test Data Sets

| Dataset | Description | Location |
|---------|-------------|----------|
| locations_route.json | 5 locations with GPS coordinates | tests/web/fixtures/locations/ |
| routes_optimized.json | Pre-calculated optimal routes | tests/web/fixtures/routes/ |
| schedules_daily.json | Daily visit schedules | tests/web/fixtures/schedules/ |

### 5.3 Test Locations

| Location Name | Latitude | Longitude | Purpose |
|---------------|----------|------------|---------|
| Toko ABC | -6.2088 | 106.8456 | Location #1 in route |
| Toko DEF | -6.2100 | 106.8500 | Location #2 in route |
| Toko GHI | -6.2150 | 106.8550 | Location #3 in route |
| Toko JKL | -6.2200 | 106.8600 | Location #4 in route |
| Toko MNO | -6.2250 | 106.8650 | Location #5 in route |
| Toko XYZ | -6.2300 | 106.8700 | Unscheduled location |

---

## 6. Automation Plan

### 6.1 Coverage

| Category | Total | Automated | Manual | Reason for Manual |
|----------|-------|-----------|--------|-------------------|
| Functional | 29 | 26 | 3 | TC-003-004, TC-003-010, TC-003-022 require specific test setups |
| Security | 4 | 4 | 0 | All security tests automatable |
| Usability | 5 | 0 | 5 | Visual/subjective assessment required |

**Total Automation Coverage: 90% (30/33 test cases)**

### 6.2 Playwright Mapping (Web)

| Test Case | Playwright File | Page Object | Status |
|-----------|-----------------|-------------|--------|
| TC-003-001 to TC-003-005 | features/route-creation.spec.ts | pages/RouteCreationPage.ts<br>pages/MapPage.ts | Pending |
| TC-003-006 to TC-003-010 | features/daily-route-view.spec.ts | pages/ScheduleDashboardPage.ts<br>pages/LocationDetailPage.ts | Pending |
| TC-003-011 to TC-003-016 | features/schedule-validation.spec.ts | pages/ScheduleDashboardPage.ts<br>pages/FlagReviewPage.ts | Pending |
| TC-003-023 to TC-003-029 | features/deviation-notification.spec.ts | pages/DeviationDashboardPage.ts<br>pages/NotificationPage.ts | Pending |

### 6.3 Maestro Mapping (Mobile)

| Test Case | Maestro File | Status |
|-----------|--------------|--------|
| TC-003-006 to TC-003-010 | flows/daily-route-view.yaml<br>flows/location-details.yaml<br>flows/route-progress.yaml<br>flows/late-notification.yaml<br>flows/no-schedule.yaml | Pending |
| TC-003-011 to TC-003-016 | flows/checkin-valid.yaml<br>flows/checkin-wrong-day.yaml<br>flows/checkin-wrong-time.yaml<br>flows/checkin-unscheduled.yaml<br>flows/time-tolerance-edge.yaml<br>flows/supervisor-override.yaml | Pending |
| TC-003-017 to TC-003-022 | flows/route-correct.yaml<br>flows/route-deviation.yaml<br>flows/deviation-exceeds.yaml<br>flows/route-progress-view.yaml<br>flows/deviation-within.yaml<br>flows/return-skipped.yaml | Pending |
| TC-003-028 | flows/deviation-low-severity.yaml | Pending |

### 6.4 Selector Strategy

| Element | Selector | Type | Priority |
|---------|----------|------|----------|
| Generate route button | data-testid="generate-route-btn" | data-testid | Highest |
| Route map container | data-testid="route-map" | data-testid | Highest |
| Location marker | data-testid="location-marker-{id}" | data-testid | Highest |
| Schedule dashboard | data-testid="schedule-dashboard" | data-testid | Highest |
| Location list item | data-testid="location-item-{id}" | data-testid | Highest |
| Deviation notification | role="alert" | aria-label | High |
| Filter dropdown | aria-label="Filter deviations" | aria-label | High |
| Progress bar | data-testid="route-progress" | data-testid | High |

**Selector Priority:** data-testid > aria-label/role > CSS class > text

---

## 7. UAT Scenarios

<!--
Client-facing test scripts. Ditulis dalam Bahasa Indonesia sesuai konfigurasi project.
-->

### UAT Scenario 01: Pembuatan Rute Optimal

**Objective:** Memastikan scheduler dapat membuat rute optimal secara otomatis.

**Prerequisites:**
- Scheduler login ke web dashboard
- 5 lokasi tersedia dengan koordinat GPS
- Worker "John Doe" tersedia untuk penugasan

**Steps:**
1. Buka menu "Manajemen Rute"
2. Pilih worker "John Doe"
3. Pilih tanggal "2026-04-24"
4. Pilih 5 lokasi: Toko ABC, Toko DEF, Toko GHI, Toko JKL, Toko MNO
5. Klik "Generate Optimal Route"
6. Lihat rute yang dihasilkan
7. Cek estimasi jarak dan waktu
8. Klik "Simpan dan Tugaskan"

**Expected Results:**
- Rute dibuat dengan urutan optimal (jarak terpendek)
- Peta menampilkan garis rute yang menghubungkan lokasi
- Setiap lokasi memiliki nomor urutan (1, 2, 3, 4, 5)
- Estimasi ditampilkan:
  - Total jarak (km)
  - Total waktu perjalanan
  - Waktu antar lokasi
- Rute berhasil disimpan dan ditugaskan ke worker

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 02: Melihat Jadwal & Rute Harian

**Objective:** Memastikan worker dapat melihat jadwal dan rute lengkap untuk hari ini.

**Prerequisites:**
- Worker login ke aplikasi
- 5 kunjungan dijadwalkan untuk hari ini
- Rute optimal sudah ditentukan

**Steps:**
1. Buka aplikasi di smartphone
2. Buka dashboard "Jadwal Hari Ini"
3. Lihat daftar lokasi dengan urutan
4. Lihat peta dengan rute visual
5. Ketuk salah satu lokasi untuk melihat detail
6. Ketuk "Navigate" untuk buka Google Maps

**Expected Results:**
- Daftar 5 lokasi dengan urutan jelas (1-5)
- Setiap lokasi menampilkan:
  - Nama lokasi
  - Waktu jadwal
  - Estimasi kedatangan
- Peta menampilkan:
  - Garis rute yang menghubungkan lokasi
  - Marker dengan nomor urutan
  - Warna berbeda untuk lokasi yang sudah/belum dikunjungi
- Ketuk lokasi menampilkan detail lengkap
- Tombol "Navigate" membuka Google Maps dengan arah

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 03: Penandaan Jadwal Tidak Sesuai

**Objective:** Memastikan sistem menandai kunjungan di luar jadwal.

**Prerequisites:**
- Worker login ke aplikasi
- Kunjungan dijadwalkan untuk 2026-04-24 jam 09:30

**Steps:**
1. Worker melakukan check-in pada hari yang salah (misal: sehari sebelumnya)
2. Atau worker melakukan check-in pada waktu yang salah (misal: 1 jam setelah jadwal)
3. Atau worker melakukan check-in di lokasi yang tidak dijadwalkan

**Expected Results:**
- Check-in di hari salah: Ditandai "Wrong Day", catatan "Kunjungan dijadwalkan untuk hari X, dilakukan hari Y"
- Check-in di waktu salah: Ditandai "Wrong Time", catatan dengan selisih waktu
- Check-in di lokasi salah: Ditandai "Unscheduled Visit", catatan "Lokasi tidak ada dalam jadwal"
- Supervisor dapat melihat semua flag di dashboard

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 04: Validasi Urutan Rute

**Objective:** Memastikan sistem mendeteksi penyimpangan urutan rute.

**Prerequisites:**
- Worker login ke aplikasi
- Rute yang ditugaskan: 1 → 2 → 3 → 4 → 5

**Steps:**
1. Worker menyelesaikan lokasi 1, 2, 3
2. Worker melewatkan lokasi #4 dan langsung ke #5
3. Lihat notifikasi atau flag yang dibuat
4. Cek dampak penyimpangan (jarak/waktu tambahan)

**Expected Results:**
- Kunjungan ditandai "Route Deviation"
- Sistem mencatat:
  - Urutan yang ditugaskan: 1 → 2 → 3 → 4 → 5
  - Urutan aktual: 1 → 2 → 3 → 5
  - Lokasi yang dilompati: #4
- Dampak penyimpangan dihitung:
  - Tambahan jarak (km)
  - Tambahan waktu (menit)
- Supervisor menerima notifikasi jika melebihi toleransi

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 05: Notifikasi Penyimpangan ke Supervisor

**Objective:** Memastikan supervisor menerima notifikasi penyimpangan.

**Prerequisites:**
- Supervisor login ke web dashboard
- Worker melakukan penyimpangan yang melebihi toleransi

**Steps:**
1. Worker melakukan check-in di luar jadwal (misal: 45 menit terlambat)
2. Supervisor membuka dashboard
3. Lihat notifikasi yang masuk
4. Klik notifikasi untuk melihat detail
5. Berikan tindak lanjut (misal: "Needs Attention")

**Expected Results:**
- Supervisor menerima notifikasi dengan detail:
  - Nama worker
  - Jenis penyimpangan
  - Jadwal vs aktual
  - Timestamp
- Notifikasi dapat diklik untuk detail lengkap
- Supervisor dapat memberikan tindak lanjut dengan catatan
- Deviation ditandai sesuai tindak lanjut

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 06: Revisi Keputusan Supervisor

**Objective:** Memastikan supervisor dapat mengubah keputusan sebelumnya.

**Prerequisites:**
- Supervisor login ke dashboard
- Kunjungan sebelumnya ditandai "Wrong Time"

**Steps:**
1. Supervisor membuka detail kunjungan flagged
2. Klik "Revisi Keputusan"
3. Pilih keputusan baru: "Valid"
4. Isi alasan revisi: "Worker memberikan penjelasan valid - kemacetan lalu lintas"
5. Simpan revisi

**Expected Results:**
- Status kunjungan berubah dari "Wrong Time" ke "Valid"
- Riwayat revisi dicatat:
  - Keputusan lama → baru
  - Supervisor yang merevisi
  - Timestamp revisi
  - Alasan revisi
- Worker menerima notifikasi perubahan keputusan
- Flag dihapus dari dashboard

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
FILE LOCATION: docs/features/001-visit-verification/03-schedule-validation---testing.md

RELATED FILES:
- 03-schedule-validation---business.md (PM: user stories, business rules)
- 03-schedule-validation---technical.md (Dev: API, data model)
- 03-schedule-validation---design.md (Design: UI/UX specifications)

GENERATED BY: QA Engineer (mengikuti user stories dari Business Spec)
-->
