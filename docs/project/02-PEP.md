# PROJECT EXECUTION PLAN
## RuteGo – Sales Route Planning & Execution System

---

| Atribut | Nilai |
|---------|-------|
| Document ID | PEP-RUTEGO-001 |
| Versi | 1.0 |
| Status | Draft |
| Author | Product Manager |
| Dibuat | 2026-04-23 |
| Terakhir Diperbarui | 2026-04-23 |

---

## Changelog

| Versi | Tanggal | Author | Perubahan |
|-------|---------|--------|-----------|
| 1.0 | 2026-04-23 | Product Manager | Versi awal |

---

## 1. Project Overview

### 1.1 Project Snapshot

| Atribut | Nilai |
|---------|-------|
| Nama Proyek | RuteGo – Sales Route Planning & Execution System |
| Project Code | RUTEGO-2026 |
| Tipe Proyek | Project Based |
| Tanggal Mulai | 2026-04-23 |
| Tanggal Selesai | 2026-08-05 |
| Durasi | 15 minggu |
| Metodologi | Scrum |
| Durasi Sprint | 2 minggu |
| Total Sprint | 6 sprint + Sprint 0 |

### 1.2 Dokumen Referensi

| Dokumen | Lokasi |
|---------|--------|
| BRD | `docs/project/01-BRD.md` |
| ITA | `docs/project/03-ITA.md` |
| Project Charter | `references/RuteGo - Project Charter.md` |

---

## 2. Tim Proyek

### 2.1 Tim Klien

| Peran | Nama | Tanggung Jawab |
|-------|------|----------------|
| Executive Sponsor | TBD | Persetujuan strategis, eskalasi |
| Department Sponsor | TBD | Persetujuan kebutuhan bisnis, UAT final |
| Head of Sales | TBD | Business owner, penerima manfaat utama |
| Sales Manager | TBD | UAT, validasi fitur lapangan |

### 2.2 Tim Vendor (GITS)

| Peran | Nama | Alokasi | Tanggung Jawab |
|-------|------|---------|----------------|
| Product Manager | TBD | 100% | Delivery proyek, koordinasi stakeholder |
| UI/UX Designer | TBD | 100% → 50% (setelah M2) | Desain antarmuka & UX |
| Tech Lead | TBD | 100% | Arsitektur sistem, code review |
| Backend Engineer | TBD | 100% | Pengembangan API & logika bisnis |
| Frontend/Mobile Engineer | TBD | 100% | Pengembangan antarmuka web & mobile |
| QA Engineer | TBD | 50% → 100% (dari Sprint 5) | Pengujian kualitas sistem |

### 2.3 RACI Matrix

| Deliverable | Exec Sponsor | Head of Sales | PM | Tech Lead | Dev | QA | Designer |
|-------------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| BRD & Requirements | A | R | R | C | I | C | C |
| Arsitektur (ITA) | I | I | C | R | R | I | I |
| UI/UX Design | A | R | R | C | I | I | R |
| Development | I | I | A | R | R | C | I |
| Testing (QA) | I | C | A | C | C | R | I |
| UAT | A | R | R | C | I | C | I |
| Go-Live | A | R | R | R | R | C | I |

*R=Responsible, A=Accountable, C=Consulted, I=Informed*

---

## 3. Ruang Lingkup (Scope)

### 3.1 In Scope

| # | Epic / Fitur | Prioritas | Referensi |
|---|-------------|-----------|-----------|
| 1 | Route Optimization – Perencanaan rute harian otomatis | Must Have | EPIC-001 |
| 2 | Route Optimization – Navigasi turn-by-turn in-app | Must Have | EPIC-001 |
| 3 | Visit Verification – Check-in/check-out dengan GPS | Must Have | EPIC-002 |
| 4 | Visit Verification – Foto kunjungan via kamera aplikasi | Must Have | EPIC-002 |
| 5 | Visit Verification – Timestamp otomatis | Must Have | EPIC-002 |
| 6 | Digital Order Management – Form order digital in-app | Must Have | EPIC-003 |
| 7 | Digital Order Management – Riwayat dan status order | Must Have | EPIC-003 |
| 8 | Management Dashboard – Dashboard monitoring aktivitas sales | Must Have | EPIC-004 |
| 9 | Route Optimization – Penyesuaian rute real-time | Should Have | EPIC-001 |
| 10 | Management Dashboard – Laporan kinerja sales (harian/mingguan) | Should Have | EPIC-004 |

