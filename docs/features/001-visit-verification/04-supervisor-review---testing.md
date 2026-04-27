# TESTING SPECIFICATION
## [FEAT-004] Supervisor Review & Approval

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-004 |
| Nama Fitur | Supervisor Review & Approval |
| Epic | EPIC-003: Visit Verification |
| Business Spec | [04-supervisor-review---business.md](./04-supervisor-review---business.md) |
| Status | Draft |
| Owner | Arina Saffanah Zakiyyah |
| Dibuat | 2026-04-23 |
| Terakhir Diperbarui | 2026-04-23 |

---

## 1. Test Strategy

### 1.1 Scope of Testing

| Type | In Scope | Out of Scope |
|------|----------|--------------|
| Functional | Review dashboard, approval workflow, notifications, decision revision | GPS validation (FEAT-001), photo evidence (FEAT-002), schedule validation (FEAT-003) |
| Security | Authorization (supervisor only), worker notification access, audit trail | Penetration testing |
| Performance | Dashboard load time, notification delivery time | Load testing (1000+ concurrent reviews) |
| Usability | Dashboard usability, notification clarity, decision workflow clarity | Accessibility (WCAG compliance) |

### 1.2 Risk-Based Priority

| Risk Area | Impact | Likelihood | Test Priority |
|-----------|--------|------------|---------------|
| Incorrect decision | High | Medium | P1 - Critical |
| Notification failure | High | Low | P1 - Critical |
| Unauthorized access | High | Low | P1 - Critical |
| Revision without audit | Medium | Low | P2 - Important |
| Dashboard performance | Low | Medium | P3 - Nice to have |

### 1.3 Environment Requirements

| Environment | URL | Purpose |
|-------------|-----|---------|
| Staging | https://staging.example.com | QA testing dengan mock flagged visits |
| UAT | https://uat.example.com | Client acceptance dengan real scenarios |
| Device Lab | Physical Android/iOS devices | Push notification testing untuk Maestro |

---

## 2. Acceptance Criteria

<!--
Formalized dari user stories di Business Spec.
Setiap user story mendapat Gherkin scenarios yang mencakup happy path,
validation, error handling, dan edge cases.
-->

### US-01: Dashboard Kunjungan Flagged

```gherkin
Feature: FEAT-004-US-01 Flagged Visits Dashboard
  As Supervisor
  I want melihat dashboard dengan semua kunjungan yang ditandai sistem
  So that saya dapat meninjau dan memberikan keputusan untuk setiap kunjungan yang mencurigakan

  Background:
    Given user is logged in as "Supervisor"
    And supervisor has 5 workers in their team
    And system has flagged 15 visits across various categories

  @happy-path @playwright
  Scenario: View flagged visits dashboard
    Given supervisor opens "Review Kunjungan" menu
    Then supervisor should see dashboard with:
      | List of 15 flagged visits |
      | Each visit shows:
        | Worker name |
        | Check-in timestamp |
        | Location name |
        | Flag category (GPS/Photo/Schedule/Route) |
        | Review status (Pending/Reviewed) |
        | Priority badge (High/Medium/Low) |
    And system should display counters:
      | Total flagged visits | 15 |
      | Pending review | 8 |
      | Reviewed today | 5 |
      | High priority pending | 3 |
    And dashboard should update in real-time

  @validation @playwright
  Scenario: Filter flagged visits by category
    Given supervisor is viewing flagged visits dashboard
    When supervisor applies filter "Flag Category: GPS Invalid"
    Then only GPS Invalid visits should be displayed
    And other flag categories should be hidden
    And filter indicator should be visible

  @validation @playwright
  Scenario: Filter flagged visits by status
    Given supervisor is viewing flagged visits dashboard
    When supervisor applies filter "Review Status: Pending"
    Then only pending visits should be displayed
    And reviewed visits should be hidden

  @validation @playwright
  Scenario: Sort flagged visits
    Given supervisor is viewing flagged visits dashboard
    When supervisor selects sort "Priority: High → Low"
    Then visits should be ordered by priority badge
    And High priority visits should appear first
    When supervisor selects sort "Date: Newest → Oldest"
    Then visits should be ordered by check-in timestamp (newest first)

  @happy-path @playwright
  Scenario: Export flagged visits report
    Given supervisor is viewing flagged visits dashboard
    And 10 visits match current filters
    When supervisor clicks "Export to CSV"
    Then CSV file should be downloaded
    And file should contain all 10 visits with complete data
    When supervisor clicks "Export to PDF"
    Then PDF report should be generated
    And report should be suitable for sharing with management

  @edge-case @playwright
  Scenario: Dashboard with no flagged visits
    Given supervisor has no flagged visits in team
    When supervisor opens "Review Kunjungan" dashboard
    Then supervisor should see message "Tidak ada kunjungan yang ditandai"
    And empty state illustration should be displayed
    And counters should show:
      | Total | 0 |
      | Pending | 0 |
      | Reviewed today | 0 |

  @error-handling @playwright
  Scenario: Unauthorized access to dashboard
    Given user is logged in as "Field Worker"
    When user attempts to access "Review Kunjungan" dashboard
    Then access should be denied (403 Forbidden)
    And user should see error "Anda tidak memiliki akses ke fitur ini"
```

