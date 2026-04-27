# DESIGN SPECIFICATION
## [FEAT-001] Location Check-In/Out

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-001 |
| Feature Name | Location Check-In/Out |
| Epic | EPIC-003: Visit Verification |
| Status | Draft |
| Owner | Arina Saffanah Zakiyyah |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Figma Link | - |

---

## 1. Overview

### 1.1 Design Goals

- Mobile-first interface untuk field worker yang melakukan kunjungan lapangan
- Informasi lokasi real-time yang jelas dan akurat
- Feedback visual yang immediate untuk setiap aksi check-in/out
- Error states yang informatif dengan actionable next steps

### 1.2 Reference Documents

| Document | Location |
|----------|----------|
| Business Spec | `01-location-checkin---business.md` |
| Testing Spec | `01-location-checkin---testing.md` |

---

## 2. User Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    CHECK-IN/OUT USER FLOW                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────┐      ┌─────────────┐      ┌─────────────┐         │
│  │  Start  │─────▶│  Open App   │─────▶│  Dashboard  │         │
│  │         │      │             │      │             │         │
│  └─────────┘      └──────┬──────┘      └──────┬──────┘         │
│                          │                    │                 │
│                          │                    │ [Tap Check-In]  │
│                          │                    ▼                 │
│                          │            ┌─────────────┐           │
│                          │            │ GPS Scan    │           │
│                          │            │ (Loading)   │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ├─[Valid]          │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Confirm     │           │
│                          │            │ Location    │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ├─[Confirm]        │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Check-In    │           │
│                          │            │ Success     │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Visit       │           │
│                          │            │ In Progress │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   │ [Tap Check-Out]  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Check-Out   │           │
│                          │            │ Success     │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Visit       │           │
│                          │            │ Completed   │           │
│                          │            └─────────────┘           │
│                                                                  │
│  Error Branch:                                                   │
│  ┌─────────────┐      ┌─────────────┐      ┌─────────────┐    │
│  │ GPS Invalid │─────▶│ Error Msg   │─────▶│ Retry /     │    │
│  │             │      │ + Guidance  │      │ Cancel      │    │
│  └─────────────┘      └─────────────┘      └─────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. Wireframe

### 3.1 Mobile - Dashboard (Before Check-In)

```
+----------------------------------------------------------+
|  [ ≡ ]  RouteGo                    [🔔 3]  [👤 Arina] |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  👋 Selamat Pagi, Arina!                           │  |
|  │                                                     │  |
|  │  Kunjungan hari ini: 5 lokasi                     │  |
|  │  Sudah dikunjungi: 0/5                             │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  📍 LOKASI KUNJUNGAN HARI INI                             |
|  ─────────────────────────────────                       |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  1.  PT. Maju Jaya                        [PENDING] │  |
|  │      📍 Jl. Sudirman No. 123, Jakarta              │  |
|  │      🕒 Jadwal: 09:00 - 10:30                       │  |
|  │      📏 0.5 km dari lokasi saat ini                 │  |
|  │                                                     │  |
|  │      [  📍 CHECK-IN SEKARANG  ]                    │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  2.  CV. Berkah Abadi                   [PENDING]   │  |
|  │      📍 Jl. Thamrin No. 45, Jakarta                │  |
|  │      🕒 Jadwal: 11:00 - 12:30                       │  |
|  │      📏 2.3 km dari lokasi saat ini                 │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  3.  Toko Sentosa Electronics            [PENDING]   │  |
|  │      📍 Jl. Gatot Subroto No. 78, Jakarta          │  |
|  │      🕒 Jadwal: 14:00 - 15:30                       │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
+----------------------------------------------------------+
|  [🏠 Home]  [📋 Jadwal]  [📍 Visit]  [👤 Profil]         |
+----------------------------------------------------------+
```

### 3.2 Mobile - Check-In Confirmation

