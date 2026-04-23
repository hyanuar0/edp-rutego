# DESIGN SPECIFICATION
## [FEAT-003] Manajemen Katalog Produk

---

| Attribute | Value |
|-----------|-------|
| Feature ID | FEAT-003 |
| Feature Name | Manajemen Katalog Produk |
| Epic | EPIC-001: Admin Panel |
| Status | Draft |
| Owner | UI/UX Designer |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |
| Figma Link | — |

---

## 1. Design Goals

- Katalog produk yang mudah difilter per kategori dan status
- Form input produk yang jelas dengan panduan format satuan dan harga
- SKU ditampilkan secara prominent di tabel agar mudah diidentifikasi
- Dioptimalkan untuk desktop; Admin Panel tidak perlu optimal di smartphone

---

## 2. Reference Documents

| Document | Location |
|----------|----------|
| Business Spec | `03-product-catalog---business.md` |
| Testing Spec | `03-product-catalog---testing.md` |

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
│  [Halaman Katalog Produk]  ◀──────────────────────┐              │
│       │                                            │              │
│       ├──[Klik + Tambah Produk]──▶[Form Tambah]    │              │
│       │                               │            │              │
│       │                          [Isi & Simpan]    │              │
│       │                               │            │              │
│       │                          [Sukses] ─────────┘              │
│       │                               │                           │
│       │                          [Error] ──▶ [Tampilkan pesan]   │
│       │                                                           │
│       ├──[Klik nama produk]──▶[Form Edit Produk]                  │
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

### 4.1 Halaman Daftar Produk

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+-------------------------------------------------+
|                  |                                                  |
|  NAVIGASI        |  Katalog Produk                                 |
|  ─────────       |  ─────────────────────────────────────────────  |
|  Dashboard       |                                                  |
|  User            |  [🔍 Cari nama atau SKU...]  [Kategori ▼] [Status ▼]  [+ Tambah Produk] |
|  Outlet          |                                                  |
|  > Katalog ●     |  ┌───────────────────────────────────────────────────────────────┐ |
|                  |  │ Nama Produk         │ SKU      │ Kategori │ Harga    │ Satuan │ Status  │ Aksi  │ |
|                  |  ├───────────────────────────────────────────────────────────────┤ |
|                  |  │ Mie Goreng Instan   │ PRD-001  │ Makanan  │ Rp 3.500 │ pcs    │ ● Aktif │ Edit  Nonaktifkan │ |
|                  |  ├───────────────────────────────────────────────────────────────┤ |
|                  |  │ Susu UHT Full Cream │ PRD-002  │ Minuman  │ Rp 8.000 │ pcs    │ ● Aktif │ Edit  Nonaktifkan │ |
|                  |  ├───────────────────────────────────────────────────────────────┤ |
|                  |  │ Sabun Mandi Cair    │ PRD-003  │ Perawatan│ Rp 12.500│ botol  │ ● Aktif │ Edit  Nonaktifkan │ |
|                  |  ├───────────────────────────────────────────────────────────────┤ |
|                  |  │ Kopi Sachet         │ PRD-004  │ Minuman  │ Rp 2.000 │ pcs    │ ○ Nonaktif │ Edit  Aktifkan │ |
|                  |  └───────────────────────────────────────────────────────────────┘ |
|                  |                                                  |
|                  |  Menampilkan 1–10 dari 87 produk  [< Prev] [1] [2] [3] [Next >] |
+------------------+-------------------------------------------------+
```

---

### 4.2 Form Tambah / Edit Produk

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+-------------------------------------------------+
|                  |                                                  |
|  NAVIGASI        |  ← Kembali ke Katalog Produk                    |
|  ─────────       |                                                  |
|  Dashboard       |  Tambah Produk Baru                             |
|  User            |  ─────────────────────────────────────────────  |
|  Outlet          |                                                  |
|  > Katalog ●     |  Nama Produk *                                  |
|                  |  +----------------------------------------+     |
|                  |  |  Masukkan nama produk                   |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  SKU / Kode Produk *                            |
|                  |  +----------------------------------------+     |
|                  |  |  contoh: PRD-001                        |     |
|                  |  +----------------------------------------+     |
|                  |  SKU harus unik di seluruh katalog              |
|                  |                                                  |
|                  |  Harga Satuan (Rp) *      Satuan *             |
|                  |  +---------------------+  +------------------+ |
|                  |  |  contoh: 3500        |  |  pcs ▼          | |
|                  |  +---------------------+  +------------------+ |
|                  |                           pcs / dus / karton / botol / lainnya |
|                  |                                                  |
|                  |  Kategori *                                     |
|                  |  +----------------------------------------+     |
|                  |  |  Pilih atau ketik kategori baru...  ▼   |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  Deskripsi                                      |
|                  |  +----------------------------------------+     |
|                  |  |  Opsional                               |     |
|                  |  |                                         |     |
|                  |  +----------------------------------------+     |
|                  |                                                  |
|                  |  [Batal]                        [Simpan]        |
+------------------+-------------------------------------------------+
```

---

### 4.3 Dialog Konfirmasi Nonaktifkan Produk

```
+--------------------------------------------------------------------+
|  RuteGo Admin                              [Admin Name ▼]  [Logout]|
+------------------+--+-----------------------------------------+---+
|                  |  |                                         |   |
|  NAVIGASI        |  |  Nonaktifkan Produk                     | X |
|                  |  +-----------------------------------------+   |
|                  |  |                                         |   |
|                  |  |  Anda akan menonaktifkan produk:        |   |
|                  |  |                                         |   |
|                  |  |  Nama  : Mie Goreng Instan              |   |
|                  |  |  SKU   : PRD-001                        |   |
|                  |  |  Harga : Rp 3.500 / pcs                 |   |
|                  |  |                                         |   |
|                  |  |  Produk ini tidak akan tersedia         |   |
|                  |  |  di form order Sales setelah            |   |
|                  |  |  dinonaktifkan.                         |   |
|                  |  |                                         |   |
|                  |  |  [Batal]         [Ya, Nonaktifkan]      |   |
|                  |  |                                         |   |
|                  |  +-----------------------------------------+   |
+------------------+-------------------------------------------------+
```

---

## 5. Empty & Error States

### Empty State — Belum Ada Produk

```
+--------------------------------------------------+
|                                                  |
|              📦                                   |
|                                                  |
|       Katalog produk masih kosong                |
|                                                  |
|   Tambahkan produk agar Sales dapat membuat      |
|   order digital di lapangan                      |
|                                                  |
|              [+ Tambah Produk]                   |
|                                                  |
+--------------------------------------------------+
```

### Empty State — Filter Tidak Menemukan Hasil

```
+--------------------------------------------------+
|                                                  |
|              🔍                                   |
|                                                  |
|       Tidak ada produk yang cocok                |
|                                                  |
|   Coba kata kunci, kategori, atau filter         |
|   status yang berbeda                            |
|                                                  |
+--------------------------------------------------+
```

### Error State — Gagal Memuat Data

```
+--------------------------------------------------+
|                                                  |
|              ⚠️                                   |
|                                                  |
|       Gagal memuat katalog produk                |
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
FILE LOCATION: docs/features/001-admin-panel/03-product-catalog---design.md

RELATED FILES:
- 03-product-catalog---business.md (Requirements)
- 03-product-catalog---testing.md (Test scenarios)
-->
