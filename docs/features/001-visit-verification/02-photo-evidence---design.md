# DESIGN SPECIFICATION
## [FEAT-002] Photo Evidence

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-002 |
| Feature Name | Photo Evidence |
| Epic | EPIC-003: Visit Verification |
| Status | Draft |
| Owner | Arina Saffanah Zakiyyah |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Figma Link | - |

---

## 1. Overview

### 1.1 Design Goals

- Camera-first interface untuk menghindari penggunaan galeri
- Validasi foto real-time dengan feedback visual yang jelas
- Watermark otomatis yang tidak mengganggu konten foto
- Gallery view yang informatif dengan metadata lengkap

### 1.2 Reference Documents

| Document | Location |
|----------|----------|
| Business Spec | `02-photo-evidence---business.md` |
| Testing Spec | `02-photo-evidence---testing.md` |

---

## 2. User Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                     PHOTO EVIDENCE USER FLOW                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────┐      ┌─────────────┐      ┌─────────────┐         │
│  │  Start  │─────▶│ Check-In    │─────▶│ Upload      │         │
│  │         │      │ Flow        │      │ Photo       │         │
│  └─────────┘      └──────┬──────┘      └──────┬──────┘         │
│                          │                    │                 │
│                          │                    │ [Tap Camera]    │
│                          │                    ▼                 │
│                          │            ┌─────────────┐           │
│                          │            │ Camera      │           │
│                          │            │ View        │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ├─[Take Photo]     │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ EXIF        │           │
│                          │            │ Validation  │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ├─[Valid]          │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Apply       │           │
│                          │            │ Watermark   │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Confirm     │           │
│                          │            │ Upload      │           │
│                          │            └──────┬──────┘           │
│                          │                   │                  │
│                          │                   ▼                  │
│                          │            ┌─────────────┐           │
│                          │            │ Photo       │           │
│                          │            │ Uploaded    │           │
│                          │            └─────────────┘           │
│                                                                  │
│  Error Branch:                                                   │
│  ┌─────────────┐      ┌─────────────┐      ┌─────────────┐    │
│  │ Gallery     │─────▶│ Error Alert │─────▶│ Retry /     │    │
│  │ Detected    │      │ + Guidance  │      │ Cancel      │    │
│  └─────────────┘      └─────────────┘      └─────────────┘    │
│                                                                  │
│  ┌─────────────┐      ┌─────────────┐      ┌─────────────┐    │
│  │ EXIF        │─────▶│ Suspicious  │─────▶│ Flag &      │    │
│  │ Invalid     │      │ Photo Alert │      │ Review      │    │
│  └─────────────┘      └─────────────┘      └─────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. Wireframe

### 3.1 Mobile - Photo Upload Prompt

```
+----------------------------------------------------------+
|  [ ← ]  CHECK-IN KUNJUNGAN                   [ ✕ ]        |
+----------------------------------------------------------+
|                                                           |
|  PT. Maju Jaya                                            |
|  Jl. Sudirman No. 123, Jakarta Selatan                   |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📸 FOTO LOKASI (WAJIB)                            │  │  |
|  │  ─────────────────────────                         │  │  |
|  │                                                     │  │  |
|  │         ┌─────────────────────────┐               │  │  |
|  │         │                         │               │  │  |
|  │         │      [ 📷 ]             │               │  │  |
|  │         │   Ambil Foto Lokasi     │               │  │  |
|  │         │                         │               │  │  |
|  │         └─────────────────────────┘               │  │  |
|  │                                                     │  │  |
|  │  📌 KETENTUAN FOTO:                                │  │  |
|  │  • Wajib gunakan kamera (tidak boleh galeri)      │  │  |
|  │  • Pastikan lokasi terlihat jelas                 │  │  |
|  │  • Foto akan diverifikasi berdasarkan GPS & EXIF  │  │  |
|  │  • Watermark otomatis akan ditambahkan            │  │  |
|  │                                                     │  │  |
|  │  Watermark preview:                                │  │  |
|  │  ┌─────────────────────────────────────┐          │  │  |
|  │  │ 📍 PT. Maju Jaya                    │          │  │  |
|  │  │ 🕒 23/04/2026 09:05                 │          │  │  |
|  │  │ 👤 Arina Saffanah Zakiyyah          │          │  │  |
|  │  │ 📍 -6.2088, 106.8456                │          │  │  |
|  │  └─────────────────────────────────────┘          │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
+----------------------------------------------------------+
```