```
+----------------------------------------------------------+
|  [ ← ]  CHECK-IN KUNJUNGAN                   [ ✕ ]        |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  |
|  │              📍 KONFIRMASI LOKASI                   │  |
|  │                                                     │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  PT. Maju Jaya                                            |
|  Jl. Sudirman No. 123, Jakarta Selatan                   |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📍 LOKASI ANDA SAAT INI                           │  |
|  │  ───────────────────────────                       │  |
|  │                                                     │  |
|  │  Latitude:  -6.2088                                │  |
|  │  Longitude: 106.8456                               │  |
|  │  Akurasi:    ±10 meter                             │  |
|  │                                                     │  |
|  │  📏 Jarak dari target: 15 meter                    │  |
|  │  ✅ Lokasi VALID (dalam radius geofence)           │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📸 FOTO LOKASI (WAJIB)                            │  |
|  │  ─────────────────────────                         │  |
|  │                                                     │  |
|  │         [ 📷 Ambil Foto Lokasi ]                   │  |
|  │                                                     │  |
|  │  Foto akan diverifikasi berdasarkan:                │  |
|  │  • Koordinat GPS                                   │  |
|  │  • Timestamp                                       │  |
|  │  • Kualitas gambar                                 │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📝 CATATAN KUNJUNGAN (OPSIONAL)                   │  |
|  │  ────────────────────────────────                  │  |
|  │  +---------------------------------------------+   │  |
|  │  | Tulis catatan kunjungan...               |   │  |
|  │  +---------------------------------------------+   │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  BATAL  ]                    [  CHECK-IN  ✅  ]    |
|                                                           |
+----------------------------------------------------------+
```

### 3.3 Mobile - Check-In Success

```
+----------------------------------------------------------+
|                                                           |
|                                                           |
|              ┌─────────────────────┐                     |
|              │                     │                     |
|              │       ✅            │                     |
|              │                     │                     |
|              │  CHECK-IN BERHASIL  │                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|           Kunjungan Anda telah dicatat                   │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📍 PT. Maju Jaya                                   │  |
|  │  🕒 Check-in: 09:05 WIB                             │  |
|  │  👤 Arina Saffanah Zakiyyah                         │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|           Kunjungan berlangsung...                       │
|      Jangan lupa check-out saat selesai                  │
|                                                           |
|      [  🏠 KEMBALI KE DASHBOARD  ]                       │
|                                                           |
+----------------------------------------------------------+
```

### 3.4 Mobile - Visit In Progress (Ready for Check-Out)

```
+----------------------------------------------------------+
|  [ ≡ ]  RouteGo                    [🔔 3]  [👤 Arina] |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📍 KUNJUNGAN SEDANG BERLANGSUNG                   │  |
|  │  ─────────────────────────────────                 │  |
|  │                                                     │  |
|  │  PT. Maju Jaya                                      │  |
|  │  Jl. Sudirman No. 123, Jakarta Selatan             │  |
|  │                                                     │  |
|  │  Check-in:  09:05 WIB                               │  |
|  │  Durasi:    45 menit                                │  |
|  │  Status:    ✅ Lokasi Valid                         │  |
|  │                                                     │  |
|  │  ┌─────────────────────────────────────────────┐  │  |
|  │  │  🖼️ Foto Check-In                         │  │  |
|  │  │  [ Thumbnail foto lokasi ]                  │  │  |
|  │  └─────────────────────────────────────────────┘  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📝 CATATAN KUNJUNGAN                              │  |
|  │  ──────────────────────                           │  |
|  │  Menunggu pembaharuan produk...                    │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📸 FOTO KUNJUNGAN TAMBAHAN (OPSIONAL)             │  │  |
|  │  ─────────────────────────────────────             │  │  |
|  │  [+ Tambah Foto]                                   │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [   🚪 CHECK-OUT DARI LOKASI INI   ✅   ]          |
|                                                           |
+----------------------------------------------------------+
```

### 3.5 Mobile - Check-Out Confirmation

