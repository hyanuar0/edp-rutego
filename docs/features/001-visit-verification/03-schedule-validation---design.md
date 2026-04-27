# DESIGN SPECIFICATION
## [FEAT-003] Schedule & Route Validation

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-003 |
| Feature Name | Schedule & Route Validation |
| Epic | EPIC-003: Visit Verification |
| Status | Draft |
| Owner | Arina Saffanah Zakiyyah |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Figma Link | - |

---

## 1. Overview

### 1.1 Design Goals

- Visualisasi rute yang jelas dengan peta interaktif
- Informasi jadwal yang mudah dipahami dengan progress indicator
- Notifikasi penyimpangan yang actionable
- Dashboard supervisor untuk monitoring kepatuhan

### 1.2 Reference Documents

| Document | Location |
|----------|----------|
| Business Spec | `03-schedule-validation---business.md` |
| Testing Spec | `03-schedule-validation---testing.md` |

---

## 2. User Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                   SCHEDULE & ROUTE USER FLOW                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────┐      ┌─────────────┐      ┌─────────────┐         │
│  │  Start  │─────▶│  Open App   │─────▶│  Schedule   │         │
│  │         │      │             │      │  Dashboard  │         │
│  └─────────┘      └──────┬──────┘      └──────┬──────┘         │
│                          │                    │                 │
│                          │                    │ [View Today]    │
│                          │                    ▼                 │
│                          │            ┌─────────────┐           │
│                          │            │ View Route  │           │
│                          │            │ & Map       │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ├─[Follow Route]   │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Navigate    │           │
│                          │            │ to Location │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ├─[Check-In]      │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Visit       │           │
│                          │            │ Completed   │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ├─[Next Location]  │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Continue    │           │
│                          │            │ Route       │           │
│                          │            └─────────────┘           │
│                                                                  │
│  Deviation Branch:                                              │
│  ┌─────────────┐      ┌─────────────┐      ┌─────────────┐    │
│  │ Route       │─────▶│ Deviation   │─────▶│ Notify      │    │
│  │ Deviation   │      │ Detected    │      │ Supervisor  │    │
│  └─────────────┘      └─────────────┘      └─────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. Wireframe

### 3.1 Mobile - Worker Schedule Dashboard

```
+----------------------------------------------------------+
|  [ ≡ ]  RouteGo                    [🔔 3]  [👤 Arina] |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  👋 Selamat Pagi, Arina!                           │  │  |
|  │                                                     │  │  |
|  │  Kunjungan hari ini: 5 lokasi                     │  │  |
|  │  Estimasi selesai: 17:30 WIB                      │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  📍 JADWAL & RUTE HARI INI                                |
|  ─────────────────────────────────                       |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🗓️ 23 APRIL 2026                                  │  │  |
|  │                                                     │  │  |
|  │  📊 RINGKASAN RUTE                                 │  │  |
|  │  • Total jarak: 45.2 km                            │  │  |
|  │  • Estimasi waktu: 6 jam 30 menit                 │  │  |
|  │  • Kunjungan: 5 lokasi                            │  │  |
|  │  • Status: ✅ Sesuai jadwal                       │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📍 RUTE KUNJUNGAN (URUTAN OPTIMAL)                │  │  |
|  │  ──────────────────────────────────────            │  │  |
|  │                                                     │  │  |
|  │  1.  PT. Maju Jaya                        [09:00]  │  │  |
|  │      └─ 0.5 km dari lokasi saat ini               │  │  |
|  │                                                     │  │  |
|  │  2.  CV. Berkah Abadi                     [11:00]  │  │  |
|  │      └─ 3.2 km dari lokasi #1                     │  │  |
|  │                                                     │  │  |
|  │  3.  Toko Sentosa Electronics             [14:00]  │  │  |
|  │      └─ 2.8 km dari lokasi #2                     │  │  |
|  │                                                     │  │  |
|  │  4.  PT. Global Trade                     [15:30]  │  │  |
|  │      └─ 1.5 km dari lokasi #3                     │  │  |
|  │                                                     │  │  |
|  │  5.  CV. Mitra Sejahtera                   [17:00]  │  │  |
|  │      └─ 2.1 km dari lokasi #4                     │  │  |
|  │                                                     │  │  |
|  │                    [ 🗺️ LIHAT PETA RUTE ]         │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
+----------------------------------------------------------+
|  [🏠 Home]  [📋 Jadwal]  [📍 Visit]  [👤 Profil]         |
+----------------------------------------------------------+
```