### 3.2 Out of Scope

| # | Item | Alasan | Fase Berikutnya? |
|---|------|--------|-----------------|
| 1 | Integrasi ERP kompleks | Di luar scope proyek saat ini; membutuhkan analisis terpisah | Fase 2 |
| 2 | AI demand forecasting | Kompleksitas tinggi; belum ada baseline data | Fase 2 |
| 3 | Warehouse management system | Domain terpisah dari ruang lingkup sales operations | TBD |

### 3.3 Change Control

**Proses Change Request:**
1. Requestor mengajukan CR melalui PM (form CR tersedia di `docs/project/change-requests/`)
2. PM + Tech Lead mengevaluasi dampak (effort, timeline, biaya)
3. Jika effort > 3 man-days → diperlukan persetujuan Sponsor
4. Update PEP dan SPEC setelah CR disetujui
5. CR didokumentasikan di `docs/project/change-requests/CR-XXX-slug.md`

---

## 4. Timeline & Milestone

### 4.1 Visualisasi Timeline

```
Apr                  Mei                  Jun                  Jul                  Agu
W1  W2  W3  W4  W5  W6  W7  W8  W9  W10 W11 W12 W13 W14 W15
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
[S0      ][S1      ][S2      ][S3      ][S4      ][S5      ][S6      ][Deploy]
     ▲         ▲                   ▲                   ▲         ▲         ▲
    M1         M2                  M3                  M4        M5       M5
```

```
Sprint 0  : Discovery & Setup       (2026-04-23 – 2026-05-06)
Sprint 1  : Design & Architecture   (2026-05-07 – 2026-05-20)
Sprint 2  : Core – Route & Auth     (2026-05-21 – 2026-06-03)
Sprint 3  : Visit Verification      (2026-06-04 – 2026-06-17)
Sprint 4  : Digital Order           (2026-06-18 – 2026-07-01)
Sprint 5  : Dashboard & Integration (2026-07-02 – 2026-07-15)
Sprint 6  : QA & UAT               (2026-07-16 – 2026-07-29)
Deployment: Deploy & Training       (2026-07-30 – 2026-08-05)
```

### 4.2 Milestones

| ID | Milestone | Target Tanggal | Deliverables | Status |
|----|-----------|----------------|--------------|--------|
| M1 | Kickoff Complete | 2026-04-23 | Tim onboard, environment setup dimulai, BRD final | Pending |
| M2 | Design & Architecture Complete | 2026-05-20 | UI/UX design disetujui, ITA final, API contract | Pending |
| M3 | MVP Ready | 2026-07-01 | Semua fitur Must Have selesai di staging | Pending |
| M4 | UAT Complete | 2026-07-29 | UAT sign-off dari klien, zero critical bugs | Pending |
| M5 | Go-Live | 2026-08-05 | Sistem aktif di production, tim dilatih | Pending |

---

## 5. Sprint Planning

### Sprint 0: Discovery & Setup
**Durasi:** 2026-04-23 – 2026-05-06

| # | Aktivitas | Owner | Status |
|---|-----------|-------|--------|
| 1 | Project kickoff meeting dengan seluruh stakeholder | PM | Planned |
| 2 | Finalisasi requirement & BRD | PM | Planned |
| 3 | Setup environment: development, staging, production | Tech Lead | Planned |
| 4 | Setup repository & CI/CD pipeline dasar | Tech Lead | Planned |
| 5 | Kick-off desain: eksplorasi visual & komponen | Designer | Planned |
| 6 | Draft arsitektur sistem (ITA) | Tech Lead | Planned |
| 7 | Definisi API contract awal | Tech Lead + BE | Planned |

**Goals Sprint 0:**
- [ ] Seluruh tim onboard dan setup lingkungan kerja selesai
- [ ] BRD disetujui oleh stakeholder
- [ ] Draft ITA tersedia untuk review

---

### Sprint 1: Design & Architecture
**Durasi:** 2026-05-07 – 2026-05-20