---

### US-02: Meninjau Detail Kunjungan

```gherkin
Feature: FEAT-004-US-02 Visit Detail Review
  As Supervisor
  I want melihat detail lengkap kunjungan yang ditandai
  So that saya dapat membuat keputusan yang informasi dan akurat

  Background:
    Given user is logged in as "Supervisor"
    And supervisor is viewing flagged visits dashboard
    And a visit is flagged as "Photo Suspicious"

  @happy-path @playwright
  Scenario: View comprehensive visit details
    Given supervisor clicks on flagged visit
    Then system should display visit detail page with sections:

    **Worker Information:**
      | Worker name | John Doe |
      | Worker ID | W-001 |
      | Team | Sales Team A |
      | Profile photo | [Displayed] |

    **Visit Information:**
      | Location | Toko ABC |
      | Address | Jl. Contoh No. 123 |
      | GPS coordinates | -6.2088, 106.8456 |
      | Check-in time | 2026-04-23 09:15 |
      | Check-out time | 2026-04-23 10:30 |
      | Duration | 1 hour 15 minutes |
      | Schedule status | Sesuai jadwal |

    **Flag Details (Photo Suspicious):**
      | Photo with watermark | [Displayed] |
      | EXIF DateTimeOriginal | 2026-04-20 14:30 |
      | EXIF GPS | -6.2088, 106.8456 |
      | Flag reason | Timestamp 3 days old |

    **Action Buttons:**
      | ✅ Setujui (Valid) | [Enabled] |
      | ⚠️ Tandai Mencurigakan | [Enabled] |
      | ❌ Tolak (Invalid) | [Enabled] |

  @validation @playwright
  Scenario: View GPS Invalid flag details
    Given supervisor clicks on visit flagged as "GPS Invalid"
    Then system should display:
      | Check-in location (map, coordinates) | -6.2100, 106.8500 |
      | Target location (map, coordinates) | -6.2088, 106.8456 |
      | Distance from target | 150 meters |
      | Geofence radius | 100 meters |
    And map should show both locations with markers
    And line should indicate distance between locations

  @validation @playwright
  Scenario: View Schedule Mismatch flag details
    Given supervisor clicks on visit flagged as "Schedule Mismatch"
    Then system should display:
      | Scheduled date | 2026-04-24 |
      | Scheduled time | 09:30 |
      | Actual check-in date | 2026-04-23 |
      | Actual check-in time | 09:30 |
      | Mismatch type | Wrong Day |
      | Explanation | "Kunjungan dijadwalkan untuk besok, tetapi dilakukan hari ini" |

  @validation @playwright
  Scenario: View Route Deviation flag details
    Given supervisor clicks on visit flagged as "Route Deviation"
    Then system should display:
      | Assigned route sequence | 1 → 2 → 3 → 4 → 5 |
      | Actual route sequence | 1 → 3 → 2 → 4 → 5 |
      | Skipped location | Location #2 |
      | Out-of-sequence visit | Location #3 before #2 |
      | Additional distance | 3.5 km |
      | Additional travel time | 12 minutes |
      | Efficiency decrease | 15% |

  @happy-path @playwright
  Scenario: View visit history
    Given supervisor is viewing visit detail page
    When supervisor scrolls to "Riwayat Kunjungan" section
    Then supervisor should see:
      | Previous visits to this location | 3 visits |
      | Dates of previous visits | Listed |
      | Worker performance at location | Average rating: 4.5/5 |

  @edge-case @playwright
  Scenario: View visit with multiple previous reviews
    Given visit has been reviewed by 2 different supervisors
    When supervisor views visit detail
    Then supervisor should see "Keputusan Supervisor Lain" section
    And section should show:
      | Supervisor A | Decision: Flagged | Date: 2026-04-23 |
      | Supervisor B | Decision: Valid | Date: 2026-04-24 |
    And current supervisor should see note: "Keputusan terakhir: Valid"

  @edge-case @playwright
  Scenario: View visit with revision history
    Given visit has been revised 2 times
    When supervisor clicks "Lihat Riwayat Revisi"
    Then supervisor should see revision history:
      | Revision 1 | Invalid → Flagged | Supervisor A | 2026-04-23 10:00 | Reason: Worker provided explanation |
      | Revision 2 | Flagged → Valid | Supervisor B | 2026-04-24 09:00 | Reason: Additional evidence received |
```

---

### US-03: Memberikan Keputusan Approval