### 3.2 Mobile - Camera View

```
+----------------------------------------------------------+
|  [ ✕ ]                                               [ 💡 ]|
+----------------------------------------------------------+
|                                                           |
|                                                           |
|                                                           |
|                                                           |
|                                                           |
|                                                           |
|                                                           |
|                  [ CAMERA VIEWFINDER ]                    |
|                                                           |
|                                                           |
|                                                           |
|                                                           |
|                                                           |
|                                                           |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📌 PASTIKAN LOKASI TERLIHAT JELAS                 │  │  |
|  │  ──────────────────────────────────────────────    │  │  |
|  │  • Tampak nama/alamat toko/kantor                  │  │  |
|  │  • Pencahayaan cukup                               │  │  |
|  │  • Hindari bayangan/gelap                          │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|           [ 🖼️ GALERI ]              [ 📸 JEPRET ]        |
|                                                           |
+----------------------------------------------------------+
```

### 3.3 Mobile - Gallery Detection Alert

```
+----------------------------------------------------------+
|                                                           |
|              ┌─────────────────────┐                     |
|              │       ⚠️            │                     |
|              │                     │                     |
|              │  DETEKSI GALERI     │                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|      Sistem mendeteksi Anda mencoba mengunggah foto      │
|      dari galeri. Hal ini tidak diizinkan.                │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  ❌ KENAPA TIDAK BOLEH?                             │  │  |
|  │  ─────────────────────────────                     │  │  |
|  │  • Foto dari galeri mungkin bukan foto asli        │  │  |
|  │  • Tidak dapat memverifikasi lokasi & waktu        │  │  |
|  │  • Dapat mengakibatkan kunjungan ditolak           │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  ✅ SOLUSI                                          │  │  |
|  │  ─────────────────                                 │  │  |
|  │  Gunakan kamera langsung untuk mengambil foto      │  │  |
|  │  di lokasi kunjungan.                               │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  KEMBALI  ]              [  📷 BUKA KAMERA  ]      |
|                                                           |
+----------------------------------------------------------+
```

### 3.4 Mobile - Photo Preview with EXIF Validation

```
+----------------------------------------------------------+
|  [ ← ]  PREVIEW FOTO                           [ ✕ ]      |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  |
|  │              [ FOTO PREVIEW ]                       │  |
|  │                                                     │  |
|  │              (dengan watermark)                     │  │  |
|  │                                                     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  ✅ VALIDASI FOTO                                   │  │  |
|  │  ───────────────────────────                       │  │  |
|  │                                                     │  │  |
|  │  ✅ EXIF Data:      Tersedia                        │  │  |
|  │  ✅ Timestamp:      23/04/2026 09:05:12             │  │  |
|  │  ✅ GPS Coordinate: -6.2088, 106.8456               │  │  |
|  │  ✅ Device:         iPhone 13 Pro                   │  │  |
|  │  ✅ Camera Source:  Kamera (bukan galeri)           │  │  |
|  │  ✅ Location Match: Valid (15m dari target)         │  │  |
|  │  ✅ Photo Freshness: Baru diambil (<5 menit)        │  │  |
|  │                                                     │  │  |
|  │  Status: 🟢 FOTO VALID                              │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📋 METADATA FOTO                                  │  │  |
|  │  ───────────────────────                           │  │  |
|  │  Filename: IMG_20260423_090512.jpg                 │  │  |
|  │  Size:      2.4 MB                                 │  │  |
|  │  Resolution: 4032 x 3024 px                        │  │  |
|  │  ISO:       64                                     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  🔄 ULANG FOTO  ]              [  ✅ SIMPAN  ]     |
|                                                           |
+----------------------------------------------------------+
```

### 3.5 Mobile - Suspicious Photo Alert