### 3.2 Mobile - Route Map View

```
+----------------------------------------------------------+
|  [ ← ]  PETA RUTE KUNJUNGAN                    [ 🔄 ]     |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  │  |
|  │              [ INTERACTIVE MAP ]                    │  │  |
|  │                                                     │  │  |
|  │         1 ──────▶ 2 ──────▶ 3                       │  │  |
|  │         │                │                         │  │  |
|  │         │                └─────▶ 4 ──────▶ 5       │  │  |
|  │                                                     │  │  |
|  │  📍 Lokasi saat ini                                │  │  |
|  │  🟢 Lokasi yang sudah dikunjungi                   │  │  |
|  │  ⚪ Lokasi yang belum dikunjungi                   │  │  |
|  │                                                     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📊 INFORMASI RUTE                                 │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  Total jarak:     45.2 km                          │  │  |
|  │  Estimasi waktu:  6 jam 30 menit                   │  │  |
|  │  Progress:         0/5 lokasi selesai              │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📍 LOKASI BERIKUTNYA                              │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  1. PT. Maju Jaya                                  │  │  |
|  │  📍 Jl. Sudirman No. 123, Jakarta                 │  │  |
|  │  🕒 Jadwal: 09:00 - 10:30                         │  │  |
|  │  📏 0.5 km dari lokasi saat ini                   │  │  |
|  │                                                     │  │  |
|  │  [  🗺️ NAVIGASI  ]  [  📍 CHECK-IN  ]            │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
+----------------------------------------------------------+
```

### 3.3 Mobile - Route Progress (In Transit)

```
+----------------------------------------------------------+
|  [ ≡ ]  RouteGo                    [🔔 3]  [👤 Arina] |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🚗 DALAM PERJALANAN                               │  │  |
|  │  ───────────────────────────                       │  │  |
|  │                                                     │  │  |
|  │  Menuju lokasi #2: CV. Berkah Abadi                │  │  |
|  │  Estimasi tiba: 15 menit                           │  │  |
|  │  Jarak tersisa: 3.2 km                             │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  📍 PROGRESS RUTE                                         |
|  ─────────────────────────────────                       |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  │  |
|  │  1. ✅ PT. Maju Jaya                    [SELESAI]   │  │  |
|  │     Check-in: 09:05  Check-out: 10:15              │  │  |
|  │                                                     │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │             │                                      │  │  |
|  │             ▼ (3.2 km, 15 menit)                   │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │                                                     │  │  |
|  │  2. 🚗 CV. Berkah Abadi                  [DALAM]    │  │  |
|  │     Jadwal: 11:00 - 12:30                         │  │  |
|  │     Estimasi tiba: 10:45 (15 menit lebih awal)    │  │  |
|  │                                                     │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │             │                                      │  │  |
|  │             ▼ (2.8 km, 20 menit)                   │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │                                                     │  │  |
|  │  3. ⏳ Toko Sentosa Electronics             [11:00] │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │             │                                      │  │  |
|  │             ▼ (1.5 km, 15 menit)                   │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │                                                     │  │  |
|  │  4. ⏳ PT. Global Trade                     [15:30] │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │             │                                      │  │  |
|  │             ▼ (2.1 km, 20 menit)                   │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │                                                     │  │  |
|  │  5. ⏳ CV. Mitra Sejahtera                   [17:00] │  │  |
|  │                                                     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  🗺️ LIHAT PETA  ]      [  📱 RINGKASAN  ]        |
|                                                           |
+----------------------------------------------------------+
```

### 3.4 Mobile - Schedule Mismatch Alert

```
+----------------------------------------------------------+
|                                                           |
|              ┌─────────────────────┐                     |
|              │       ⚠️            │                     |
|              │                     │                     |
|              │  JADWAL TIDAK SESUAI│                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|      Anda melakukan check-in di luar jadwal yang         │
|      ditentukan.                                          │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📋 DETAIL JADWAL                                  │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  Lokasi:          CV. Berkah Abadi                 │  │  |
|  │  Jadwal:          Senin, 23 April 2026             │  │  |
|  │  Waktu jadwal:    11:00 - 12:30 WIB                │  │  |
|  │  Waktu check-in:  10:30 WIB                        │  │  |
|  │  Selisih:         30 menit lebih awal             │  │  |
|  │  Status:          ⚠️ Wrong Time                   │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  💡 INFORMASI                                      │  │  |
|  │  ─────────────────                                 │  │  |
|  │  Toleransi waktu check-in: ±30 menit dari jadwal.  │  │  |
|  │  Check-in Anda masih dalam toleransi.              │  │  |
|  │  Kunjungan akan ditandai tetapi tidak ditolak.     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  ⚠️ KONSEKUENSI                                     │  │  |
|  │  ─────────────────                                 │  │  |
|  │  Kunjungan ini akan ditandai sebagai "Schedule     │  │  |
|  │  Mismatch" dan akan direview oleh supervisor.      │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  BATAL  ]                    [  LANJUTKAN  ]       |
|                                                           |
+----------------------------------------------------------+
```

