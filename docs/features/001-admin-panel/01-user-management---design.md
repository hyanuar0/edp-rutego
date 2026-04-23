# DESIGN SPECIFICATION
## [FEAT-001] Manajemen User & Role

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-001 |
| Feature Name | Manajemen User & Role |
| Epic | EPIC-001: Admin Panel |
| Status | Draft |
| Owner | UI/UX Designer |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Figma Link | — |

---

## 1. Design Goals

- Tabel user yang mudah dipindai dengan status dan role yang langsung terlihat
- Form tambah/edit yang ringkas dan berurutan secara logis
- Konfirmasi eksplisit sebelum tindakan nonaktifkan/aktifkan yang tidak dapat dibatalkan secara instan
- Dioptimalkan untuk desktop; Admin Panel tidak perlu optimal di smartphone

---

## 2. Reference Documents

| Document | Location |
|----------|----------|
| Business Spec | `01-user-management---business.md` |
| Testing Spec | `01-user-management---testing.md` |

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
│  [Halaman Manajemen User]  ◀──────────────────────┐              │
│       │                                            │              │
│       ├──[Klik + Tambah User]──▶[Form Tambah]      │              │
│       │                             │              │              │
│       │                        [Isi & Simpan]      │              │
│       │                             │              │              │
│       │                        [Sukses] ───────────┘              │
│       │                             │                             │
│       │                        [Error] ──▶ [Tampilkan pesan]     │
│       │                                                           │
│       ├──[Klik nama user]──▶[Form Edit User]                      │
│       │                         │                                 │
│       │                    [Simpan] ──▶ [Sukses / Error]          │
│       │                                                           │
│       └──[Klik Nonaktifkan/Aktifkan]──▶[Dialog Konfirmasi]        │
│                                             │                     │
│                                        [Konfirmasi] ──▶ [Update] │
│                                             │                     │
│                                        [Batal] ──▶ [Tutup]       │
└──────────────────────────────────────────────────────────────────┘
```

---

## 4. Wireframes

### 4.1 Halaman Daftar User

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+-------------------------------------------------+
|                  |                                                  |
|  NAVIGASI        |  Manajemen User                                 |
|  ─────────       |  ─────────────────────────────────────────────  |
|  Dashboard       |                                                  |
|  > User ●        |  [🔍 Cari nama atau email...]   [Role ▼] [Status ▼]  [+ Tambah User] |
|  Outlet          |                                                  |
|  Katalog Produk  |  ┌────────────────────────────────────────────┐ |
|                  |  │ Nama           │ Email         │ Role     │ Status  │ Aksi   │ |
|                  |  ├────────────────────────────────────────────┤ |
|                  |  │ Budi Santoso   │ budi@...      │ Sales    │ ● Aktif │ Edit  Nonaktifkan │ |
|                  |  ├────────────────────────────────────────────┤ |
|                  |  │ Diana Putri    │ diana@...     │ Manager  │ ● Aktif │ Edit  Nonaktifkan │ |
|                  |  ├────────────────────────────────────────────┤ |
|                  |  │ Eko Prasetyo   │ eko@...       │ Admin    │ ○ Nonaktif │ Edit  Aktifkan │ |
|                  |  ├────────────────────────────────────────────┤ |
|                  |  │ ...            │               │          │         │       │ |
|                  |  └────────────────────────────────────────────┘ |
|                  |                                                  |
|                  |  Menampilkan 1–10 dari 24 user   [< Prev] [1] [2] [3] [Next >] |
+------------------+-------------------------------------------------+
```

---

### 4.2 Form Tambah / Edit User

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+-------------------------------------------------+
|                  |                                                  |
|  NAVIGASI        |  ← Kembali ke Daftar User                       |
|  ─────────       |                                                  |
|  Dashboard       |  Tambah User Baru                               |
|  > User ●        |  ─────────────────────────────────────────────  |
|  Outlet          |                                                  |
|  Katalog Produk  |  Nama Lengkap *                                 |
|                  |  +----------------------------------------+     |
|                  |  |  Masukkan nama lengkap                  |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  Email *                                        |
|                  |  +----------------------------------------+     |
|                  |  |  contoh@email.com                       |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  Password Sementara *                           |
|                  |  +----------------------------------------+     |
|                  |  |  ••••••••                           [👁]|     |
|                  |  +----------------------------------------+     |
|                  |  Min. 8 karakter                               |
|                  |                                                  |
|                  |  Role *                                         |
|                  |  ( ) Admin   (●) Manager   ( ) Sales           |
|                  |                                                  |
|                  |  [Batal]                        [Simpan]        |
+------------------+-------------------------------------------------+
```

**Catatan:**
- Field dengan tanda `*` adalah wajib
- Pesan error muncul di bawah field terkait (merah, teks kecil)
- Tombol "Simpan" disabled sampai field wajib terisi semua
- Pada form Edit, field Password dikosongkan dan opsional (hanya diisi jika ingin reset)

---

### 4.3 Dialog Konfirmasi Nonaktifkan User

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+--+-----------------------------------------+---+
|                  |  |                                         |   |
|  NAVIGASI        |  |  Nonaktifkan User                       | X |
|                  |  +-----------------------------------------+   |
|                  |  |                                         |   |
|                  |  |  Anda akan menonaktifkan akun:          |   |
|                  |  |                                         |   |
|                  |  |  Nama  : Budi Santoso                   |   |
|                  |  |  Email : budi@rutego.com                |   |
|                  |  |  Role  : Sales                          |   |
|                  |  |                                         |   |
|                  |  |  User ini tidak akan bisa login         |   |
|                  |  |  setelah dinonaktifkan.                 |   |
|                  |  |                                         |   |
|                  |  |  [Batal]          [Ya, Nonaktifkan]     |   |
|                  |  |                                         |   |
|                  |  +-----------------------------------------+   |
+------------------+-------------------------------------------------+
```

---

## 5. Empty & Error States

### Empty State — Belum Ada User

```
+--------------------------------------------------+
|                                                  |
|              👤                                   |
|                                                  |
|         Belum ada user terdaftar                 |
|                                                  |
|   Tambahkan user pertama untuk memulai           |
|                                                  |
|              [+ Tambah User]                     |
|                                                  |
+--------------------------------------------------+
```

### Empty State — Pencarian Tidak Ditemukan

```
+--------------------------------------------------+
|                                                  |
|              🔍                                   |
|                                                  |
|       Tidak ada user yang cocok                  |
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
|       Gagal memuat daftar user                   |
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
FILE LOCATION: docs/features/001-admin-panel/01-user-management---design.md

RELATED FILES:
- 01-user-management---business.md (Requirements)
- 01-user-management---testing.md (Test scenarios)
-->
