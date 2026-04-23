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
| 1 | User Management | Create, view, edit, deactivate users and manage roles/permissions |
| 2 | Content / Data Management | CRUD operations on core business data (products, categories, etc.) |

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
| 01 | FEAT-001 | User Management | Must Have | — | Planned |
| 02 | FEAT-002 | Content / Data Management | Must Have | — | Planned |

### Feature Documents

| Feature | Business Spec | Technical Spec | Design Spec |
|---------|---------------|----------------|-------------|
| FEAT-001 User Management | `01-user-management---business.md` | `01-user-management---technical.md` | `01-user-management---design.md` |
| FEAT-002 Content / Data Management | `02-content-data-management---business.md` | `02-content-data-management---technical.md` | `02-content-data-management---design.md` |

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
| Total Features | 2 |
| Completed | 0 |
| In Progress | 0 |
| Not Started | 2 |

### Feature Status

| Feature | Business | Technical | Design | Dev | QA | Status |
|---------|----------|-----------|--------|-----|-----|--------|
| FEAT-001 User Management | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | Planning |
| FEAT-002 Content / Data Management | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | Planning |

Legend: ✅ Done | 🔄 In Progress | ⬜ Not Started | ❌ Blocked

---

## 6. Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-04-23 | Hendy Yanuar | Initial version |

---

<!--
FILE LOCATION: docs/features/001-admin-panel/00-admin-panel---overview.md

RELATED FILES:
- 01-user-management---business.md
- 01-user-management---technical.md
- 01-user-management---design.md
- 02-content-data-management---business.md
- 02-content-data-management---technical.md
- 02-content-data-management---design.md
-->