| Feature ID | Deliverable | Owner | Status |
|------------|-------------|-------|--------|
| — | UI/UX design – Mobile app (Sales Rep flow) | Designer | Planned |
| — | UI/UX design – Web dashboard (Manager flow) | Designer | Planned |
| — | Finalisasi ITA & tech stack | Tech Lead | Planned |
| — | Database schema design | Tech Lead + BE | Planned |
| — | Setup project structure (backend & frontend) | Tech Lead | Planned |

**Goals Sprint 1:**
- [ ] Design UI/UX disetujui oleh klien (Head of Sales & Sales Manager)
- [ ] ITA final disetujui oleh Tech Lead
- [ ] Project scaffolding siap untuk development

**Deliverables:**
- [ ] Figma/Design file – semua screen
- [ ] `docs/project/03-ITA.md` (final)

---

### Sprint 2: Core – Autentikasi & Route Optimization
**Durasi:** 2026-05-21 – 2026-06-03

| Feature ID | Fitur | Story Points | Owner | Status |
|------------|-------|:---:|-------|--------|
| — | Autentikasi & manajemen user (login, role-based access) | 8 | BE + FE | Planned |
| FEAT-001 | Perencanaan rute harian otomatis (Google Maps API) | 13 | BE + FE | Planned |
| FEAT-002 | Navigasi turn-by-turn in-app | 8 | FE | Planned |

**Goals Sprint 2:**
- [ ] Sales Rep dapat login dan melihat rute harian yang dioptimasi
- [ ] Navigasi in-app berfungsi di perangkat mobile

**Deliverables:**
- [ ] Endpoint autentikasi (register, login, refresh token)
- [ ] Endpoint route optimization terintegrasi Google Maps
- [ ] Screen mobile: Login, Home/Rute Harian, Navigasi

---

### Sprint 3: Visit Verification
**Durasi:** 2026-06-04 – 2026-06-17

| Feature ID | Fitur | Story Points | Owner | Status |
|------------|-------|:---:|-------|--------|
| FEAT-004 | Check-in/check-out kunjungan dengan GPS | 8 | BE + FE | Planned |
| FEAT-005 | Pengambilan foto kunjungan via kamera aplikasi | 5 | FE | Planned |
| FEAT-006 | Timestamp otomatis pada setiap aktivitas kunjungan | 3 | BE | Planned |

**Goals Sprint 3:**
- [ ] Sales Rep dapat melakukan check-in dan check-out dengan verifikasi GPS
- [ ] Foto kunjungan dapat diambil dan tersimpan di server
- [ ] Setiap kunjungan memiliki timestamp otomatis yang tidak dapat dimanipulasi

**Deliverables:**
- [ ] Endpoint check-in/check-out dengan validasi GPS radius
- [ ] Upload foto ke cloud storage
- [ ] Screen mobile: Detail Kunjungan, Check-in, Check-out

---

### Sprint 4: Digital Order Management
**Durasi:** 2026-06-18 – 2026-07-01

| Feature ID | Fitur | Story Points | Owner | Status |
|------------|-------|:---:|-------|--------|
| FEAT-007 | Form order digital in-app (produk, kuantitas, catatan) | 8 | BE + FE | Planned |
| FEAT-008 | Riwayat dan status order | 5 | BE + FE | Planned |
| FEAT-003 | Penyesuaian rute secara real-time (Should Have) | 5 | BE + FE | Planned |

**Goals Sprint 4:**
- [ ] Sales Rep dapat membuat order digital selama kunjungan
- [ ] Order tersimpan dan dapat dilacak statusnya
- [ ] Sales Rep dapat mengubah urutan kunjungan secara manual

**Deliverables:**
- [ ] Endpoint CRUD order dengan katalog produk
- [ ] Screen mobile: Form Order, Riwayat Order
- [ ] Fitur drag-and-drop penyesuaian rute (opsional jika waktu mencukupi)

---

### Sprint 5: Management Dashboard & Integrasi
**Durasi:** 2026-07-02 – 2026-07-15

| Feature ID | Fitur | Story Points | Owner | Status |
|------------|-------|:---:|-------|--------|
| FEAT-009 | Dashboard monitoring aktivitas sales (real-time) | 13 | BE + FE | Planned |
| FEAT-010 | Laporan kinerja sales harian/mingguan (Should Have) | 8 | BE + FE | Planned |
| — | Integration testing & bug fixes | — | QA | Planned |