```gherkin
Feature: FEAT-004-US-03 Approval Decision
  As Supervisor
  I want memberikan keputusan (Valid/Flagged/Invalid) dengan catatan
  So that status kunjungan final dan worker menerima kejelasan

  Background:
    Given user is logged in as "Supervisor"
    And supervisor is viewing a flagged visit detail page

  @happy-path @playwright
  Scenario: Approve visit as Valid
    Given supervisor clicks "✅ Setujui (Valid)" button
    Then system should display confirmation: "Anda yakin ingin menyetujui kunjungan ini?"
    When supervisor confirms approval
    Then visit status should change to "Valid"
    And flag should be removed
    And visit should be marked as "Kunjungan Sah"
    And system should record:
      | Decision | Valid |
      | Supervisor | [Supervisor Name] |
      | Timestamp | [Current time] |
    And success message should be displayed: "Kunjungan disetujui"

  @validation @playwright
  Scenario: Flag visit as suspicious with notes
    Given supervisor clicks "⚠️ Tandai Mencurigakan" button
    Then system should display decision form with required fields:
      | Alasan penandaan (dropdown) | [Required] |
      | Catatan tambahan (text) | [Required] |
    When supervisor selects reason: "Lokasi tidak sesuai foto"
    And supervisor enters note: "Foto menunjukkan toko berbeda dengan lokasi GPS"
    And supervisor submits decision
    Then visit status should change to "Flagged"
    And system should record decision with notes
    And decision should be marked for ongoing monitoring
    And worker should receive notification with details

  @validation @playwright
  Scenario: Reject visit as Invalid with severity level
    Given supervisor clicks "❌ Tolak (Invalid)" button
    Then system should display decision form with required fields:
      | Alasan penolakan (dropdown) | [Required] |
      | Catatan tambahan (text) | [Required] |
      | Tingkat keparahan (dropdown) | [Required] |
    When supervisor selects reason: "Foto dari galeri"
    And supervisor enters note: "EXIF data tidak valid, terdeteksi dari galeri"
    And supervisor selects severity: "High"
    And supervisor submits decision
    Then visit status should change to "Invalid"
    And system should record decision with notes and severity
    And worker should receive notification with details
    And Manager/HR should receive notification about High severity rejection

  @validation @playwright
  Scenario: Cannot submit Flagged/Invalid without notes
    Given supervisor clicks "⚠️ Tandai Mencurigakan" button
    And system displays decision form
    When supervisor attempts to submit without filling required fields
    Then submit button should be disabled
    Or system should show error: "Alasan dan catatan wajib diisi"

  @edge-case @playwright
  Scenario: Approve visit with optional note
    Given supervisor clicks "✅ Setujui (Valid)" button
    And supervisor confirms approval
    Then system should offer optional note field
    When supervisor adds note: "Kunjungan valid, tidak ada masalah"
    And supervisor submits
    Then visit should be approved as Valid
    And note should be saved for reference

  @error-handling @playwright
  Scenario: Cancel decision before confirmation
    Given supervisor clicks "✅ Setujui (Valid)" button
    And confirmation dialog is displayed
    When supervisor clicks "Batal" or cancels the dialog
    Then decision should not be submitted
    And visit status should remain unchanged
    And supervisor should return to detail page

  @edge-case @playwright
  Scenario: Multiple decisions for same visit
    Given visit has already been reviewed and marked as "Flagged"
    When supervisor views visit detail
    Then current decision should be displayed
    And supervisor should see option to "Revisi Keputusan"
    And original decision buttons should be disabled or hidden
```

---

### US-04: Notifikasi Keputusan ke Worker