```
+----------------------------------------------------------+
|  [ ← ]  PREVIEW FOTO                           [ ✕ ]      |
+----------------------------------------------------------+
|                                                           |
|              ┌─────────────────────┐                     |
|              │       ⚠️            │                     |
|              │                     │                     |
|              │  FOTO MENCURIGAKAN   │                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  ❌ VALIDASI FOTO                                   │  │  |
|  │  ───────────────────────────                       │  │  |
|  │                                                     │  │  |
|  │  ✅ EXIF Data:      Tersedia                        │  │  |
|  │  ❌ Timestamp:      20/04/2026 14:30 (KEMARIN)      │  │  |
|  │  ✅ GPS Coordinate: -6.2088, 106.8456               │  │  |
|  │  ✅ Device:         iPhone 13 Pro                   │  │  |
|  │  ✅ Camera Source:  Kamera (bukan galeri)           │  │  |
|  │  ⚠️ Location Match: Valid (15m dari target)         │  │  |
|  │  ❌ Photo Freshness: LAMA (>24 jam)                 │  │  |
|  │                                                     │  │  |
|  │  Status: ⚠️ FOTO MENCURIGAKAN                       │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  💡 MASALAH YANG TERDETEKSI                        │  │  |
|  │  ─────────────────────────────────                │  │  |
|  │  Timestamp foto menunjukkan foto diambil kemarin.  │  │  |
|  │  Hal ini mencurigakan karena Anda baru check-in   │  │  |
|  │  hari ini.                                          │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  ⚠️ KONSEKUENSI                                     │  │  |
|  │  ─────────────────                                 │  │  |
|  │  Kunjungan ini akan ditandai sebagai "Mencurigakan"│  │  |
|  │  dan memerlukan review dari supervisor.            │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  🔄 ULANG FOTO  ]    [  ⚠️ SIMPAN TETAP  ]       |
|                                                           |
+----------------------------------------------------------+
```

### 3.6 Mobile - Watermark Preview

```
+----------------------------------------------------------+
|  [ ← ]  PREVIEW WATERMARK                       [ ✕ ]    |
+----------------------------------------------------------+
|                                                           |
|  Watermark akan ditambahkan secara otomatis ke foto.      │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  |
|  │              [ FOTO ASLI ]                          │  │  |
|  │                                                     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|                    ┃      ↓      ┃                        │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  │  |
|  │           [ FOTO DENGAN WATERMARK ]                 │  │  |
|  │                                                     │  │  |
|  │  ┌─────────────────────────────────────┐          │  │  |
|  │  │ 📍 PT. Maju Jaya                    │          │  │  |
|  │  │ 🕒 23/04/2026 09:05                 │          │  │  |
|  │  │ 👤 Arina Saffanah Zakiyyah          │          │  │  |
|  │  │ 📍 -6.2088, 106.8456                │          │  │  |
|  │  └─────────────────────────────────────┘          │  │  |
|  │                                                     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🎨 PENGATURAN WATERMARK                          │  │  |
|  │  ──────────────────────────────                   │  │  |
|  │  Posisi:     [ Kiri Atas ▼ ]                       │  │  |
|  │  Opasitas:   [ 70% ████████░░ ]                   │  │  |
|  │  Ukuran:     [ Medium ▼ ]                         │  │  |
|  │                                                     │  │  |
|  │  Informasi yang ditampilkan:                      │  │  |
|  │  ☑ Nama lokasi                                     │  │  |
|  │  ☑ Tanggal & waktu                                 │  │  |
|  │  ☑ Nama worker                                     │  │  |
|  │  ☑ Koordinat GPS                                   │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  BATAL  ]                    [  TERAPKAN  ]       |
|                                                           |
+----------------------------------------------------------+
```

### 3.7 Mobile - Photo Upload Progress

```
+----------------------------------------------------------+
|                                                           |
|              ┌─────────────────────┐                     |
|              │       ⏳            │                     |
|              │                     │                     |
|              │  MENGUNGGAH FOTO    │                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|      Mohon tunggu, foto sedang diunggah...               │
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  │  |
|  │          [ ████████████████░░░░ ] 75%              │  │  |
|  │                                                     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📊 PROGRESS DETAIL                                │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  ✅ Membaca EXIF data                              │  │  |
|  │  ✅ Validasi timestamp                             │  │  |
|  │  ✅ Validasi GPS coordinate                         │  │  |
|  │  ✅ Menerapkan watermark                           │  │  |
|  │  ⏳ Mengompresi foto...                            │  │  |
|  │  ⏳ Mengunggah ke server...                        │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
+----------------------------------------------------------+
```

### 3.8 Mobile - Photo Upload Success