### 3.5 Mobile - Route Deviation Alert

```
+----------------------------------------------------------+
|                                                           |
|              ┌─────────────────────┐                     |
|              │       ⚠️            │                     |
|              │                     │                     |
|              │  PENYIMPANGAN RUTE  │                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|      Anda mengunjungi lokasi dalam urutan yang berbeda   │
|      dari rute yang ditentukan.                           │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📋 RUTE YANG DITUGASKAN                           │  │  |
|  │  ─────────────────────────────                     │  │  |
|  │  1. PT. Maju Jaya          ✅ (selesai)            │  │  |
|  │  2. CV. Berkah Abadi       ⏭️ (dilewati)           │  │  |
|  │  3. Toko Sentosa Electronics                      │  │  |
|  │  4. PT. Global Trade                               │  │  |
|  │  5. CV. Mitra Sejahtera                            │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📋 RUTE AKTUAL                                    │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  1. PT. Maju Jaya          ✅ (selesai)            │  │  |
|  │  3. Toko Sentosa Electronics 📍 (saat ini)        │  │  |
|  │                                                     │  │  |
|  │  Lokasi yang dilewati:                             │  │  |
|  │  • CV. Berkah Abadi (#2)                          │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📊 DAMPAK PENYIMPANGAN                            │  │  |
|  │  ─────────────────────────────                     │  │  |
|  │  Tambahan jarak:     +5.2 km                       │  │  |
|  │  Tambahan waktu:     +25 menit                    │  │  |
|  │  Penurunan efisiensi: 15%                          │  │  |
|  │  Status:             ⚠️ Melebihi toleransi        │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  ⚠️ KONSEKUENSI                                     │  │  |
|  │  ─────────────────                                 │  │  |
|  │  Penyimpangan ini akan diberitahu ke supervisor    │  │  |
|  │  dan akan dicatat dalam riwayat kunjungan.        │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  KEMBALI KE RUTE  ]       [  LANJUTKAN  ]         |
|                                                           |
+----------------------------------------------------------+
```

### 3.6 Web - Scheduler Route Creation

```
+------------------------------------------------------------------+
|  [ Logo ]                              [ Search ]  [ User ▼ ]    |
+------------------------------------------------------------------+
|                                                                   |
|  SIDEBAR          |  MAIN CONTENT                                |
|  ───────          |  ────────────                                |
|                   |                                               |
|  > Dashboard      |  📍 Breadcrumb: Schedule > Create Route       |
|  > Schedule ●     |                                               |
|  > Workers        |  ┌─────────────────────────────────────────┐ |
|  > Locations      |  │  PAGE HEADER                             │ |
|  > Reports        |  │  Buat Rute Kunjungan              [Save]│ |
|                   |  └─────────────────────────────────────────┘ |
|                   |                                               |
|                   |  ┌─────────────────────────────────────────┐ |
|                   |  │  FORM PEMBUATAN RUTE                     │ |
|                   |  │  ─────────────────────────────          │ |
|                   |  │                                         │ |
|                   |  │  Nama Rute:                              │ |
|                   |  │  +-----------------------------------+  │ |
|                   |  │  | Rute Arina - 23 April 2026       |  │ |
|                   |  │  +-----------------------------------+  │ |
|                   |  │                                         │ |
|                   |  │  Worker:                                 │ |
|                   |  │  +-----------------------------------+  │ |
|                   |  │  | Arina Saffanah Zakiyyah ▼         |  │ |
|                   |  │  +-----------------------------------+  │ |
|                   |  │                                         │ |
|                   |  │  Tanggal:                                │ |
|                   |  │  +-----------------------------------+  │ |
|                   |  │  | 23 April 2026                      |  │ |
|                   |  │  +-----------------------------------+  │ |
|                   |  │                                         │ |
|                   |  │  Lokasi Kunjungan:                       │ |
|                   |  │  [+ Tambah Lokasi]                       │ |
|                   |  │                                         │ |
|                   |  │  ┌───────────────────────────────────┐ │ |
|                   |  │  │ 1. PT. Maju Jaya         [Hapus] │ │ |
|                   |  │  │    Jl. Sudirman No. 123           │ │ |
|                   |  │  │    📍 -6.2088, 106.8456            │ │ |
|                   |  │  │    🕒 09:00 - 10:30                │ │ |
|                   |  │  └───────────────────────────────────┘ │ |
|                   |  │                                         │ |
|                   |  │  ┌───────────────────────────────────┐ │ |
|                   |  │  │ 2. CV. Berkah Abadi       [Hapus] │ │ |
|                   |  │  │    Jl. Thamrin No. 45              │ │ |
|                   |  │  │    📍 -6.1950, 106.8230            │ │ |
|                   |  │  │    🕒 11:00 - 12:30                │ │ |
|                   |  │  └───────────────────────────────────┘ │ |
|                   |  │                                         │ |
|                   |  │  [+ Tambah Lokasi]                       │ |
|                   |  │                                         │ |
|                   |  │  Algoritma Optimasi:                     │ |
|                   |  │  +-----------------------------------+  │ |
|                   |  │  | Traveling Salesman Problem (TSP)  │  │ |
|                   |  │  +-----------------------------------+  │ |
|                   |  │                                         │ |
|                   |  │  [  🔧 Generate Rute Optimal  ]           │ |
|                   |  └─────────────────────────────────────────┘ |
|                   |                                               |
+-------------------+-----------------------------------------------+
```

