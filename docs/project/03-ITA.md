# IMPLEMENTATION & TECHNICAL ARCHITECTURE
## RuteGo – Sales Route Planning & Execution System

---

| Atribut | Nilai |
|---------|-------|
| Document ID | ITA-RUTEGO-001 |
| Versi | 1.0 |
| Status | Draft |
| Author | Tech Lead |
| Dibuat | 2026-04-23 |
| Terakhir Diperbarui | 2026-04-23 |

---

## Changelog

| Versi | Tanggal | Author | Perubahan |
|-------|---------|--------|-----------|
| 1.0 | 2026-04-23 | Tech Lead | Versi awal |

---

## 1. Prinsip Arsitektur

| Prinsip | Deskripsi | Rasional |
|---------|-----------|----------|
| **API-First** | Semua business logic diekspos melalui RESTful API | Mendukung dua client terpisah (Admin App & User App) dari satu sumber kebenaran |
| **Modular Monolith** | Backend dibangun sebagai satu aplikasi NestJS dengan modul yang terpisah jelas | Menjaga simplisitas deployment MVP 1 tanpa overhead microservices |
| **Security by Design** | Auth, RBAC, enkripsi, dan validasi input diimplementasikan dari awal | Mengurangi attack surface; data kunjungan sales bersifat sensitif |
| **Self-Hosted Stack** | Infrastruktur berjalan di VPS dengan Docker Compose (API, DB, Redis, MinIO) | Kontrol penuh atas data; tidak ada vendor lock-in untuk storage dan cache |
| **TypeScript Everywhere** | Backend (NestJS) dan Frontend (React + Vite) keduanya menggunakan TypeScript | Type safety mengurangi bug runtime; refactoring lebih aman |

---

## 2. Arsitektur Sistem

### 2.1 High-Level Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                              CLIENTS                                      │
│                                                                           │
│   ┌───────────────────────┐        ┌───────────────────────┐             │
│   │      Admin App        │        │       User App        │             │
│   │   (React + Vite)      │        │   (React + Vite)      │             │
│   │  Role: Admin          │        │  Role: Manager, Sales │             │
│   └──────────┬────────────┘        └──────────┬────────────┘             │
│              └───────────────┬────────────────┘                          │
│                              │ HTTPS                                      │
├──────────────────────────────┼───────────────────────────────────────────┤
│                         REVERSE PROXY                                     │
│              Nginx  •  SSL Termination  •  Rate Limiting                 │
├──────────────────────────────┼───────────────────────────────────────────┤
│                         APPLICATION LAYER                                 │
│   ┌──────────────────────────────────────────────────────────────────┐   │
│   │                    Backend API  (NestJS + TypeScript)            │   │
│   │                                                                  │   │
│   │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │   │
│   │  │   Auth   │ │  Users   │ │ Outlets  │ │ Products │           │   │
│   │  │  Module  │ │  Module  │ │  Module  │ │  Module  │           │   │
│   │  └──────────┘ └──────────┘ └──────────┘ └──────────┘           │   │
│   │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │   │
│   │  │  Routes  │ │  Visits  │ │  Orders  │ │Dashboard │           │   │
│   │  │  Module  │ │  Module  │ │  Module  │ │  Module  │           │   │
│   │  └──────────┘ └──────────┘ └──────────┘ └──────────┘           │   │
│   │  ┌──────────┐                                                    │   │
│   │  │  Files   │  (MinIO client)                                    │   │
│   │  │  Module  │                                                    │   │
│   │  └──────────┘                                                    │   │
│   └──────────────────────────────────────────────────────────────────┘   │
├──────────────────────────────┼───────────────────────────────────────────┤
│                           DATA LAYER                                      │
│   ┌──────────┐    ┌──────────┐    ┌──────────────────────┐              │
│   │  MySQL   │    │  Redis   │    │       MinIO          │              │
│   │ (Prisma) │    │  Cache   │    │  (Visit Photos &     │              │
│   │          │    │  + JWT   │    │   Static Assets)     │              │
│   │ Primary  │    │ Blacklist│    │                      │              │
│   └──────────┘    └──────────┘    └──────────────────────┘              │
├─────────────────────────────────────────────────────────────────────────-┤
│                       EXTERNAL INTEGRATIONS                               │
│                   Google Maps API  (Route Optimization)                   │
└──────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Architecture Diagram (Mermaid)