```gherkin
Feature: FEAT-004-US-04 Decision Notification to Worker
  As Field Worker
  I want menerima notifikasi tentang keputusan supervisor
  So that saya mengetahui status final kunjungan dan dapat mengambil tindakan jika perlu

  Background:
    Given user is logged in as "Field Worker"
    And worker has completed a visit that was flagged
    And supervisor has reviewed and made a decision

  @happy-path @maestro @playwright
  Scenario: Receive Valid decision notification
    Given supervisor approves visit as "Valid"
    When worker checks device notifications
    Then worker should receive notification:
      | Title | "Kunjungan Disetujui" |
      | Body | "Kunjungan Anda di Toko ABC pada 23/04/2026 telah disetujui." |
      | Status | Valid |
    And notification should not require any action
    And worker should be able to tap notification for details

  @happy-path @maestro @playwright
  Scenario: Receive Flagged decision notification
    Given supervisor marks visit as "Flagged"
    When worker checks device notifications
    Then worker should receive notification:
      | Title | "Kunjungan Ditandai" |
      | Body | "Kunjungan Anda di Toko ABC pada 23/04/2026 ditandai sebagai mencurigakan." |
      | Reason | "Lokasi tidak sesuai foto" |
      | Note | "Foto menunjukkan toko berbeda dengan lokasi GPS" |
      | Status | Flagged |
    And notification should include: "Kunjungan ini tetap dihitung tetapi akan dipantau lebih lanjut."
    And worker should be able to tap notification for details

  @happy-path @maestro @playwright
  Scenario: Receive Invalid decision notification
    Given supervisor rejects visit as "Invalid"
    And severity is "Medium"
    When worker checks device notifications
    Then worker should receive notification:
      | Title | "Kunjungan Ditolak" |
      | Body | "Kunjungan Anda di Toko ABC pada 23/04/2026 ditolak." |
      | Reason | "Foto dari galeri" |
      | Note | "EXIF data tidak valid, terdeteksi dari galeri" |
      | Status | Invalid |
    And notification should include: "Kunjungan ini tidak dihitung. Silakan ulangi kunjungan jika perlu."

  @validation @maestro @playwright
  Scenario: Receive Invalid decision with High severity
    Given supervisor rejects visit as "Invalid"
    And severity is "High"
    When worker checks device notifications
    Then worker should receive notification with severity indication
    And notification should be marked as urgent
    And notification should include all standard Invalid notification details
    And Manager/HR should also be notified separately

  @happy-path @maestro @playwright
  Scenario: View notification details
    Given worker has received decision notification
    When worker taps on notification
    Then app should open to visit detail page
    And page should display:
      | Decision status | [Valid/Flagged/Invalid] |
      | Supervisor name | [Supervisor who made decision] |
      | Decision timestamp | [Date and time] |
      | Reason | [Decision reason] |
      | Notes | [Decision notes] |
    And worker should see full visit context

  @validation @maestro @playwright
  Scenario: Filter decision notifications
    Given worker has received 10 decision notifications
    And notifications include various statuses (Valid/Flagged/Invalid)
    When worker opens notification history
    Then worker should be able to filter by status
    When worker applies filter "Status: Flagged"
    Then only Flagged notifications should be displayed
    And other notifications should be hidden

  @edge-case @maestro @playwright
  Scenario: No notification permissions
    Given worker has denied notification permissions
    When supervisor makes a decision
    Then notification should not be displayed
    And notification should be logged in app
    And worker should see badge indicator in app
    And worker should see notification when they open the app

  @edge-case @maestro @playwright
  Scenario: Receive revised decision notification
    Given supervisor has revised decision from "Invalid" to "Valid"
    When worker checks notifications
    Then worker should receive notification:
      | Title | "Keputusan Diperbarui" |
      | Body | "Keputusan untuk kunjungan di Toko ABC pada 23/04/2026 telah diperbarui." |
      | Old decision | Invalid |
      | New decision | Valid |
      | Reason | "Worker provided additional explanation" |
    And notification should include link to view revision history
```

---

### US-05: Revisi Keputusan Supervisor

```gherkin
Feature: FEAT-004-US-05 Supervisor Decision Revision
  As Supervisor
  I want mengubah keputusan sebelumnya jika ada informasi baru atau kesalahan
  So that keputusan yang adil dapat diperbaiki dan worker tidak dirugikan

  Background:
    Given user is logged in as "Supervisor"
    And supervisor previously reviewed and made a decision for a visit
    And visit status is "Flagged" or "Valid" or "Invalid"

  @happy-path @playwright
  Scenario: Revise decision with new information
    Given supervisor is viewing a visit with existing decision
    And current decision is "Flagged"
    When supervisor clicks "↻ Revisi Keputusan" button
    Then system should display revision form showing:
      | Current decision | Flagged |
      | Current reason | "Lokasi tidak sesuai foto" |
      | Current notes | "Foto menunjukkan toko berbeda" |
      | Current date | 2026-04-23 |
    And form should require:
      | Reason for revision (dropdown) | [Required] |
      | Revision notes (text) | [Required] |
      | New decision | [Valid/Flagged/Invalid] |
    When supervisor selects reason: "Informasi baru"
    And supervisor enters note: "Worker memberikan bukti bahwa foto benar"
    And supervisor selects new decision: "Valid"
    And supervisor submits revision
    Then visit status should update to "Valid"
    And revision history should be recorded:
      | Old decision | Flagged → Valid |
      | Revising supervisor | [Supervisor Name] |
      | Revision timestamp | [Current time] |
      | Revision reason | "Informasi baru" |
      | Revision notes | "Worker memberikan bukti" |
    And worker should receive notification about decision change

  @validation @playwright
  Scenario: Cannot revise without providing reason and notes
    Given supervisor clicks "↻ Revisi Keputusan" button
    And revision form is displayed
    When supervisor attempts to submit without filling required fields
    Then submit button should be disabled
    Or system should show error: "Alasan revisi dan catatan wajib diisi"

  @validation @playwright
  Scenario: New decision requires notes if Flagged/Invalid
    Given supervisor is revising from "Valid" to "Flagged"
    And supervisor fills in revision reason
    When supervisor selects new decision "Flagged"
    And supervisor attempts to submit without new notes
    Then system should show error: "Catatan wajib diisi untuk keputusan Flagged/Invalid"
    And revision should not be submitted

  @edge-case @playwright
  Scenario: View complete revision history
    Given visit has been revised 3 times
    When supervisor clicks "Lihat Riwayat Revisi Lengkap"
    Then supervisor should see complete revision history:
      | Original decision | Invalid | Supervisor A | 2026-04-23 09:00 | Reason: Foto dari galeri |
      | Revision 1 | Invalid → Flagged | Supervisor B | 2026-04-23 14:00 | Reason: Worker appealed |
      | Revision 2 | Flagged → Valid | Supervisor A | 2026-04-24 10:00 | Reason: Additional evidence |
    And each revision should show all details

  @edge-case @playwright
  Scenario: Manager can view revision statistics
    Given manager is logged in
    And manager accesses "Audit & Compliance" section
    When manager views revision statistics
    Then manager should see:
      | Total revisions | 25 |
      | Most frequent reviser | Supervisor A (12 revisions) |
      | Most common revision reason | Informasi baru (15) |
      | Revisions by supervisor | [Breakdown by supervisor] |

  @error-handling @playwright
  Scenario: Cancel revision before confirmation
    Given supervisor has filled in revision form
    And supervisor has not yet submitted
    When supervisor clicks "Batal" or closes form
    Then revision should not be saved
    And original decision should remain unchanged
    And supervisor should return to detail page

  @edge-case @playwright
  Scenario: Time limit for revision (if enforced)
    Given business rule limits revisions to 7 days
    And original decision was made on 2026-04-15
    And current date is 2026-04-25 (10 days later)
    When supervisor views visit detail
    Then "Revisi Keputusan" button should be disabled or hidden
    And supervisor should see message: "Batas waktu revisi telah terlewati (7 hari)"
```