```
+----------------------------------------------------------+
|                                                           |
|                                                           |
|              ┌─────────────────────┐                     |
|              │                     │                     |
|              │       ✅            │                     |
|              │                     │                     |
|              │  FOTO BERHASIL     │                     |
|              │     DIUNGGAH       │                     |
|              │                     │                     |
|              └─────────────────────┘                     |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📸 INFORMASI FOTO                                 │  │  |
|  │  ───────────────────────────                       │  │  |
|  │  Status:     🟢 Valid                              │  │  |
|  │  Ukuran:     2.4 MB → 800 KB (dikompresi)          │  │  |
|  │  Watermark:  ✅ Diterapkan                          │  │  |
|  │  Timestamp:  23/04/2026 09:05:12                    │  │  |
|  │  Lokasi:     -6.2088, 106.8456                      │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  LIHAT FOTO  ]          [  LANJUTKAN  ✅  ]       |
|                                                           |
+----------------------------------------------------------+
```

### 3.9 Mobile - Worker Photo Gallery

```
+----------------------------------------------------------+
|  [ ← ]  GALERI FOTO KUNJUNGAN                            |
+----------------------------------------------------------+
|                                                           |
|  🔍 [ Cari foto...                    ]                  |
|                                                           |
|  📅 Filter: [ Semua ▼ ]  [ Bulan Ini ▼ ]                 |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🟢 23 Apr 2026                        2 foto       │  │  |
|  │  ─────────────────────────────────────────────────  │  │  |
|  │  PT. Maju Jaya                                      │  │  |
|  │                                                     │  │  |
|  │  ┌─────────┐  ┌─────────┐                          │  │  |
|  │  │   1    │  │   2    │                          │  │  |
|  │  │ [Thumb]│  │ [Thumb]│                          │  │  |
|  │  │Check-in│  │Check-out│                          │  │  |
|  │  │ 09:05  │  │ 10:15  │                          │  │  |
|  │  └─────────┘  └─────────┘                          │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🟢 22 Apr 2026                        3 foto       │  │  |
|  │  ─────────────────────────────────────────────────  │  │  |
|  │  CV. Berkah Abadi                                   │  │  |
|  │                                                     │  │  |
|  │  ┌─────────┐  ┌─────────┐  ┌─────────┐             │  │  |
|  │  │   1    │  │   2    │  │   3    │             │  │  |
|  │  │ [Thumb]│  │ [Thumb]│  │ [Thumb]│             │  │  |
|  │  │Check-in│  │Tengah  │  │Check-out│             │  │  |
|  │  │ 14:00  │  │ 14:30  │  │ 15:30  │             │  │  |
|  │  └─────────┘  └─────────┘  └─────────┘             │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  ⚠️ 21 Apr 2026                        2 foto       │  │  |
|  │  ─────────────────────────────────────────────────  │  │  |
|  │  Toko Sentosa Electronics                          │  │  |
|  │                                                     │  │  |
|  │  ┌─────────┐  ┌─────────┐                          │  │  |
|  │  │   1    │  │   2    │                          │  │  |
|  │  │ [Thumb]│  │ [Thumb]│                          │  │  |
|  │  │Check-in│  │Check-out│                          │  │  |
|  │  │ ⚠️     │  │ ⚠️     │                          │  │  |
|  │  └─────────┘  └─────────┘                          │  │  |
|  │  ⚠️ 2 foto mencurigakan                         │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
+----------------------------------------------------------+
```

### 3.10 Mobile - Photo Detail View

```
+----------------------------------------------------------+
|  [ ← ]  DETAIL FOTO                                     |
+----------------------------------------------------------+
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │                                                     │  │  |
|  │              [ FOTO FULL SIZE ]                     │  │  |
|  │                                                     │  │  |
|  │              (dengan watermark)                     │  │  |
|  │                                                     │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📋 METADATA FOTO                                  │  │  |
|  │  ───────────────────────                           │  │  |
|  │  Status:          🟢 Valid                         │  │  |
|  │  Lokasi:          PT. Maju Jaya                    │  │  |
|  │  Tanggal:         23/04/2026                       │  │  |
|  │  Waktu:           09:05:12 WIB                     │  │  |
|  │  Koordinat:       -6.2088, 106.8456                │  │  |
|  │  Akurasi GPS:     ±10 meter                       │  │  |
|  │  Perangkat:       iPhone 13 Pro                   │  │  |
|  │  Ukuran asli:     2.4 MB                          │  │  |
|  │  Ukuran kompresi: 800 KB                          │  │  |
|  │  Resolusi:         4032 x 3024 px                 │  │  |
|  │  ISO:             64                              │  │  |
|  │  Tipe:            Check-in                        │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  🎨 WATERMARK                                      │  │  |
|  │  ─────────────────                                 │  │  |
|  │  📍 PT. Maju Jaya                                  │  │  |
|  │  🕒 23/04/2026 09:05                               │  │  |
|  │  👤 Arina Saffanah Zakiyyah                        │  │  |
|  │  📍 -6.2088, 106.8456                              │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  🗑️ HAPUS  ]          [  📥 UNDUH  ]             |
|                                                           |
+----------------------------------------------------------+
```