```mermaid
flowchart TB
    subgraph Clients
        ADMIN["Admin App\n(React + Vite)\nRole: Admin"]
        USER["User App\n(React + Vite)\nRole: Manager / Sales"]
    end

    subgraph Gateway
        NGINX["Nginx\nReverse Proxy + SSL"]
    end

    subgraph API["Backend API (NestJS)"]
        AUTH[Auth Module]
        USERS[Users Module]
        OUTLETS[Outlets Module]
        PRODUCTS[Products Module]
        ROUTES[Routes Module]
        VISITS[Visits Module]
        ORDERS[Orders Module]
        DASH[Dashboard Module]
        FILES[Files Module]
    end

    subgraph Data
        DB[(MySQL\nvia Prisma)]
        CACHE[(Redis\nCache + JWT Blacklist)]
        STORAGE[(MinIO\nObject Storage)]
    end

    subgraph External
        GMAPS[Google Maps API]
    end

    ADMIN --> NGINX
    USER --> NGINX
    NGINX --> AUTH
    NGINX --> USERS
    NGINX --> OUTLETS
    NGINX --> PRODUCTS
    NGINX --> ROUTES
    NGINX --> VISITS
    NGINX --> ORDERS
    NGINX --> DASH
    NGINX --> FILES

    AUTH --> DB
    AUTH --> CACHE
    USERS --> DB
    OUTLETS --> DB
    PRODUCTS --> DB
    ROUTES --> DB
    ROUTES --> GMAPS
    ROUTES --> CACHE
    VISITS --> DB
    VISITS --> FILES
    ORDERS --> DB
    DASH --> DB
    DASH --> CACHE
    FILES --> STORAGE
```

### 2.3 Deployment Architecture (Single VPS)

```
┌─────────────────────────────────────────────────────────┐
│                  VPS (Production)                        │
│                                                          │
│   ┌─────────────────────────────────────────────────┐   │
│   │              Docker Compose Stack                │   │
│   │                                                  │   │
│   │  ┌──────────┐  ┌──────────┐  ┌──────────┐      │   │
│   │  │  nginx   │  │   api    │  │  mysql   │      │   │
│   │  │ :80/:443 │  │  :3000   │  │  :3306   │      │   │
│   │  └──────────┘  └──────────┘  └──────────┘      │   │
│   │  ┌──────────┐  ┌──────────┐                     │   │
│   │  │  redis   │  │  minio   │                     │   │
│   │  │  :6379   │  │  :9000   │                     │   │
│   │  └──────────┘  └──────────┘                     │   │
│   └─────────────────────────────────────────────────┘   │
│                                                          │
│   SSL: Let's Encrypt via Certbot                        │
└─────────────────────────────────────────────────────────┘
```

---

## 3. Technology Stack

### 3.1 Core Technologies

| Layer | Teknologi | Versi | Kegunaan |
|-------|-----------|-------|----------|
| **Frontend** | React | 18.x | UI library untuk Admin App & User App |
| **Frontend Build** | Vite | 5.x | Build tool & dev server |
| **Frontend Styling** | Tailwind CSS | 3.x | Utility-first CSS framework |
| **Frontend Language** | TypeScript | 5.x | Type-safe JavaScript |
| **Backend Framework** | NestJS | 10.x | Node.js framework modular berbasis TypeScript |
| **Backend Runtime** | Node.js | 20.x LTS | JavaScript runtime |
| **ORM** | Prisma | 5.x | Type-safe ORM untuk MySQL |
| **Database** | MySQL | 8.x | Primary relational database |
| **Cache** | Redis | 7.x | Caching, JWT blacklist |
| **File Storage** | MinIO | Latest | Self-hosted S3-compatible object storage |
| **Reverse Proxy** | Nginx | Latest | SSL termination, routing, rate limiting |

### 3.2 External Integrations

| Integrasi | Provider | Kegunaan |
|-----------|----------|----------|
| Route Optimization | Google Maps Platform (Routes API / Directions API) | Menghitung urutan kunjungan outlet yang optimal |
| Geocoding | Google Maps Platform (Geocoding API) | Konversi alamat outlet ke koordinat GPS |