```
+----------------------------------------------------------+
|  [ ← ]  CHECK-OUT KUNJUNGAN                  [ ✕ ]        |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  |
|  │              🚪 KONFIRMASI CHECK-OUT               │  |
|  │                                                     │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  PT. Maju Jaya                                            |
|  Jl. Sudirman No. 123, Jakarta Selatan                   |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📊 RINGKASAN KUNJUNGAN                            │  |
|  │  ───────────────────────────                       │  |
|  │                                                     │  |
|  │  Check-in:   09:05 WIB                             │  |
|  │  Check-out:  10:15 WIB                             │  |
|  │  Durasi:     1 jam 10 menit                        │  |
|  │  Lokasi:     ✅ Valid                              │  |
|  │  Foto:       2 foto terlampir                     │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📸 FOTO CHECK-OUT (WAJIB)                        │  │  |
|  │  ───────────────────────────                       │  │  |
|  │                                                     │  │  |
|  │         [ 📷 Ambil Foto Check-Out ]                │  │  |
|  │                                                     │  │  |
|  │  Foto terakhir sebelum meninggalkan lokasi         │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📝 CATATAN PENUTUP (OPSIONAL)                    │  │  |
|  │  ─────────────────────────────────                │  │  |
|  │  +---------------------------------------------+   │  │  |
|  │  | Hasil kunjungan, kesepakatan, dll...       |   │  │  |
|  │  +---------------------------------------------+   │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  BATAL  ]                    [  CHECK-OUT ✅  ]    |
|                                                           |
+----------------------------------------------------------+
```

### 3.6 Mobile - Check-Out Success

```
+----------------------------------------------------------+
|                                                           |
|                                                           |
|              ┌─────────────────────┐                     |
|              │                     │                     |
|              │       ✅            │                     |
|              │                     │                     |
|              │  CHECK-OUT BERHASIL │                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|           Kunjungan telah selesai                        │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📍 PT. Maju Jaya                                   │  |
|  │  🕒 Check-in:  09:05 WIB                            │  |
|  │  🕒 Check-out: 10:15 WIB                            │  |
|  │  ⏱️ Durasi:    1 jam 10 menit                      │  |
|  │  👤 Arina Saffanah Zakiyyah                         │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|           Kunjungan selanjutnya:                         │
|      CV. Berkah Abadi - 11:00 WIB                        │
|                                                           |
|      [  🏠 KEMBALI KE DASHBOARD  ]                       │
|                                                           |
+----------------------------------------------------------+
```

### 3.7 Mobile - GPS Error State

```
+----------------------------------------------------------+
|  [ ≡ ]  RouteGo                    [🔔 3]  [👤 Arina] |
+----------------------------------------------------------+
|                                                           |
|              ┌─────────────────────┐                     |
|              │       ⚠️            │                     |
|              │                     │                     |
|              │  GPS TIDAK TERDETEKSI│                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|      Lokasi Anda tidak dapat ditemukan.                  │
|      Pastikan GPS telah diaktifkan.                       │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  💡 TIPS UNTUK AKURASI GPS                         │  │  |
|  │  ─────────────────────────────                     │  │  |
|  │  • Aktifkan Location Services                      │  │  |
|  │  • Pilih mode "High Accuracy"                       │  │  |
|  │  • Buka area terbuka                               │  │  |
|  │  • Matikan power saving mode                       │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🔧 PENGATURAN GPS                                 │  │  |
|  │  ────────────────────                              │  │  |
|  │  Status:      ❌ Off                                │  │  |
|  │  Akurasi:    -                                     │  │  |
|  │  Satelit:    0 terdeteksi                          │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  ❌ BATAL  ]              [  🔄 COBA LAGI  ]      |
|                                                           |
+----------------------------------------------------------+
```

### 3.8 Mobile - Location Invalid Error

```
+----------------------------------------------------------+
|  [ ← ]  CHECK-IN KUNJUNGAN                   [ ✕ ]        |
+----------------------------------------------------------+
|                                                           |
|              ┌─────────────────────┐                     |
|              │       ⚠️            │                     |
|              │                     │                     |
|              │  LOKASI TIDAK VALID │                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|      Anda berada di luar radius geofence yang diizinkan. │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📍 LOKASI SAAT INI                               │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  Latitude:  -6.2088                                │  │  |
|  │  Longitude: 106.8456                               │  │  |
|  │                                                     │  │  |
|  │  📏 Jarak dari target: 350 meter                   │  │  |
|  │  ❌ TIDAK VALID (di luar radius 100 meter)         │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🎯 LOKASI YANG DIMINTA                           │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  PT. Maju Jaya                                      │  │  |
|  │  Jl. Sudirman No. 123, Jakarta Selatan             │  │  |
|  │  Radius geofence: 100 meter                        │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      Apakah Anda berada di lokasi yang benar?            │
|                                                           |
|      [  ❌ BATAL  ]              [  🔄 DETEKSI ULANG  ]   |
|                                                           |
+----------------------------------------------------------+
```

