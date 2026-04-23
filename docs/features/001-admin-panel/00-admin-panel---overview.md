# EPIC OVERVIEW
## [EPIC-001] Admin Panel

---

| Attribute | Value |
|-----------|-------|
| Epic ID | EPIC-001 |
| Epic Name | Admin Panel |
| Type | Functional |
| Status | Backlog |
| Owner | — |
| Created | 2026-04-23 |
| Last Updated | 2026-04-23 |

---

## 1. Background

The Admin Panel provides a single centralized platform for administrators to manage users, data, system settings, and monitor platform activity. Without this, admin operations require direct developer intervention, slowing down routine tasks and creating operational risk.

**Related BRD Section:** See `docs/project/01-BRD.md`

---

## 2. Scope

### 2.1 In Scope

| # | Capability | Description |
|---|------------|-------------|
| 1 | Manajemen User & Role | Buat, lihat, edit, nonaktifkan user dan kelola role (Admin, Manager, Sales) |
| 2 | Manajemen Outlet | CRUD outlet beserta koordinat GPS untuk mendukung optimasi rute kunjungan |
| 3 | Manajemen Katalog Produk | CRUD produk (nama, SKU, harga, satuan, kategori) sebagai referensi order Sales |

### 2.2 Out of Scope

| # | Item | Reason |
|---|------|--------|
| 1 | Mobile app admin interface | Admin panel is web-only |
| 2 | Public-facing features | This epic covers internal admin tooling only |
| 3 | Third-party integrations | No external system integrations (e.g., CRM, ERP) |
| 4 | Billing & payments | Financial transactions are handled separately |

---

## 3. Features

| Seq | Feature ID | Feature Name | Priority | Sprint | Status |
|-----|------------|--------------|----------|--------|--------|
| 01 | FEAT-001 | Manajemen User & Role | Must Have | — | Planned |
| 02 | FEAT-002 | Manajemen Outlet | Must Have | — | Planned |
| 03 | FEAT-003 | Manajemen Katalog Produk | Must Have | — | Planned |

### Feature Documents

| Feature | Business Spec | Technical Spec | Design Spec |
|---------|---------------|----------------|-------------|
| FEAT-001 Manajemen User & Role | `01-user-management---business.md` | `01-user-management---technical.md` | `01-user-management---design.md` |
| FEAT-002 Manajemen Outlet | `02-outlet-management---business.md` | `02-outlet-management---technical.md` | `02-outlet-management---design.md` |
| FEAT-003 Manajemen Katalog Produk | `03-product-catalog---business.md` | `03-product-catalog---technical.md` | `03-product-catalog---design.md` |

---

## 4. Dependencies

| # | Dependency | Type | Owner | Status |
|---|------------|------|-------|--------|
| 1 | Authentication system | Internal | — | Pending |

---

## 5. Completion Tracker

### Overall Progress

```
[░░░░░░░░░░░░░░░░░░░░] 0% Complete
```

| Metric | Value |
|--------|-------|
| Total Features | 3 |
| Completed | 0 |
| In Progress | 0 |
| Not Started | 3 |

### Feature Status

| Feature | Business | Technical | Design | Dev | QA | Status |
|---------|----------|-----------|--------|-----|-----|--------|
| FEAT-001 Manajemen User & Role | 🔄 | ⬜ | ⬜ | ⬜ | ⬜ | Planning |
| FEAT-002 Manajemen Outlet | 🔄 | ⬜ | ⬜ | ⬜ | ⬜ | Planning |
| FEAT-003 Manajemen Katalog Produk | 🔄 | ⬜ | ⬜ | ⬜ | ⬜ | Planning |

Legend: ✅ Done | 🔄 In Progress | ⬜ Not Started | ❌ Blocked

---

## 6. Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-04-23 | Hendy Yanuar | Initial version |
| 1.1 | 2026-04-23 | Hendy Yanuar | Updated to 3 features per BRD; added business specs for all features |

---

<!--
FILE LOCATION: docs/features/001-admin-panel/00-admin-panel---overview.md

RELATED FILES:
- 01-user-management---business.md
- 01-user-management---technical.md
- 01-user-management---design.md
- 02-outlet-management---business.md
- 02-outlet-management---technical.md
- 02-outlet-management---design.md
- 03-product-catalog---business.md
- 03-product-catalog---technical.md
- 03-product-catalog---design.md
-->