**Goals Sprint 5:**
- [ ] Sales Manager dapat memonitor seluruh aktivitas sales secara real-time
- [ ] Laporan kinerja dapat di-export
- [ ] Semua modul terintegrasi dan berjalan di staging

**Deliverables:**
- [ ] Web dashboard: Peta lokasi sales, daftar kunjungan hari ini, statistik order
- [ ] Laporan harian/mingguan dalam format yang dapat di-export
- [ ] Integration test report

---

### Sprint 6: QA & UAT
**Durasi:** 2026-07-16 – 2026-07-29

| # | Aktivitas | Owner | Status |
|---|-----------|-------|--------|
| 1 | Full regression testing di staging | QA | Planned |
| 2 | Performance testing (load & response time) | QA + Tech Lead | Planned |
| 3 | Security testing (autentikasi, HTTPS, data enkripsi) | Tech Lead | Planned |
| 4 | UAT session dengan Sales Manager & Head of Sales | PM + Klien | Planned |
| 5 | Bug fixes berdasarkan hasil UAT | Dev | Planned |
| 6 | Persiapan production deployment (checklist go-live) | Tech Lead | Planned |

**Goals Sprint 6:**
- [ ] Zero critical & high bugs open
- [ ] UAT sign-off dari klien
- [ ] Checklist go-live selesai divalidasi

---

### Deployment & Training (Week 15)
**Durasi:** 2026-07-30 – 2026-08-05

| # | Aktivitas | Owner | Status |
|---|-----------|-------|--------|
| 1 | Production deployment | Tech Lead | Planned |
| 2 | Smoke testing di production | QA | Planned |
| 3 | Training Sales Representative | PM | Planned |
| 4 | Training Sales Manager & Head of Sales (dashboard) | PM | Planned |
| 5 | Handover dokumentasi teknis | Tech Lead | Planned |
| 6 | Project closure & sign-off | PM | Planned |

---

## 6. Communication Plan

### 6.1 Rapat Rutin

| Rapat | Frekuensi | Peserta | Waktu | Output |
|-------|-----------|---------|-------|--------|
| Daily Standup | Harian (Senin–Jumat) | Tim Dev | 09:00 WIB | Status update, blocker |
| Sprint Planning | Per sprint (hari Kamis W1) | Semua tim | Sprint pertama | Sprint backlog |
| Sprint Review & Demo | Per sprint (hari Rabu W2) | Semua tim + Klien | Sprint terakhir | Demo, feedback |
| Sprint Retrospective | Per sprint (hari Rabu W2) | Tim Dev | Setelah Review | Action items |
| Weekly Status Report | Mingguan | PM + Klien | Jumat | Laporan progress |
| Milestone Review | Per milestone | PM + Klien | Sesuai jadwal | Persetujuan milestone |

### 6.2 Channel Komunikasi

| Tujuan | Channel | Peserta |
|--------|---------|---------|
| Komunikasi harian tim | Slack | Semua tim |
| Komunikasi formal dengan klien | Email | PM, Sponsor |
| Issue & task tracking | Taiga | Tim Dev |
| Dokumentasi | Git Repository | Semua tim |
| Demo & meeting | Video call (Zoom/Meet) | Sesuai undangan |

---

## 7. Manajemen Risiko

### 7.1 Risk Register

| ID | Risiko | Prob | Dampak | Skor | Mitigasi | Owner | Status |
|----|--------|:---:|:---:|:---:|----------|-------|--------|
| R01 | Akurasi GPS rendah di area sinyal lemah/dalam gedung | T | T | 9 | Toleransi radius GPS yang dapat dikonfigurasi; fallback input manual dengan approval manager | Tech Lead | Open |
| R02 | Resistensi adopsi oleh Sales Representative | T | T | 9 | Onboarding terstruktur; desain UX minimal friction; champion user dari klien | PM | Open |
| R03 | Koneksi internet tidak stabil di area lapangan | S | S | 4 | Mode offline terbatas dengan sinkronisasi saat koneksi kembali | Tech Lead | Open |
| R04 | Data outlet tidak lengkap atau tidak akurat | S | S | 4 | Validasi & pembersihan data outlet sebelum go-live; fitur update outlet oleh Sales Rep | PM | Open |
| R05 | Keterlambatan persetujuan desain dari klien | S | T | 6 | Timeline review desain ditetapkan di awal; max 3 hari revisi per iterasi | PM | Open |
| R06 | Integrasi Google Maps API melebihi quota/biaya | R | S | 2 | Estimasi penggunaan API sejak awal; implementasi caching untuk mengurangi hit rate | Tech Lead | Open |