---

## 3. Validation Rules

<!--
Field-level validation. Derived from business rules and data requirements.
Dev uses this for implementation, QA uses this for test cases.
-->

| Field | Rule | Error Message |
|-------|------|---------------|
| Decision Type | Required: Valid/Flagged/Invalid | "Keputusan harus dipilih" |
| Reason (for Flagged/Invalid) | Required, dropdown or text | "Alasan wajib dipilih" |
| Notes (for Flagged/Invalid) | Required, minimum 10 characters | "Catatan wajib diisi (minimal 10 karakter)" |
| Notes (for Valid) | Optional | - |
| Severity Level (for Invalid) | Required: Low/Medium/High | "Tingkat keparahan wajib dipilih" |
| Revision Reason | Required | "Alasan revisi wajib diisi" |
| Revision Notes | Required, minimum 10 characters | "Catatan revisi wajib diisi (minimal 10 karakter)" |
| Review Status | Must be reviewed before final | "Kunjungan harus ditinjau supervisor" |
| Authorization | Only supervisor can access | "Anda tidak memiliki akses" |

---

## 4. Test Cases

<!--
Detailed test cases expanded from acceptance criteria.
ID format: TC-004-SEQ
-->

### 4.1 Functional Tests

| ID | Scenario | Preconditions | Steps | Expected Result | Priority | Automated |
|----|----------|---------------|-------|-----------------|----------|-----------|
| TC-004-001 | View flagged visits dashboard | 15 flagged visits | 1. Open dashboard | All visits displayed with details, counters correct | P1 | Yes (Playwright) |
| TC-004-002 | Filter by flag category | 15 flagged visits | 1. Apply filter | Only matching category shown | P1 | Yes (Playwright) |
| TC-004-003 | Filter by review status | 15 flagged visits | 1. Apply filter | Only matching status shown | P1 | Yes (Playwright) |
| TC-004-004 | Sort by priority | 15 flagged visits | 1. Sort by priority | Ordered High → Low | P2 | Yes (Playwright) |
| TC-004-005 | Export flagged visits report | 10 visits match filter | 1. Export CSV/PDF | Files downloaded with correct data | P2 | Yes (Playwright) |
| TC-004-006 | Empty dashboard state | No flagged visits | 1. Open dashboard | Empty state message shown | P3 | Yes (Playwright) |
| TC-004-007 | View GPS Invalid details | GPS Invalid flag | 1. Click visit | Location details, map, distance shown | P1 | Yes (Playwright) |
| TC-004-008 | View Photo Suspicious details | Photo Suspicious flag | 1. Click visit | Photo, EXIF, reason shown | P1 | Yes (Playwright) |
| TC-004-009 | View Schedule Mismatch details | Schedule Mismatch flag | 1. Click visit | Schedule vs actual shown | P1 | Yes (Playwright) |
| TC-004-010 | View Route Deviation details | Route Deviation flag | 1. Click visit | Assigned vs actual route shown | P1 | Yes (Playwright) |
| TC-004-011 | View visit history | Visit with 3 previous visits | 1. Scroll to history | Previous visits displayed | P2 | Yes (Playwright) |
| TC-004-012 | Approve as Valid | Flagged visit | 1. Click Valid, 2. Confirm | Status changes to Valid, flag removed | P1 | Yes (Playwright) |
| TC-004-013 | Flag as suspicious | Flagged visit | 1. Click Flagged, 2. Fill form, 3. Submit | Status changes, notes saved, worker notified | P1 | Yes (Playwright) |
| TC-004-014 | Reject as Invalid Medium severity | Flagged visit | 1. Click Invalid, 2. Fill form, 3. Submit | Status changes, worker notified | P1 | Yes (Playwright) |
| TC-004-015 | Reject as Invalid High severity | Flagged visit | 1. Click Invalid, 2. Select High, 3. Submit | Status changes, worker + Manager/HR notified | P1 | Yes (Playwright) |
| TC-004-016 | Cannot submit without notes | Decision form empty | 1. Attempt submit | Button disabled or error shown | P1 | Yes (Playwright) |
| TC-004-017 | Approve with optional note | Valid decision | 1. Approve, 2. Add optional note | Approved with note saved | P2 | Yes (Playwright) |
| TC-004-018 | Cancel before confirmation | Confirmation dialog | 1. Click cancel | Decision not submitted, status unchanged | P2 | Yes (Playwright) |
| TC-004-019 | Receive Valid notification | Worker, Valid decision | 1. Check notifications | Valid notification received | P1 | Yes (Maestro + Playwright) |
| TC-004-020 | Receive Flagged notification | Worker, Flagged decision | 1. Check notifications | Flagged notification with details received | P1 | Yes (Maestro + Playwright) |
| TC-004-021 | Receive Invalid notification | Worker, Invalid decision | 1. Check notifications | Invalid notification with details received | P1 | Yes (Maestro + Playwright) |
| TC-004-022 | Receive High severity notification | Worker, High Invalid | 1. Check notifications | Urgent notification received | P1 | Yes (Maestro + Playwright) |
| TC-004-023 | View notification details | Worker, notification | 1. Tap notification | Visit detail page opened | P1 | Yes (Maestro + Playwright) |
| TC-004-024 | Filter notification history | Worker, 10 notifications | 1. Apply filter | Only matching status shown | P2 | Yes (Maestro + Playwright) |
| TC-004-025 | Revise decision with new info | Existing decision | 1. Revise, 2. Fill form, 3. Submit | Status updated, history logged, worker notified | P1 | Yes (Playwright) |
| TC-004-026 | Cannot revise without reason | Revision form empty | 1. Attempt submit | Error shown, revision blocked | P1 | Yes (Playwright) |
| TC-004-027 | New decision requires notes | Revise to Flagged/Invalid | 1. Select without notes | Error shown, revision blocked | P1 | Yes (Playwright) |
| TC-004-028 | View revision history | 3 revisions | 1. View history | Complete history displayed | P2 | Yes (Playwright) |
| TC-004-029 | Manager views revision statistics | Manager login | 1. Access audit section | Statistics displayed correctly | P2 | Yes (Playwright) |
| TC-004-030 | Cancel revision before submit | Revision form filled | 1. Cancel | Original decision unchanged | P2 | Yes (Playwright) |