### 3.11 Mobile - Supervisor Photo Review

```
+----------------------------------------------------------+
|  [ ← ]  REVIEW FOTO KUNJUNGAN                            |
+----------------------------------------------------------+
|                                                           |
|  PT. Maju Jaya - 23 Apr 2026                              |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📸 FOTO CHECK-IN                                   │  │  |
|  │  ───────────────────────────                       │  │  |
|  │                                                     │  │  |
|  │  ┌─────────────────────────────────────┐          │  │  |
|  │  │                                     │          │  │  |
|  │  │        [ FOTO PREVIEW ]             │          │  │  |
|  │  │                                     │          │  │  |
|  │  └─────────────────────────────────────┘          │  │  |
|  │                                                     │  │  |
|  │  ┌─────────────────────────────────────────────┐ │  │  |
|  │  │ ✅ EXIF: Valid                             │ │  │  |
|  │  │ ✅ Timestamp: 23/04/2026 09:05:12          │ │  │  |
|  │  │ ✅ GPS: -6.2088, 106.8456 (15m dari target)│ │  │  |
|  │  │ ✅ Source: Kamera (bukan galeri)          │ │  │  |
|  │  │ ✅ Freshness: Baru diambil (<5 menit)     │ │  │  |
|  │  └─────────────────────────────────────────────┘ │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  📸 FOTO CHECK-OUT                                  │  │  |
|  │  ───────────────────────────                       │  │  |
|  │                                                     │  │  |
|  │  ┌─────────────────────────────────────┐          │  │  |
|  │  │                                     │          │  │  |
|  │  │        [ FOTO PREVIEW ]             │          │  │  |
|  │  │                                     │          │  │  |
|  │  └─────────────────────────────────────┘          │  │  |
|  │                                                     │  │  |
|  │  ┌─────────────────────────────────────────────┐ │  │  |
|  │  │ ✅ EXIF: Valid                             │ │  │  |
|  │  │ ✅ Timestamp: 23/04/2026 10:15:30          │ │  │  |
|  │  │ ✅ GPS: -6.2088, 106.8456 (12m dari target)│ │  │  |
|  │  │ ✅ Source: Kamera (bukan galeri)          │ │  │  |
|  │  │ ✅ Freshness: Baru diambil (<5 menit)     │ │  │  |
|  │  └─────────────────────────────────────────────┘ │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|  ┌────────────────────────────────────────────────────┐  |
|  │  👮 KEPUTUSAN SUPERVISOR                          │  │  |
|  │  ────────────────────────────────                 │  │  |
|  │  Berdasarkan review foto, bukti kunjungan:        │  │  |
|  │  ⚪ Valid                                          │  │  |
|  │  ⚪ Mencurigakan                                   │  │  |
|  │  ⚪ Tidak Valid                                   │  │  |
|  │                                                     │  │  |
|  │  Catatan (opsional):                               │  │  |
|  │  +---------------------------------------------+   │  │  |
|  │  |                                             |   │  │  |
|  │  +---------------------------------------------+   │  │  |
|  └────────────────────────────────────────────────────┘  |
|                                                           |
|      [  BATAL  ]                    [  SIMPAN  ✅  ]      |
|                                                           |
+----------------------------------------------------------+
```

---

## 4. Component Specifications

### 4.1 Camera Button

**Properties:**

| Property | Value |
|----------|-------|
| Width | 80px |
| Height | 80px |
| Border Radius | 50% |
| Background | white |
| Border | 4px solid var(--color-gray-300) |
| Shadow | var(--shadow-lg) |

**States:**

| State | Border | Size |
|-------|--------|------|
| Default | gray-300 | 80px |
| Pressed | primary-500 | 75px |
| Disabled | gray-200 | 80px |