### 3.3 Supporting Libraries

| Kategori | Library | Kegunaan |
|----------|---------|----------|
| Auth | `@nestjs/passport`, `passport-jwt` | JWT strategy & guard |
| Validation | `class-validator`, `class-transformer` | Input validation & DTO transformation |
| Hashing | `bcrypt` | Password hashing |
| File Upload | `@nestjs/platform-express`, `multer` | Multipart file upload handling |
| MinIO Client | `minio` | S3-compatible storage client |
| Config | `@nestjs/config` | Environment variable management |
| API Docs | `@nestjs/swagger` | OpenAPI / Swagger auto-generation |
| Testing | `jest`, `supertest` | Unit & integration testing |
| Frontend HTTP | `axios` / `TanStack Query` | API calls & server-state management |
| Frontend Router | `React Router v6` | Client-side routing |

### 3.4 Development Tools

| Tool | Kegunaan |
|------|----------|
| Docker & Docker Compose | Containerisasi semua layanan |
| GitLab | Version control & CI/CD pipeline |
| pnpm | Package manager (monorepo) |
| ESLint + Prettier | Code linting & formatting |
| Husky + lint-staged | Pre-commit hooks |

---

## 4. Data Architecture

### 4.1 Database Strategy

| Aspek | Keputusan |
|-------|-----------|
| Tipe Database | Relasional (MySQL 8.x) |
| ORM / Migration | Prisma Migrate |
| Charset | `utf8mb4` (mendukung emoji & karakter khusus) |
| Backup Strategy | Daily automated backup ke file / remote storage |
| Connection Pooling | Prisma connection pool (default) |

### 4.2 Core Entities (High-Level)

```mermaid
erDiagram
    users ||--o{ sales_plans : "membuat"
    users ||--o{ visits : "melakukan"
    users ||--o{ orders : "membuat"

    users {
        uuid id PK
        string name
        string email
        string password_hash
        enum role "admin | manager | sales"
        enum status "active | inactive"
        timestamp created_at
        timestamp updated_at
    }

    outlets ||--o{ plan_outlets : "termasuk dalam"
    outlets ||--o{ visits : "dikunjungi"
    outlets {
        uuid id PK
        string name
        string address
        decimal latitude
        decimal longitude
        enum status "active | inactive"
        timestamp created_at
        timestamp updated_at
    }

    products ||--o{ order_items : "dipesan"
    products {
        uuid id PK
        string name
        string sku
        string unit
        decimal price
        enum status "active | inactive"
        timestamp created_at
        timestamp updated_at
    }

    sales_plans ||--o{ plan_outlets : "berisi"
    sales_plans {
        uuid id PK
        uuid user_id FK
        date plan_date
        enum status "draft | active | completed"
        timestamp created_at
    }

    plan_outlets {
        uuid id PK
        uuid plan_id FK
        uuid outlet_id FK
        int visit_order
        enum status "pending | visited | skipped"
    }

    visits ||--o{ orders : "menghasilkan"
    visits {
        uuid id PK
        uuid plan_outlet_id FK
        uuid user_id FK
        uuid outlet_id FK
        decimal checkin_lat
        decimal checkin_lng
        string photo_url
        text notes
        timestamp checkin_at
        timestamp checkout_at
    }

    orders ||--o{ order_items : "berisi"
    orders {
        uuid id PK
        uuid visit_id FK
        uuid user_id FK
        uuid outlet_id FK
        enum status "pending | confirmed | cancelled"
        text notes
        timestamp created_at
    }

    order_items {
        uuid id PK
        uuid order_id FK
        uuid product_id FK
        int quantity
        decimal unit_price
    }
```

> **Catatan:** Schema detail per fitur (index, constraint, soft delete) didefinisikan di SPEC-Technical masing-masing fitur.

### 4.3 Caching Strategy

| Data | Cache Layer | TTL | Invalidasi |
|------|-------------|-----|------------|
| JWT blacklist (logout) | Redis | Sisa masa berlaku token | On logout |
| User session data | Redis | 7 hari (match refresh token) | On logout / password change |
| Route optimization result | Redis | 24 jam | Ketika daftar outlet berubah |
| Dashboard aggregate stats | Redis | 5 menit | Rolling expire |

### 4.4 File Storage Strategy (MinIO)