### 3.7 Web - Route Optimization Result

```
+------------------------------------------------------------------+
|  [ Logo ]                              [ Search ]  [ User ▼ ]    |
+------------------------------------------------------------------+
|                                                                   |
|  SIDEBAR          |  MAIN CONTENT                                |
|  ───────          |  ────────────                                |
|                   |                                               |
|  > Dashboard      |  📍 Breadcrumb: Schedule > Route Result       |
|  > Schedule ●     |                                               |
|  > Workers        |  ┌─────────────────────────────────────────┐ |
|  > Locations      |  │  HASIL OPTIMASI RUTE                     │ |
|  > Reports        |  │  ────────────────────────────            │ |
|                   |  │                                         │ |
|                   |  │  ✅ Rute optimal berhasil dibuat!        │ |
|                   |  └─────────────────────────────────────────┘ |
|                   |                                               |
|                   |  ┌─────────────────────────────────────────┐ |
|                   |  │  📊 STATISTIK RUTE                      │ |
|                   |  │  ────────────────────────               │ |
|                   |  │                                         │ |
|                   |  │  Total jarak:     45.2 km               │ |
|                   |  │  Total waktu:      6 jam 30 menit       │ |
|                   |  │  Waktu perjalanan: 2 jam 15 menit       │ |
|                   |  │  Waktu kunjungan: 4 jam 15 menit        │ |
|                   |  │  Efisiensi:       85%                   │ |
|                   |  │                                         │ |
|                   |  └─────────────────────────────────────────┘ |
|                   |                                               |
|                   |  ┌─────────────────────────────────────────┐ |
|                   |  │  🗺️ PETA RUTE                          │ |
|                   |  │  ────────────────────                   │ |
|                   |  │                                         │ |
|                   |  │         [ INTERACTIVE MAP ]              │ |
|                   |  │                                         │ |
|                   |  │     1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5            │ |
|                   |  │                                         │ |
|                   |  │  Jarak antar lokasi:                     │ |
|                   |  │  1 → 2: 3.2 km (15 menit)                │ |
|                   |  │  2 → 3: 2.8 km (20 menit)                │ |
|                   |  │  3 → 4: 1.5 km (15 menit)                │ |
|                   |  │  4 → 5: 2.1 km (20 menit)                │ |
|                   |  │                                         │ |
|                   |  └─────────────────────────────────────────┘ |
|                   |                                               |
|                   |  ┌─────────────────────────────────────────┐ |
|                   |  │  📍 URUTAN KUNJUNGAN                    │ |
|                   |  │  ────────────────────────               │ |
|                   |  │                                         │ |
|                   |  │  1. PT. Maju Jaya           [09:00]     │ |
|                   |  │  2. CV. Berkah Abadi        [11:00]     │ |
|                   |  │  3. Toko Sentosa Electronics [14:00]     │ |
|                   |  │  4. PT. Global Trade        [15:30]     │ |
|                   |  │  5. CV. Mitra Sejahtera     [17:00]     │ |
|                   |  │                                         │ |
|                   |  │  [Ubah Urutan Secara Manual]             │ |
|                   |  └─────────────────────────────────────────┘ |
|                   |                                               |
|                   |  [  Kembali  ]  [  Simpan & Tetapkan  ]      |
|                   |                                               |
+-------------------+-----------------------------------------------+
```