### 4.2 Security Tests

| ID | Scenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-004-S01 | Worker cannot access dashboard | 1. Worker attempts to access review dashboard | Access denied (403) | P1 |
| TC-004-S02 | Manager cannot make decisions | 1. Manager attempts to approve/reject | Action buttons disabled or access denied | P2 |
| TC-004-S03 | Decision audit trail | 1. Review audit logs | All decisions logged with supervisor ID and timestamp | P1 |
| TC-004-S04 | Revision authorization | 1. Check if supervisor A can revise supervisor B | Only original supervisor can revise (unless authorized) | P2 |

### 4.3 Usability Tests

| ID | Scenario | Steps | Expected Result | Priority |
|----|----------|-------|-----------------|----------|
| TC-004-U01 | Dashboard usability | 1. Navigate dashboard | Easy to filter, sort, view details | P2 |
| TC-004-U02 | Notification clarity | 1. Receive various notifications | Messages clear, actionable | P2 |
| TC-004-U03 | Decision workflow clarity | 1. Make decision through workflow | Steps intuitive, forms clear | P2 |
| TC-004-U04 | Revision history readability | 1. View revision history | History clear, complete, easy to follow | P3 |
| TC-004-U05 | Counter visibility | 1. View dashboard | Counters prominent, accurate | P2 |

---

## 5. Test Data

<!--
Specific test accounts, seed data, and fixtures needed.
-->

### 5.1 Test Accounts

| Account | Role | Credentials | Purpose |
|---------|------|-------------|---------|
| test_supervisor | Supervisor | supervisor@test.com / Test123! | Dashboard and decision making |
| test_supervisor_2 | Supervisor | supervisor2@test.com / Test123! | Revision testing |
| test_worker_01 | Field Worker | worker01@test.com / Test123! | Notification testing |
| test_manager | Manager | manager@test.com / Test123! | Audit and statistics |

### 5.2 Test Data Sets

| Dataset | Description | Location |
|---------|-------------|----------|
| flagged_visits.json | Pre-flagged visits across categories | tests/web/fixtures/visits/ |
| decisions_history.json | Previous decisions for revision testing | tests/web/fixtures/decisions/ |