| Jenis File | Bucket | Path Pattern | Access |
|-----------|--------|--------------|--------|
| Foto kunjungan | `visit-photos` | `/{user_id}/{visit_id}/{timestamp}.jpg` | Private (pre-signed URL) |
| Foto thumbnail | `visit-photos` | `/{user_id}/{visit_id}/{timestamp}_thumb.jpg` | Private (pre-signed URL) |

- File diakses melalui **pre-signed URL** dengan TTL 1 jam — URL tidak bisa dibagikan sembarangan
- Kompresi & resize gambar dilakukan server-side sebelum disimpan ke MinIO

---

## 5. API Architecture

### 5.1 API Conventions

| Aspek | Standar |
|-------|---------|
| Style | REST |
| Base URL | `/api/v1` |
| Versioning | URL path (`/v1`) |
| Naming | kebab-case, plural nouns |
| Autentikasi | Bearer Token (JWT) |
| Dokumentasi | OpenAPI 3.0 via `@nestjs/swagger` |
| Content-Type | `application/json` (kecuali file upload: `multipart/form-data`) |

### 5.2 API Endpoint Overview

| Module | Endpoint Prefix | Deskripsi |
|--------|----------------|-----------|
| Auth | `/api/v1/auth` | Login, refresh token, logout |
| Users | `/api/v1/users` | CRUD user, assign role |
| Outlets | `/api/v1/outlets` | CRUD outlet + koordinat GPS |
| Products | `/api/v1/products` | CRUD katalog produk |
| Plans | `/api/v1/plans` | Rencana rute harian |
| Visits | `/api/v1/visits` | Check-in, checkout, foto |
| Orders | `/api/v1/orders` | Order digital per kunjungan |
| Dashboard | `/api/v1/dashboard` | Statistik & monitoring data |
| Files | `/api/v1/files` | Upload foto, generate pre-signed URL |

### 5.3 API Documentation

| Environment | URL |
|-------------|-----|
| Local | `http://localhost:3000/api/docs` |
| Staging | `https://staging-api.[domain]/api/docs` |

### 5.4 Standard Response Format

**Success:**
```json
{
  "success": true,
  "data": { "..." },
  "meta": { "page": 1, "limit": 20, "total": 100 }
}
```

**Error:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Pesan error yang dapat dibaca pengguna",
    "details": [{ "field": "email", "message": "Email tidak valid" }]
  }
}
```

---

## 6. Security Architecture

### 6.1 Authentication & Authorization

| Aspek | Implementasi |
|-------|--------------|
| Auth Method | JWT (Access Token + Refresh Token) |
| Access Token TTL | 15 menit |
| Refresh Token TTL | 7 hari |
| Password Hashing | bcrypt (cost factor 12) |
| JWT Storage | Access token: memory (React state); Refresh token: HttpOnly cookie |
| Token Blacklist | Redis (menyimpan revoked token hingga expired) |
| Authorization | Role-Based Access Control (RBAC) via NestJS Guard |

### 6.2 Role Definitions

| Role | Deskripsi | Akses |
|------|-----------|-------|
| `admin` | Administrator sistem | Admin App: full access ke semua data master |
| `manager` | Sales Manager / Head of Sales | User App: dashboard, laporan, monitoring semua Sales |
| `sales` | Sales Representative | User App: rute, kunjungan, order milik sendiri |

### 6.3 Security Measures

- [x] HTTPS everywhere (TLS via Let's Encrypt)
- [x] Rate limiting per IP via Nginx
- [x] Input validation dengan `class-validator` di semua DTO
- [x] SQL injection prevention via Prisma (parameterized queries)
- [x] XSS prevention via output encoding di frontend
- [x] CORS dikonfigurasi hanya untuk domain yang diizinkan
- [x] Security headers: `helmet` middleware (CSP, HSTS, X-Frame-Options, dll.)
- [x] File upload validation: tipe MIME dan ukuran maksimum
- [x] Pre-signed URL untuk akses foto (tidak expose bucket public)
- [x] Audit log untuk operasi sensitif (login, logout, data master changes)

---

## 7. Development Environment

### 7.1 Project Structure (Monorepo)

```
rutego/
├── apps/
│   ├── api/              # NestJS Backend
│   │   ├── src/
│   │   │   ├── modules/
│   │   │   │   ├── auth/
│   │   │   │   ├── users/
│   │   │   │   ├── outlets/
│   │   │   │   ├── products/
│   │   │   │   ├── plans/
│   │   │   │   ├── visits/
│   │   │   │   ├── orders/
│   │   │   │   ├── dashboard/
│   │   │   │   └── files/
│   │   │   ├── common/   # Guards, interceptors, decorators, pipes
│   │   │   ├── config/   # Config module & env validation
│   │   │   └── main.ts
│   │   ├── prisma/
│   │   │   └── schema.prisma
│   │   ├── Dockerfile
│   │   └── test/
│   ├── admin/            # React + Vite (Admin App)
│   │   ├── src/
│   │   └── Dockerfile
│   └── user/             # React + Vite (User App)
│       ├── src/
│       └── Dockerfile
├── nginx/
│   └── nginx.conf
├── docker-compose.yml        # Local dev — infra only
├── docker-compose.prod.yml   # Production — full stack
├── .env.example
└── pnpm-workspace.yaml
```

### 7.2 Quick Start (Local Development)

Untuk lokal, hanya infrastruktur yang berjalan di Docker (MySQL, Redis, MinIO). Aplikasi dijalankan langsung di host untuk mendapatkan hot reload.

```bash
# 1. Clone repository
git clone [gitlab-repo-url]
cd rutego