### 3.8 Web - Supervisor Deviation Dashboard

```
+------------------------------------------------------------------+
|  [ Logo ]                              [ Search ]  [ User ▼ ]    |
+------------------------------------------------------------------+
|                                                                   |
|  SIDEBAR          |  MAIN CONTENT                                |
|  ───────          |  ────────────                                |
|                   |                                               |
|  > Dashboard      |  📍 Breadcrumb: Monitor > Deviations          |
|  > Schedule ●     |                                               |
|  > Workers        |  ┌─────────────────────────────────────────┐ |
|  > Locations      |  │  MONITORING PENYIMPANGAN                  │ |
|  > Reports        |  │  ────────────────────────────            │ |
|                   |  │                                         │ |
|                   |  │  Filter: [Hari Ini ▼] [Worker ▼]         │ |
|                   |  │                                         │ |
|                   |  │  📊 STATISTIK HARI INI                   │ |
|                   |  │  • Total penyimpangan: 5                 │ |
|                   |  │  • Schedule mismatch: 3                  │ |
|                   |  │  • Route deviation: 2                    │ |
|                   |  │  • Perlu review: 2                       │ |
|                   |  └─────────────────────────────────────────┘ |
|                   |                                               |
|                   |  ┌─────────────────────────────────────────┐ |
|                   |  │  DAFTAR PENYIMPANGAN                     │ |
|                   |  │  ────────────────────────────            │ |
|                   |  │                                         │ |
|                   |  │  ┌─────────────────────────────────────┐│ |
|                   |  │  │ ⚠️ Route Deviation             [Detail]││ |
|                   |  │  │ ─────────────────────────────────── ││ |
|                   |  │  │ Arina Saffanah Zakiyyah               ││ |
|                   |  │  │ Toko Sentosa Electronics              ││ |
|                   |  │  │ 10:30 WIB                            ││ |
|                   |  │  │ Lokasi #2 dilewati                    ││ |
|                   |  │  │ +5.2 km, +25 menit                    ││ |
|                   |  │  │ [Review] [Ignore]                     ││ |
|                   |  │  └─────────────────────────────────────┘│ |
|                   |  │                                         │ |
|                   |  │  ┌─────────────────────────────────────┐│ |
|                   |  │  │ ⚠️ Schedule Mismatch            [Detail]││ |
|                   |  │  │ ─────────────────────────────────── ││ |
|                   |  │  │ Budi Santoso                         ││ |
|                   |  │  │ PT. Global Trade                     ││ |
|                   |  │  │ 14:00 WIB                            ││ |
|                   |  │  │ Check-in 15 menit terlambat          ││ |
|                   |  │  │ [Review] [Ignore]                     ││ |
|                   |  │  └─────────────────────────────────────┘│ |
|                   |  │                                         │ |
|                   |  │  ┌─────────────────────────────────────┐│ |
|                   |  │  │ ⚠️ Route Deviation             [Detail]││ |
|                   |  │  │ ─────────────────────────────────── ││ |
|                   |  │  │ Citra Dewi                          ││ |
|                   |  │  │ CV. Mitra Sejahtera                  ││ |
|                   |  │  │ 16:00 WIB                            ││ |
|                   |  │  │ Urutan berbeda dari rute             ││ |
|                   |  │  │ [Review] [Ignore]                     ││ |
|                   |  │  └─────────────────────────────────────┘│ |
|                   |  │                                         │ |
|                   |  │                    [Load More]           │ |
|                   |  └─────────────────────────────────────────┘ |
|                   |                                               |
+-------------------+-----------------------------------------------+
```

---

## 4. Component Specifications

### 4.1 Route Progress Card

**Properties:**

| Property | Value |
|----------|-------|
| Width | 100% |
| Padding | 16px |
| Border Radius | 12px |
| Background | white |
| Shadow | var(--shadow-sm) |
| Border | 1px solid var(--color-gray-200) |

**States:**