### 5.3 Test Visits

| Visit ID | Worker | Flag Category | Location | Date/Time | Priority |
|----------|--------|---------------|----------|-----------|----------|
| V-001 | John Doe | GPS Invalid | Toko ABC | 2026-04-23 09:15 | High |
| V-002 | Jane Smith | Photo Suspicious | Toko DEF | 2026-04-23 10:30 | Medium |
| V-003 | Bob Brown | Schedule Mismatch | Toko GHI | 2026-04-23 14:00 | Low |
| V-004 | Alice White | Route Deviation | Toko JKL | 2026-04-23 11:00 | Medium |

---

## 6. Automation Plan

### 6.1 Coverage

| Category | Total | Automated | Manual | Reason for Manual |
|----------|-------|-----------|--------|-------------------|
| Functional | 30 | 27 | 3 | TC-004-006, TC-004-017, TC-004-030 require specific test setups |
| Security | 4 | 4 | 0 | All security tests automatable |
| Usability | 5 | 0 | 5 | Visual/subjective assessment required |

**Total Automation Coverage: 90% (31/34 test cases)**

### 6.2 Playwright Mapping (Web)

| Test Case | Playwright File | Page Object | Status |
|-----------|-----------------|-------------|--------|
| TC-004-001 to TC-004-006 | features/flagged-dashboard.spec.ts | pages/FlaggedVisitsDashboardPage.ts<br>pages/VisitDetailPage.ts | Pending |
| TC-004-007 to TC-004-011 | features/visit-detail-review.spec.ts | pages/VisitDetailPage.ts<br>pages/FlagDetailsSection.ts | Pending |
| TC-004-012 to TC-004-018 | features/decision-workflow.spec.ts | pages/VisitDetailPage.ts<br>pages/DecisionFormPage.ts | Pending |
| TC-004-025 to TC-004-030 | features/decision-revision.spec.ts | pages/VisitDetailPage.ts<br>pages/RevisionHistoryPage.ts | Pending |

### 6.3 Maestro Mapping (Mobile)

| Test Case | Maestro File | Status |
|-----------|--------------|--------|
| TC-004-019 to TC-004-024 | flows/notification-valid.yaml<br>flows/notification-flagged.yaml<br>flows/notification-invalid.yaml<br>flows/notification-invalid-high.yaml<br>flows/notification-details.yaml<br>flows/notification-filter.yaml | Pending |

### 6.4 Selector Strategy

| Element | Selector | Type | Priority |
|---------|----------|------|----------|
| Review dashboard | data-testid="review-dashboard" | data-testid | Highest |
| Flagged visit item | data-testid="flagged-visit-{id}" | data-testid | Highest |
| Filter dropdown | aria-label="Filter visits" | aria-label | High |
| Sort dropdown | aria-label="Sort visits" | aria-label | High |
| Export CSV button | data-testid="export-csv-btn" | data-testid | High |
| Approve button | data-testid="approve-btn" | data-testid | Highest |
| Flag button | data-testid="flag-btn" | data-testid | Highest |
| Reject button | data-testid="reject-btn" | data-testid | Highest |
| Decision form | data-testid="decision-form" | data-testid | High |
| Reason dropdown | data-testid="reason-dropdown" | data-testid | High |
| Notes textarea | data-testid="notes-textarea" | data-testid | High |
| Revision button | data-testid="revise-btn" | data-testid | High |
| Notification item | data-testid="notification-{id}" | data-testid | High |

**Selector Priority:** data-testid > aria-label/role > CSS class > text

---

## 7. UAT Scenarios

<!--
Client-facing test scripts. Ditulis dalam Bahasa Indonesia sesuai konfigurasi project.
-->

### UAT Scenario 01: Dashboard Kunjungan Flagged

**Objective:** Memastikan supervisor dapat melihat dan mengelola kunjungan yang ditandai.

**Prerequisites:**
- Supervisor login ke web dashboard
- Minimal 5 kunjungan yang ditandai sistem

**Steps:**
1. Buka web dashboard supervisor
2. Buka menu "Review Kunjungan"
3. Lihat daftar kunjungan yang ditandai
4. Coba filter berdasarkan kategori flag
5. Coba urutkan berdasarkan prioritas
6. Klik salah satu kunjungan untuk melihat detail

**Expected Results:**
- Dashboard menampilkan semua kunjungan flagged dengan informasi lengkap
- Setiap kunjungan menampilkan:
  - Nama worker
  - Tanggal/waktu check-in
  - Lokasi
  - Kategori flag
  - Status review
  - Badge prioritas
- Counter ditampilkan dengan benar (total, pending, reviewed today)
- Filter berfungsi (GPS/Photo/Schedule/Route)
- Pengurutan berfungsi (prioritas, tanggal, status)
- Klik kunjungan menampilkan detail lengkap

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 02: Memberikan Keputusan Approval

**Objective:** Memastikan supervisor dapat memberikan keputusan dengan catatan.