# 2. Setup environment variables
cp .env.example .env
# Edit .env jika ada nilai yang perlu disesuaikan

# 3. Start infrastruktur (MySQL, Redis, MinIO)
docker compose up -d

# 4. Install dependencies (semua apps sekaligus via pnpm workspace)
pnpm install

# 5. Jalankan database migration
pnpm --filter api prisma migrate dev

# 6. Seed data awal (admin user + sample data)
pnpm --filter api prisma db seed

# 7. Start semua dev server
pnpm --filter api dev    # API       → http://localhost:3000
                         # Swagger   → http://localhost:3000/api/docs
pnpm --filter admin dev  # Admin App → http://localhost:5173
pnpm --filter user dev   # User App  → http://localhost:5174

# MinIO Console (browser) → http://localhost:9001
# Credentials: minioadmin / minioadmin123
```

### 7.3 `docker-compose.yml` (Local Dev — Infrastruktur)

File ini menjalankan **infrastruktur saja**. Aplikasi (API, Admin App, User App) dijalankan langsung di host.

```yaml
services:
  mysql:
    image: mysql:8.0
    container_name: rutego-mysql
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD:-root}
      MYSQL_DATABASE: ${MYSQL_DATABASE:-rutego}
      MYSQL_USER: ${MYSQL_USER:-rutego}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD:-rutego123}
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "root", "-p${MYSQL_ROOT_PASSWORD:-root}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s

  redis:
    image: redis:7-alpine
    container_name: rutego-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  minio:
    image: minio/minio:latest
    container_name: rutego-minio
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: ${MINIO_ROOT_USER:-minioadmin}
      MINIO_ROOT_PASSWORD: ${MINIO_ROOT_PASSWORD:-minioadmin123}
    ports:
      - "9000:9000"   # S3 API
      - "9001:9001"   # MinIO Console
    volumes:
      - minio_data:/data
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 15s
      timeout: 10s
      retries: 5
      start_period: 15s

  minio-init:
    image: minio/mc:latest
    container_name: rutego-minio-init
    depends_on:
      minio:
        condition: service_healthy
    entrypoint: >
      /bin/sh -c "
        mc alias set local http://minio:9000 $${MINIO_ROOT_USER:-minioadmin} $${MINIO_ROOT_PASSWORD:-minioadmin123};
        mc mb --ignore-existing local/$${MINIO_BUCKET_PHOTOS:-visit-photos};
        mc anonymous set private local/$${MINIO_BUCKET_PHOTOS:-visit-photos};
        echo 'MinIO buckets ready.';
      "
    restart: on-failure

volumes:
  mysql_data:
  redis_data:
  minio_data:
