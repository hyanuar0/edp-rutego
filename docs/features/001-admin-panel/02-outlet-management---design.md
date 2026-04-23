# DESIGN SPECIFICATION
## [FEAT-002] Manajemen Outlet

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-002 |
| Feature Name | Manajemen Outlet |
| Epic | EPIC-001: Admin Panel |
| Status | Draft |
| Owner | UI/UX Designer |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Figma Link | — |

---

## 1. Design Goals

- Tabel outlet dengan indikator visual yang jelas untuk status GPS (terisi/kosong)
- Form input koordinat GPS yang memberi petunjuk format yang diharapkan
- Mudah mengidentifikasi outlet yang belum memiliki koordinat GPS (risiko kualitas rute)
- Dioptimalkan untuk desktop; Admin Panel tidak perlu optimal di smartphone

---

## 2. Reference Documents

| Document | Location |
|----------|----------|
| Business Spec | `02-outlet-management---business.md` |
| Testing Spec | `02-outlet-management---testing.md` |

---

## 3. User Flow

```
┌──────────────────────────────────────────────────────────────────┐
│                        USER FLOW                                  │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  [Login Admin]                                                    │
│       │                                                           │
│       ▼                                                           │
│  [Halaman Manajemen Outlet]  ◀─────────────────────┐             │
│       │                                             │             │
│       ├──[Klik + Tambah Outlet]──▶[Form Tambah]     │             │
│       │                               │             │             │
│       │                          [Isi & Simpan]     │             │
│       │                               │             │             │
│       │                          [Sukses] ──────────┘             │
│       │                               │                           │
│       │                          [Error GPS/Form] ──▶ [Tampilkan pesan] │
│       │                                                           │
│       ├──[Klik nama outlet]──▶[Form Edit Outlet]                  │
│       │                           │                               │
│       │                      [Simpan] ──▶ [Sukses / Error]        │
│       │                                                           │
│       └──[Klik Nonaktifkan/Aktifkan]──▶[Dialog Konfirmasi]        │
│                                             │                     │
│                                        [Konfirmasi] ──▶ [Update] │
│                                        [Batal] ──▶ [Tutup]       │
└──────────────────────────────────────────────────────────────────┘
```

---

## 4. Wireframes

### 4.1 Halaman Daftar Outlet

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+-------------------------------------------------+
|                  |                                                  |
|  NAVIGASI        |  Manajemen Outlet                               |
|  ─────────       |  ─────────────────────────────────────────────  |
|  Dashboard       |                                                  |
|  User            |  [🔍 Cari nama atau alamat...]   [Status ▼]    [+ Tambah Outlet] |
|  > Outlet ●      |                                                  |
|  Katalog Produk  |  ┌───────────────────────────────────────────────────────────┐ |
|                  |  │ Nama Outlet     │ Alamat          │ GPS  │ Status  │ Aksi  │ |
|                  |  ├───────────────────────────────────────────────────────────┤ |
|                  |  │ Toko Maju       │ Jl. Raya No.1   │ ✅   │ ● Aktif │ Edit  Nonaktifkan │ |
|                  |  ├───────────────────────────────────────────────────────────┤ |
|                  |  │ Warung Sejahtera│ Jl. Pahlawan 5  │ ✅   │ ● Aktif │ Edit  Nonaktifkan │ |
|                  |  ├───────────────────────────────────────────────────────────┤ |
|                  |  │ Minimart Ceria  │ Jl. Melati 12   │ ⚠️   │ ● Aktif │ Edit  Nonaktifkan │ |
|                  |  │                 │                 │(GPS kosong)│    │       │ |
|                  |  ├───────────────────────────────────────────────────────────┤ |
|                  |  │ Toko Bahagia    │ Jl. Kenanga 3   │ ✅   │ ○ Nonaktif │ Edit  Aktifkan │ |
|                  |  └───────────────────────────────────────────────────────────┘ |
|                  |                                                  |
|                  |  Menampilkan 1–10 dari 48 outlet  [< Prev] [1] [2] [3] [Next >] |
+------------------+-------------------------------------------------+
```

**Catatan:** Kolom GPS menampilkan ✅ jika koordinat sudah terisi, ⚠️ jika belum — memudahkan Admin mengidentifikasi outlet yang perlu dilengkapi.

---

### 4.2 Form Tambah / Edit Outlet

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+-------------------------------------------------+
|                  |                                                  |
|  NAVIGASI        |  ← Kembali ke Daftar Outlet                     |
|  ─────────       |                                                  |
|  Dashboard       |  Tambah Outlet Baru                             |
|  User            |  ─────────────────────────────────────────────  |
|  > Outlet ●      |                                                  |
|  Katalog Produk  |  Nama Outlet *                                  |
|                  |  +----------------------------------------+     |
|                  |  |  Masukkan nama outlet                   |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  Alamat *                                       |
|                  |  +----------------------------------------+     |
|                  |  |  Masukkan alamat lengkap                |     |
|                  |  |                                         |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  Koordinat GPS                                  |
|                  |  ──────────────────────────────                 |
|                  |  Latitude *                  Longitude *        |
|                  |  +-------------------+    +-------------------+ |
|                  |  |  contoh: -6.2088  |    |  contoh: 106.8456 | |
|                  |  +-------------------+    +-------------------+ |
|                  |  Gunakan angka desimal (titik sebagai pemisah) |
|                  |                                                  |
|                  |  Nama Kontak                                    |
|                  |  +----------------------------------------+     |
|                  |  |  Opsional                               |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  Nomor Telepon                                  |
|                  |  +----------------------------------------+     |
|                  |  |  Opsional                               |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  [Batal]                        [Simpan]        |
+------------------+-------------------------------------------------+
```