**Prerequisites:**
- Supervisor login ke dashboard
- Kunjungan ditandai "Photo Suspicious"

**Steps:**
1. Buka detail kunjungan flagged
2. Lihat semua informasi (worker, kunjungan, flag details)
3. Klik salah satu tombol keputusan:
   a. ✅ Setujui (Valid)
   b. ⚠️ Tandai Mencurigakan (Flagged)
   c. ❌ Tolak (Invalid)
4. Untuk opsi (b) atau (c), isi form wajib:
   - Pilih alasan
   - Isi catatan tambahan
5. Submit keputusan

**Expected Results:**
- **Untuk Valid:**
  - Konfirmasi ditampilkan
  - Status berubah ke "Valid"
  - Flag dihapus
  - Kunjungan dianggap sah

- **Untuk Flagged:**
  - Form wajib ditampilkan
  - Setelah submit, status berubah ke "Flagged"
  - Catatan disimpan
  - Worker menerima notifikasi

- **Untuk Invalid:**
  - Form wajib ditampilkan
  - Pilih tingkat keparahan
  - Setelah submit, status berubah ke "Invalid"
  - Worker menerima notifikasi
  - Jika High severity, Manager/HR diberi notifikasi

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 03: Notifikasi ke Worker

**Objective:** Memastikan worker menerima notifikasi keputusan supervisor.

**Prerequisites:**
- Worker login ke aplikasi
- Supervisor memberikan keputusan (Valid/Flagged/Invalid)
- Izin notifikasi aktif

**Steps:**
1. Supervisor memberikan keputusan untuk kunjungan
2. Worker menunggu notifikasi
3. Worker menerima notifikasi
4. Worker ketuk notifikasi untuk melihat detail

**Expected Results:**
- Worker menerima notifikasi push di device
- Notifikasi berisi:
  - Judul (tergantung keputusan)
  - Detail keputusan
  - Alasan
  - Catatan
- Ketuk notifikasi membuka halaman detail dengan:
  - Status keputusan
  - Alasan dan catatan lengkap
  - Informasi kunjungan

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 04: Revisi Keputusan

**Objective:** Memastikan supervisor dapat merevisi keputusan yang sudah ada.

**Prerequisites:**
- Supervisor login ke dashboard
- Kunjungan sudah direview dengan keputusan "Flagged"

**Steps:**
1. Buka detail kunjungan yang sudah direview
2. Klik tombol "Revisi Keputusan"
3. Lihat keputusan saat ini dan alasan
4. Pilih alasan revisi
5. Isi catatan revisi
6. Pilih keputusan baru
7. Submit revisi

**Expected Results:**
- Form revisi ditampilkan dengan:
  - Keputusan saat ini
  - Riwayat keputusan sebelumnya
  - Form alasan dan catatan revisi
- Setelah submit:
  - Status kunjungan diperbarui
  - Riwayat revisi dicatat
  - Worker menerima notifikasi perubahan keputusan
- Manager dapat melihat statistik revisi

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 05: Filter dan Ekspor Laporan

**Objective:** Memastikan supervisor dapat memfilter dan mengekspor data.

**Prerequisites:**
- Supervisor login ke dashboard
- Minimal 10 kunjungan flagged

**Steps:**
1. Buka dashboard "Review Kunjungan"
2. Terapkan filter (misal: hanya GPS Invalid)
3. Klik "Export to CSV"
4. Buka file CSV yang diunduh
5. Kembali ke dashboard, klik "Export to PDF"
6. Buka file PDF yang diunduh

**Expected Results:**
- Filter berfungsi, hanya menampilkan kategori yang dipilih
- Export CSV:
  - File diunduh
  - Berisi semua data kunjungan yang sesuai filter
  - Format CSV dengan kolom yang benar
- Export PDF:
  - File diunduh
  - Format report rapi dan profesional
  - Cocok untuk dibagikan ke manajemen

**Status:** [ ] Pass  [ ] Fail

**Notes:**
_____________________

---

### UAT Scenario 06: Performance Dashboard

**Objective:** Memastikan dashboard perform dengan baik.

**Prerequisites:**
- Supervisor login ke dashboard
- Sistem memiliki 100 kunjungan flagged

**Steps:**
1. Buka dashboard "Review Kunjungan"
2. Catat waktu loading
3. Coba filter beberapa kali
4. Cek penggunaan memori (opsional)

**Expected Results:**
- Dashboard dimuat dalam <3 detik
- Filter diterapkan dalam <1 detik
- Tidak ada lag saat scroll atau interaksi
- Counter ditampilkan dengan akurat

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
FILE LOCATION: docs/features/001-visit-verification/04-supervisor-review---testing.md

RELATED FILES:
- 04-supervisor-review---business.md (PM: user stories, business rules)
- 04-supervisor-review---technical.md (Dev: API, data model)
- 04-supervisor-review---design.md (Design: UI/UX specifications)

GENERATED BY: QA Engineer (mengikuti user stories dari Business Spec)
-->