```

### 7.4 `docker-compose.prod.yml` (Production — Full Stack)

File ini menjalankan **seluruh stack** di VPS production. Aplikasi dijalankan sebagai Docker container dari image yang sudah di-build oleh GitLab CI.

```yaml
services:
  nginx:
    image: nginx:alpine
    container_name: rutego-nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/certs:/etc/nginx/certs:ro   # Let's Encrypt certificates
    depends_on:
      - api
    restart: unless-stopped

  api:
    image: registry.gitlab.com/[group]/rutego/api:${API_IMAGE_TAG:-latest}
    container_name: rutego-api
    environment:
      NODE_ENV: production
      DATABASE_URL: ${DATABASE_URL}
      REDIS_URL: ${REDIS_URL}
      JWT_SECRET: ${JWT_SECRET}
      JWT_REFRESH_SECRET: ${JWT_REFRESH_SECRET}
      JWT_EXPIRES_IN: ${JWT_EXPIRES_IN}
      JWT_REFRESH_EXPIRES_IN: ${JWT_REFRESH_EXPIRES_IN}
      MINIO_ENDPOINT: minio
      MINIO_PORT: 9000
      MINIO_USE_SSL: "false"
      MINIO_ACCESS_KEY: ${MINIO_ACCESS_KEY}
      MINIO_SECRET_KEY: ${MINIO_SECRET_KEY}
      MINIO_BUCKET_PHOTOS: ${MINIO_BUCKET_PHOTOS:-visit-photos}
      MINIO_PRESIGNED_EXPIRY: ${MINIO_PRESIGNED_EXPIRY:-3600}
      GOOGLE_MAPS_API_KEY: ${GOOGLE_MAPS_API_KEY}
    depends_on:
      mysql:
        condition: service_healthy
      redis:
        condition: service_healthy
    restart: unless-stopped

  mysql:
    image: mysql:8.0
    container_name: rutego-mysql
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE:-rutego}
      MYSQL_USER: ${MYSQL_USER:-rutego}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    container_name: rutego-redis
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  minio:
    image: minio/minio:latest
    container_name: rutego-minio
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: ${MINIO_ROOT_USER}
      MINIO_ROOT_PASSWORD: ${MINIO_ROOT_PASSWORD}
    volumes:
      - minio_data:/data
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 15s
      timeout: 10s
      retries: 5
      start_period: 15s
    restart: unless-stopped

  minio-init:
    image: minio/mc:latest
    depends_on:
      minio:
        condition: service_healthy
    entrypoint: >
      /bin/sh -c "
        mc alias set local http://minio:9000 $${MINIO_ROOT_USER} $${MINIO_ROOT_PASSWORD};
        mc mb --ignore-existing local/$${MINIO_BUCKET_PHOTOS:-visit-photos};
        mc anonymous set private local/$${MINIO_BUCKET_PHOTOS:-visit-photos};
      "
    restart: on-failure

volumes:
  mysql_data:
  redis_data:
  minio_data:
```

### 7.5 `.env.example`

```env
# =============================================================================
# RuteGo — Environment Variables
# Copy this file to .env and fill in the values.
# Never commit .env to version control.
# =============================================================================

# Application
NODE_ENV=development
API_PORT=3000

# Database (MySQL)
MYSQL_DATABASE=rutego
MYSQL_USER=rutego
MYSQL_PASSWORD=rutego123
MYSQL_ROOT_PASSWORD=root
DATABASE_URL="mysql://rutego:rutego123@localhost:3306/rutego"

# Cache (Redis)
REDIS_URL=redis://localhost:6379

# Auth (JWT) — ubah ke string acak yang panjang di production
JWT_SECRET=change-me-to-a-very-long-random-secret-key-for-access-token
JWT_REFRESH_SECRET=change-me-to-another-long-random-secret-key-for-refresh-token
JWT_EXPIRES_IN=15m
JWT_REFRESH_EXPIRES_IN=7d

# File Storage (MinIO)
MINIO_ENDPOINT=localhost
MINIO_PORT=9000
MINIO_USE_SSL=false
MINIO_ROOT_USER=minioadmin
MINIO_ROOT_PASSWORD=minioadmin123
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin123
MINIO_BUCKET_PHOTOS=visit-photos
MINIO_PRESIGNED_EXPIRY=3600

# External APIs
GOOGLE_MAPS_API_KEY=your-google-maps-api-key-here