### 4.2 EXIF Validation Card

**Properties:**

| Property | Value |
|----------|-------|
| Width | 100% |
| Padding | 12px |
| Border Radius | 8px |
| Background | gray-50 |
| Border | 1px solid |

**Status Colors:**

| Status | Background | Border | Icon |
|--------|------------|--------|------|
| Valid | green-50 | green-200 | ✅ |
| Warning | orange-50 | orange-200 | ⚠️ |
| Invalid | red-50 | red-200 | ❌ |

### 4.3 Watermark Overlay

**Properties:**

| Property | Value |
|----------|-------|
| Position | Absolute |
| Background | rgba(0, 0, 0, 0.7) |
| Text Color | white |
| Padding | 8px 12px |
| Border Radius | 4px |
| Font Size | 11px |

**Positions:**

| Position | Top | Left |
|----------|-----|------|
| Top Left | 8px | 8px |
| Top Right | 8px | auto |
| Bottom Left | auto | 8px |
| Bottom Right | auto | auto |

---

## 5. Responsive Behavior

| Breakpoint | Width | Gallery Grid | Photo Preview |
|------------|-------|--------------|---------------|
| Mobile | < 768px | 2 columns | Full width |
| Tablet | 768-1024px | 3 columns | 80% width |
| Desktop | > 1024px | 4 columns | 60% width |

---

## 6. Interactions & Animations

| Element | Trigger | Animation | Duration | Easing |
|---------|---------|-----------|----------|--------|
| Camera Button | Press | Scale down | 100ms | ease-out |
| Photo Capture | Shutter | Flash | 150ms | ease-out |
| Validation | Complete | Fade in | 300ms | ease |
| Upload Progress | Progress | Bar fill | Real-time | linear |
| Gallery Photo | Tap | Scale up | 200ms | ease-out |

---

## 7. Design Tokens Used

### Colors

| Token | Usage |
|-------|-------|
| --color-primary-500 | Camera button, actions |
| --color-green-500 | Valid status, success |
| --color-orange-500 | Suspicious photo warning |
| --color-red-500 | Invalid status, error |
| --color-gray-900 | Primary text |
| --color-gray-600 | Secondary text |

### Typography

| Element | Token |
|---------|-------|
| Photo caption | --text-body (14px) |
| Metadata | --text-caption (11px) |
| Watermark | --text-caption (11px) |

---

## 8. Accessibility

- [ ] Camera button minimum 80x80px
- [ ] EXIF validation status announced
- [ ] Error messages with actionable guidance
- [ ] Photo alt text with location info
- [ ] Color contrast minimum 4.5:1

---

## 9. Empty & Error States

### Empty Gallery State

```
+----------------------------------------------------------+
|                                                           |
|              [ 📸 Illustration ]                         |
|                                                           |
|           Belum ada foto kunjungan                       │
|                                                           |
|      Foto kunjungan akan muncul di sini                   │
|                                                           |
+----------------------------------------------------------+
```

### Upload Error State

```
+----------------------------------------------------------+
|                                                           |
|              [ ❌ Illustration ]                          |
|                                                           |
|           Gagal mengunggah foto                           │
|                                                           |
|      Periksa koneksi internet dan coba lagi               │
|                                                           |
|      [  🔄 COBA LAGI  ]                                  │
|                                                           |
+----------------------------------------------------------+
```

---

## 10. Assets Required

| Asset | Type | Status | Notes |
|-------|------|--------|-------|
| Camera icon | SVG | Needed | 24x24px |
| Gallery icon | SVG | Needed | 24x24px |
| Check icon | SVG | Needed | 16x16px |
| Warning icon | SVG | Needed | 16x16px |
| Empty gallery illustration | SVG | Needed | Empty state |

---

## 11. Design QA Checklist

- [ ] Watermark legible on all backgrounds
- [ ] EXIF validation accurate
- [ ] Gallery detection working
- [ ] Upload progress visible
- [ ] Photo preview quality maintained
- [ ] Compression artifacts minimal

---

## 12. Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Designer | | | Pending |
| PM | | | Pending |
| Tech Lead | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-visit-verification/02-photo-evidence---design.md

RELATED FILES:
- 02-photo-evidence---business.md (Requirements)
- 02-photo-evidence---testing.md (Test scenarios)
- 02-photo-evidence---technical.md (Implementation)
-->