---

### 4.3 Dialog Konfirmasi Nonaktifkan Outlet

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+--+-----------------------------------------+---+
|                  |  |                                         |   |
|  NAVIGASI        |  |  Nonaktifkan Outlet                     | X |
|                  |  +-----------------------------------------+   |
|                  |  |                                         |   |
|                  |  |  Anda akan menonaktifkan outlet:        |   |
|                  |  |                                         |   |
|                  |  |  Nama   : Toko Maju                     |   |
|                  |  |  Alamat : Jl. Raya No. 1, Jakarta       |   |
|                  |  |                                         |   |
|                  |  |  Outlet ini tidak akan muncul sebagai   |   |
|                  |  |  tujuan kunjungan di Route Planning.    |   |
|                  |  |                                         |   |
|                  |  |  [Batal]         [Ya, Nonaktifkan]      |   |
|                  |  |                                         |   |
|                  |  +-----------------------------------------+   |
+------------------+-------------------------------------------------+
```

---

## 5. Empty & Error States

### Empty State — Belum Ada Outlet

```
+--------------------------------------------------+
|                                                  |
|              🏪                                   |
|                                                  |
|         Belum ada outlet terdaftar               |
|                                                  |
|   Tambahkan outlet pertama agar Sales bisa       |
|   merencanakan rute kunjungan                    |
|                                                  |
|              [+ Tambah Outlet]                   |
|                                                  |
+--------------------------------------------------+
```

### Empty State — Pencarian Tidak Ditemukan

```
+--------------------------------------------------+
|                                                  |
|              🔍                                   |
|                                                  |
|       Tidak ada outlet yang cocok                |
|                                                  |
|   Coba kata kunci atau filter yang berbeda       |
|                                                  |
+--------------------------------------------------+
```

### Error State — Gagal Memuat Data

```
+--------------------------------------------------+
|                                                  |
|              ⚠️                                   |
|                                                  |
|       Gagal memuat daftar outlet                 |
|                                                  |
|   Periksa koneksi internet Anda dan coba lagi    |
|                                                  |
|              [Coba Lagi]                         |
|                                                  |
+--------------------------------------------------+
```

---

## 6. Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Designer | | | Pending |
| PM | | | Pending |
| Tech Lead | | | Pending |

---

<!--
FILE LOCATION: docs/features/001-admin-panel/02-outlet-management---design.md

RELATED FILES:
- 02-outlet-management---business.md (Requirements)
- 02-outlet-management---testing.md (Test scenarios)
-->