# Frontend (Vite) — prefix VITE_ wajib agar di-expose ke browser
VITE_API_BASE_URL=http://localhost:3000/api/v1
```

### 7.6 `pnpm-workspace.yaml`

```yaml
packages:
  - 'apps/*'
```

### 7.7 Dockerfiles (Production Build)

**`apps/api/Dockerfile`**

```dockerfile
# ---- Build stage ----
FROM node:20-alpine AS builder
WORKDIR /app

RUN npm install -g pnpm
COPY package.json pnpm-lock.yaml* ./
RUN pnpm install --frozen-lockfile

COPY . .
RUN pnpm prisma generate
RUN pnpm build

# ---- Production stage ----
FROM node:20-alpine AS runner
WORKDIR /app

ENV NODE_ENV=production

RUN npm install -g pnpm
COPY package.json pnpm-lock.yaml* ./
RUN pnpm install --frozen-lockfile --prod

COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules/.prisma ./node_modules/.prisma
COPY prisma ./prisma

EXPOSE 3000
CMD ["node", "dist/main"]
```

**`apps/admin/Dockerfile`** dan **`apps/user/Dockerfile`** (identik, ganti nama app)

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app

RUN npm install -g pnpm
COPY package.json pnpm-lock.yaml* ./
RUN pnpm install --frozen-lockfile

COPY . .
RUN pnpm build   # Output: dist/

# Nginx serves static build output
FROM nginx:alpine AS runner
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.app.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 7.8 `nginx/nginx.conf` (Production)

```nginx
events {
  worker_connections 1024;
}