| State | Icon | Color |
|-------|------|-------|
| Completed | ✅ | green-500 |
| In Progress | 🚗 | primary-500 |
| Pending | ⏳ | gray-400 |
| Skipped | ⏭️ | orange-500 |

### 4.2 Map Marker

**Types:**

| Type | Icon | Color | Size |
|------|------|-------|------|
| Current Location | 📍 | primary-500 | Large |
| Completed | 🟢 | green-500 | Medium |
| Next | ⚪ | gray-400 | Medium |
| Pending | ⚪ | gray-300 | Small |

### 4.3 Deviation Alert Card

**Properties:**

| Property | Value |
|----------|-------|
| Width | 100% |
| Padding | 16px |
| Border Radius | 8px |
| Background | orange-50 |
| Border | 2px solid orange-200 |
| Border Left | 4px solid orange-500 |

**Severity Levels:**

| Level | Background | Border | Icon |
|-------|------------|--------|------|
| Low | orange-50 | orange-200 | ⚠️ |
| Medium | orange-100 | orange-300 | ⚠️ |
| High | red-50 | red-200 | ❌ |

---

## 5. Responsive Behavior

| Breakpoint | Width | Map View | Route List |
|------------|-------|----------|------------|
| Mobile | < 768px | Full screen | Stacked cards |
| Tablet | 768-1024px | 60% width | 2 columns |
| Desktop | > 1024px | Side panel | Full list |

---

## 6. Interactions & Animations

| Element | Trigger | Animation | Duration | Easing |
|---------|---------|-----------|----------|--------|
| Route Card | Tap | Expand details | 200ms | ease-out |
| Map Marker | Tap | Show tooltip | 150ms | ease |
| Deviation Alert | Swipe | Dismiss | 300ms | ease-in |
| Progress Update | Auto | Animate bar | 500ms | ease-out |
| Navigation Button | Tap | Scale 0.95 | 100ms | ease-out |

---

## 7. Design Tokens Used

### Colors

| Token | Usage |
|-------|-------|
| --color-primary-500 | Navigation, active states |
| --color-green-500 | Completed visits |
| --color-orange-500 | Deviation warnings |
| --color-red-500 | High severity alerts |
| --color-gray-900 | Primary text |
| --color-gray-600 | Secondary text |

### Typography

| Element | Token |
|---------|-------|
| Page title | --text-heading-1 (24px) |
| Section title | --text-heading-2 (20px) |
| Location name | --text-heading-3 (18px) |
| Route info | --text-body (14px) |
| Time | --text-caption (12px) |

---

## 8. Accessibility

- [ ] Map markers have aria-labels
- [ ] Route progress announced by screen reader
- [ ] Deviation alerts with clear guidance
- [ ] Touch targets minimum 48x48px
- [ ] Color contrast minimum 4.5:1

---

## 9. Empty & Error States

### No Schedule State

```
+----------------------------------------------------------+
|                                                           |
|              [ 📅 Illustration ]                         |
|                                                           |
|           Belum ada jadwal hari ini                      │
|                                                           |
|      Hubungi scheduler untuk mendapatkan jadwal          │
|                                                           |
+----------------------------------------------------------+
```

### Route Error State

```
+----------------------------------------------------------+
|                                                           |
|              [ ❌ Illustration ]                          |
|                                                           |
|           Gagal membuat rute optimal                     │
|                                                           |
|      Periksa lokasi dan coba lagi                        │
|                                                           |
|      [  🔄 COBA LAGI  ]                                  │
|                                                           |
+----------------------------------------------------------+
```

---

## 10. Assets Required

| Asset | Type | Status | Notes |
|-------|------|--------|-------|
| Map marker icons | SVG | Needed | Multiple colors |
| Route path line | SVG | Needed | Dashed/solid |
| Navigation icon | SVG | Needed | 24x24px |
| Empty schedule illustration | SVG | Needed | Empty state |

---

## 11. Design QA Checklist

- [ ] Map route visualization accurate
- [ ] Progress indicators clear
- [ ] Deviation alerts actionable
- [ ] Schedule mismatch notifications timely
- [ ] Mobile navigation smooth
- [ ] Route optimization result clear

---

## 12. Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Designer | | | Pending |
| PM | | | Pending |
| Tech Lead | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-visit-verification/03-schedule-validation---design.md

RELATED FILES:
- 03-schedule-validation---business.md (Requirements)
- 03-schedule-validation---testing.md (Test scenarios)
- 03-schedule-validation---technical.md (Implementation)
-->