*Prob/Dampak: R=Rendah(1), S=Sedang(2), T=Tinggi(3) | Skor = Prob × Dampak*

### 7.2 Dependencies

| ID | Dependency | Owner | Due Date | Status | Dampak jika Terlambat |
|----|------------|-------|----------|--------|----------------------|
| D01 | Data outlet (nama, alamat, koordinat GPS) dari klien | Head of Sales | 2026-05-06 | Pending | Menghalangi testing FEAT-001 di Sprint 2 |
| D02 | Akses Google Maps API key (production) | Tech Lead | 2026-05-06 | Pending | Menghalangi development EPIC-001 |
| D03 | Persetujuan UI/UX design dari klien | Head of Sales | 2026-05-20 | Pending | Sprint 2 tidak dapat dimulai tanpa design approval |
| D04 | Setup server production | Tech Lead | 2026-07-15 | Pending | Menghalangi deployment di Week 15 |

---

## 8. Quality Assurance

### 8.1 Definition of Done

**Level Story:**
- [ ] Code selesai dan di-commit ke branch feature
- [ ] Unit test ditulis dan pass
- [ ] Code review oleh Tech Lead
- [ ] Deploy ke staging berhasil
- [ ] QA test pass di staging
- [ ] Tidak ada critical/high bug yang open

**Level Sprint:**
- [ ] Semua story dalam sprint berstatus Done
- [ ] Sprint demo dilakukan dan disetujui
- [ ] Tidak ada critical bug yang open
- [ ] Dokumentasi fitur diperbarui

**Level Go-Live:**
- [ ] UAT sign-off dari klien
- [ ] Performance test pass (load time < 3 detik, API < 500ms)
- [ ] Security test pass (HTTPS, autentikasi, enkripsi)
- [ ] Training selesai dilakukan

### 8.2 Testing Strategy

| Tipe Testing | Pelaksana | Kapan | Tools |
|-------------|-----------|-------|-------|
| Unit Test | Developer | Selama development | Jest (FE), Pytest (BE) |
| Integration Test | Developer | Setelah story selesai | Playwright |
| E2E Test | QA | Setelah deploy ke staging | Playwright |
| Performance Test | QA + Tech Lead | Sprint 6 | k6 / Locust |
| Security Test | Tech Lead | Sprint 6 | Manual + OWASP checklist |
| UAT | Klien (Sales Manager, Head of Sales) | Sprint 6 | Manual |

---

## 9. Deliverables Tracker

| # | Deliverable | Owner | Target Tanggal | Status |
|---|-------------|-------|----------------|--------|
| 1 | BRD Disetujui | PM | 2026-04-30 | |
| 2 | ITA Final | Tech Lead | 2026-05-20 | |
| 3 | UI/UX Design Disetujui | Designer | 2026-05-20 | |
| 4 | Environment Setup Selesai | Tech Lead | 2026-05-06 | |
| 5 | MVP (semua fitur Must Have di staging) | Dev Team | 2026-07-01 | |
| 6 | Full QA Report | QA | 2026-07-22 | |
| 7 | UAT Sign-off | Klien | 2026-07-29 | |
| 8 | Production Deployment | Tech Lead | 2026-07-30 | |
| 9 | User Training Selesai | PM | 2026-08-04 | |
| 10 | Handover Dokumentasi | Tech Lead | 2026-08-05 | |
| 11 | Project Closure Sign-off | PM | 2026-08-05 | |

---

## 10. Persetujuan (Approval)

| Peran | Nama | Tanda Tangan | Tanggal |
|-------|------|--------------|---------|
| Product Manager | TBD | | |
| Tech Lead | TBD | | |
| Executive Sponsor | TBD | | |

---

<!--
LIVING DOCUMENT:
- Update status sprint di setiap sprint review
- Update risk register setiap minggu
- Update deliverables tracker di setiap milestone
- Catat semua change request di docs/project/change-requests/
-->