http {
  include       /etc/nginx/mime.types;
  default_type  application/octet-stream;

  gzip on;
  gzip_types text/plain text/css application/json application/javascript text/xml;

  server {
    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
  }

  server {
    listen 443 ssl;
    server_name admin.[domain];

    ssl_certificate     /etc/nginx/certs/fullchain.pem;
    ssl_certificate_key /etc/nginx/certs/privkey.pem;

    root /usr/share/nginx/html/admin;
    index index.html;

    location / {
      try_files $uri $uri/ /index.html;
    }
  }

  server {
    listen 443 ssl;
    server_name app.[domain];

    ssl_certificate     /etc/nginx/certs/fullchain.pem;
    ssl_certificate_key /etc/nginx/certs/privkey.pem;

    root /usr/share/nginx/html/user;
    index index.html;

    location / {
      try_files $uri $uri/ /index.html;
    }
  }

  server {
    listen 443 ssl;
    server_name api.[domain];

    ssl_certificate     /etc/nginx/certs/fullchain.pem;
    ssl_certificate_key /etc/nginx/certs/privkey.pem;

    location / {
      proxy_pass         http://api:3000;
      proxy_http_version 1.1;
      proxy_set_header   Upgrade $http_upgrade;
      proxy_set_header   Connection 'upgrade';
      proxy_set_header   Host $host;
      proxy_set_header   X-Real-IP $remote_addr;
      proxy_cache_bypass $http_upgrade;
    }
  }
}
```

### 7.9 Perintah Umum

| Perintah | Deskripsi |
|---------|-----------|
| `docker compose up -d` | Start infrastruktur lokal (MySQL, Redis, MinIO) |
| `docker compose down` | Stop infrastruktur lokal |
| `docker compose logs -f mysql` | Tail log MySQL |
| `pnpm install` | Install semua dependencies (semua apps) |
| `pnpm --filter api dev` | Start API dev server + hot reload |
| `pnpm --filter admin dev` | Start Admin App dev server |
| `pnpm --filter user dev` | Start User App dev server |
| `pnpm --filter api prisma migrate dev` | Buat & jalankan migrasi baru |
| `pnpm --filter api prisma migrate deploy` | Jalankan migrasi di staging/prod |
| `pnpm --filter api prisma db seed` | Seed data awal |
| `pnpm --filter api test` | Unit tests |
| `pnpm --filter api test:e2e` | End-to-end tests |

---

## 8. DevOps & Deployment

### 8.1 Environments

| Environment | Tujuan | Branch | Config |
|-------------|--------|--------|--------|
| Development | Pengembangan lokal | `feature/*`, `develop` | `.env` lokal |
| Staging | Testing & UAT | `develop` | `.env.staging` di VPS staging |
| Production | Live system | `main` | `.env.production` di VPS production |

### 8.2 CI/CD Pipeline (GitLab CI)

```mermaid
flowchart LR
    A[Push to Branch] --> B[Lint & Type Check]
    B --> C[Unit Tests]
    C --> D{Branch?}
    D -->|feature/*| E[Done - Review needed]
    D -->|develop| F[Build Docker Image]
    F --> G[Deploy to Staging]
    G --> H[Smoke Test]
    D -->|main| I[Build Docker Image]
    I --> J[Manual Approval]
    J --> K[Deploy to Production]
    K --> L[Smoke Test]
```

**Stages GitLab CI:**
```yaml
stages:
  - lint
  - test
  - build
  - deploy
  - verify
```

### 8.3 Branching Strategy

| Branch | Tujuan | Deploy ke |
|--------|--------|-----------|
| `main` | Kode production-ready | Production (manual trigger) |
| `develop` | Integration branch | Staging (auto) |
| `feature/*` | Feature development | — |
| `fix/*` | Bug fixes | — |
| `hotfix/*` | Production fixes kritis | Production (setelah merge ke main) |

### 8.4 Deployment Process (Production)

```bash
# Di VPS Production (via GitLab CI / manual)
git pull origin main
docker compose -f docker-compose.prod.yml pull
docker compose -f docker-compose.prod.yml up -d --no-deps api
docker exec rutego-api pnpm prisma migrate deploy
```

---

## 9. Coding Standards

### 9.1 Naming Conventions

| Item | Konvensi | Contoh |
|------|----------|--------|
| Files (BE) | kebab-case | `user.service.ts`, `auth.guard.ts` |
| Files (FE) | PascalCase (components), kebab-case (utils) | `VisitCard.tsx`, `use-auth.ts` |
| Classes | PascalCase | `UserService`, `CreateUserDto` |
| Functions / Methods | camelCase | `createUser()`, `findByEmail()` |
| Variables | camelCase | `userData`, `planOutlets` |
| Constants | SCREAMING_SNAKE_CASE | `MAX_FILE_SIZE`, `JWT_EXPIRES_IN` |
| Database tables | snake_case | `sales_plans`, `order_items` |
| API endpoints | kebab-case, plural | `/api/v1/sales-plans`, `/api/v1/order-items` |
| Enum values | SCREAMING_SNAKE_CASE | `ROLE_ADMIN`, `STATUS_ACTIVE` |

### 9.2 Commit Convention (Conventional Commits)

```
<type>(<scope>): <subject>

Types:
  feat      - Fitur baru
  fix       - Bug fix
  docs      - Perubahan dokumentasi
  refactor  - Refactoring kode (bukan fix/feat)
  test      - Menambah atau memperbaiki test
  chore     - Maintenance (deps update, config, dll.)
  style     - Formatting (tidak mengubah logika)
  perf      - Peningkatan performa

Contoh:
  feat(visits): add GPS check-in validation with radius tolerance
  fix(auth): resolve refresh token rotation race condition
  docs(api): update swagger docs for orders endpoint
```

### 9.3 Code Review Checklist

- [ ] Mengikuti coding standards dan naming conventions
- [ ] Unit test ditulis untuk business logic baru
- [ ] Tidak ada logic/credential sensitif di kode (gunakan env var)
- [ ] Input validation ada di semua endpoint yang menerima data
- [ ] Error handling tidak membocorkan stack trace ke client
- [ ] Performa: tidak ada N+1 query (gunakan Prisma `include`)
- [ ] Dokumentasi Swagger diperbarui (jika ada endpoint baru/berubah)

---

## 10. Persetujuan (Approval)

| Peran | Nama | Tanda Tangan | Tanggal |
|-------|------|--------------|---------|
| Tech Lead | TBD | | |
| Product Manager | TBD | | |

---

## Dokumen Terkait

| Dokumen | Deskripsi |
|---------|-----------|
| `docs/project/01-BRD.md` | Business Requirements Document |
| `docs/project/02-PEP.md` | Project Execution Plan |
| `docs/features/*/XX-feature---technical.md` | Technical spec per fitur |
| `apps/api/prisma/schema.prisma` | Prisma database schema |
| `apps/api/src/main.ts` | API entry point & Swagger setup |
| `.env.example` | Environment variable template |