### 3.9 Mobile - Visit History

```
+----------------------------------------------------------+
|  [ ← ]  RIWAYAT KUNJUNGAN                                |
+----------------------------------------------------------+
|                                                           |
|  🔍 [ Cari kunjungan...            ]                     |
|                                                           |
|  📅 Filter: [ Bulan Ini ▼ ]  [ Status ▼ ]                |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🟢 VALID                             23 Apr 2025   │  |
|  │  ─────────────────────────────────────────────────  │  |
|  │  PT. Maju Jaya                                      │  |
|  │  🕒 09:05 - 10:15 (1j 10m)                          │  |
|  │  📍 Jl. Sudirman No. 123, Jakarta                   │  |
|  │  📸 2 foto  📝 Ada catatan                          │  |
|  │                                                     │  |
|  │                                    [ Lihat Detail →]│  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🟢 VALID                             22 Apr 2025   │  |
|  │  ─────────────────────────────────────────────────  │  │  |
|  │  CV. Berkah Abadi                                   │  │  |
|  │  🕒 14:00 - 15:30 (1j 30m)                          │  │  |
|  │  📍 Jl. Thamrin No. 45, Jakarta                    │  │  |
|  │  📸 3 foto                                          │  │  |
|  │                                                     │  │  |
|  │                                    [ Lihat Detail →]│  │  |
|  └────────────────────────────────────────────────────┘  │  |
|                                                           │  |
|  ┌────────────────────────────────────────────────────┐  │  |
|  │  ⚠️ FLAGGED                          21 Apr 2025   │  │  |
|  │  ─────────────────────────────────────────────────  │  │  |
|  │  Toko Sentosa Electronics                          │  │  |
|  │  🕒 10:00 - 11:00 (1j 0m)                           │  │  |
|  │  📍 Jl. Gatot Subroto No. 78, Jakarta              │  │  |
|  │  ⚠️ GPS invalid (150 meter dari target)            │  │  |
|  │  📸 2 foto  👀 Perlu review supervisor             │  │  |
|  │                                                     │  │  |
|  │                                    [ Lihat Detail →]│  │  |
|  └────────────────────────────────────────────────────┘  │  |
|                                                           │  |
+----------------------------------------------------------+
```

### 3.10 Mobile - Visit Detail

```
+----------------------------------------------------------+
|  [ ← ]  DETAIL KUNJUNGAN                                 |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🟢 VALID                                           │  │  |
|  │                                                     │  │  |
|  │  PT. Maju Jaya                                      │  │  |
|  │  Jl. Sudirman No. 123, Jakarta Selatan             │  │  |
|  │                                                     │  │  |
|  │  🕒 Check-in:  09:05 WIB                            │  │  |
|  │  🕒 Check-out: 10:15 WIB                            │  │  |
|  │  ⏱️ Durasi:    1 jam 10 menit                      │  │  |
|  │  👤 Arina Saffanah Zakiyyah                         │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📍 INFORMASI LOKASI                               │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  Latitude:  -6.2088                                │  │  |
|  │  Longitude: 106.8456                               │  │  |
|  │  Akurasi:   ±10 meter                              │  │  |
|  │  Jarak:     15 meter dari target                  │  │  |
|  │  Status:    ✅ Valid                               │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📸 FOTO KUNJUNGAN (2)                             │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  ┌─────────┐  ┌─────────┐                          │  │  |
|  │  │   1    │  │   2    │                          │  │  |
|  │  │ [Foto] │  │ [Foto] │                          │  │  |
|  │  │ Check-in│  │Check-out│                          │  │  |
|  │  └─────────┘  └─────────┘                          │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📝 CATATAN                                        │  │  |
|  │  ────────────────────                              │  │  |
|  │  Menunggu pembaharuan produk musim ini.            │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
+----------------------------------------------------------+
```

---

## 4. Component Specifications

### 4.1 Check-In Button

**Properties:**

| Property | Value |
|----------|-------|
| Width | 100% |
| Height | 48px |
| Padding | 16px |
| Border Radius | 8px |
| Background | var(--color-primary-500) |
| Text Color | white |
| Font Size | 16px |
| Font Weight | 600 |

**States:**

| State | Background | Icon | Text |
|-------|------------|------|------|
| Default | primary-500 | 📍 | CHECK-IN SEKARANG |
| Hover | primary-600 | 📍 | CHECK-IN SEKARANG |
| Disabled | gray-300 | 📍 | CHECK-IN SEKARANG |
| Loading | primary-500 | ⏳ | MEMPROSES... |

### 4.2 Location Card

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

| State | Border | Badge |
|-------|--------|-------|
| Pending | gray-200 | [PENDING] |
| In Progress | primary-500 | [IN PROGRESS] |
| Completed | green-500 | [✅ COMPLETED] |
| Flagged | orange-500 | [⚠️ FLAGGED] |

### 4.3 Status Badge

| Status | Background | Text | Icon |
|--------|------------|------|------|
| Valid | green-100 | green-700 | 🟢 |
| Flagged | orange-100 | orange-700 | ⚠️ |
| Invalid | red-100 | red-700 | ❌ |
| Pending | gray-100 | gray-700 | ⏳ |

---

## 5. Responsive Behavior

| Breakpoint | Width | Layout | Notes |
|------------|-------|--------|-------|
| Mobile | < 768px | Full width, stacked | Primary target device |
| Tablet | 768-1024px | 2 columns | Side-by-side cards |
| Desktop | > 1024px | Dashboard view | For supervisor/manager |

---

## 6. Interactions & Animations

| Element | Trigger | Animation | Duration | Easing |
|---------|---------|-----------|----------|--------|
| Check-In Button | Tap | Scale 0.95 | 100ms | ease-out |
| GPS Scan | Start | Rotate icon | 1s | linear |
| Success Modal | Appear | Fade + scale up | 300ms | ease-out |
| Error State | Appear | Shake | 400ms | ease-in-out |
| Location Card | Swipe | Slide left | 200ms | ease |

---

## 7. Design Tokens Used

### Colors

| Token | Usage |
|-------|-------|
| --color-primary-500 | Primary actions, check-in button |
| --color-green-500 | Success state, valid location |
| --color-orange-500 | Flagged state |
| --color-red-500 | Error state, invalid location |
| --color-gray-900 | Primary text |
| --color-gray-600 | Secondary text |

### Typography

| Element | Token |
|---------|-------|
| Page title | --text-heading-1 (24px) |
| Card title | --text-heading-3 (18px) |
| Body text | --text-body (14px) |
| Caption | --text-caption (12px) |

---

## 8. Accessibility

- [ ] Touch targets minimum 48x48px
- [ ] Color contrast ratio minimum 4.5:1
- [ ] Screen reader announces GPS status
- [ ] Error messages have clear guidance
- [ ] Loading states announced

---

## 9. Empty & Error States

### Empty History State

```
+----------------------------------------------------------+
|                                                           |
|              [ 📋 Illustration ]                         |
|                                                           |
|           Belum ada riwayat kunjungan                   │
|                                                           |
|      Kunjungan Anda akan muncul di sini                  │
|                                                           |
+----------------------------------------------------------+
```

---

## 10. Assets Required

| Asset | Type | Status | Notes |
|-------|------|--------|-------|
| Location icon | SVG | Needed | 24x24px |
| Check-in icon | SVG | Needed | 24x24px |
| GPS error illustration | SVG | Needed | Empty state |
| Success illustration | SVG | Needed | Success modal |

---

## 11. Design QA Checklist

- [ ] GPS scan animation smooth
- [ ] Location distance accurate
- [ ] Error states actionable
- [ ] Success states clear
- [ ] Responsive on mobile devices
- [ ] Touch targets adequate size

---

## 12. Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Designer | | | Pending |
| PM | | | Pending |
| Tech Lead | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-visit-verification/01-location-checkin---design.md

RELATED FILES:
- 01-location-checkin---business.md (Requirements)
- 01-location-checkin---testing.md (Test scenarios)
- 01-location-checkin---technical.md (Implementation)
-->
