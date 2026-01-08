<div align="center">

# 🚀 E-commerce Engine

[![TypeScript](https://img.shields.io/badge/TypeScript-5.3-blue?logo=typescript)](https://typescriptlang.org)
[![NestJS](https://img.shields.io/badge/NestJS-10.3-ea2845?logo=nestjs)](https://nestjs.com)
[![Bun](https://img.shields.io/badge/Bun-1.0+-fbf0df?logo=bun)](https://bun.sh)
[![GraphQL](https://img.shields.io/badge/GraphQL-Mercurius-e10098?logo=graphql)](https://mercurius.dev)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

**A high-performance, headless, API-first e-commerce backend built for scale.**

[Features](#-features) • [Architecture](#-architecture) • [Quick Start](#-quick-start) • [API](#-api-reference) • [Modules](#-modules)

</div>

---

## ✨ Features

<table>
<tr>
<td width="50%">

### 🏗️ Enterprise Architecture
- **Hexagonal/Clean Architecture** with strict layer separation
- **Module-based monolith** for team scalability
- **Strategy Pattern** for all external integrations
- **Context-aware requests** (Channel/Language/Currency)

</td>
<td width="50%">

### ⚡ Performance First
- **Bun runtime** for blazing fast execution
- **Fastify** HTTP adapter (3x faster than Express)
- **DragonflyDB** for sub-ms caching & sessions
- **Typesense** for typo-tolerant search (<10ms)

</td>
</tr>
<tr>
<td>

### 🛒 Full Commerce Stack
- Multi-channel/multi-region storefronts
- Products with variants & bundles
- Server-side persistent carts
- Order state machine with RMA flows
- Multi-warehouse inventory with Redlock

</td>
<td>

### 🔌 Extensible
- Pluggable payment providers
- Configurable tax strategies
- Multiple shipping calculators
- Promotion rule engine
- CLI scaffolding for new modules

</td>
</tr>
</table>

---

## 🏛️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         API Layer                                │
│   ┌─────────────────────┐    ┌─────────────────────┐            │
│   │  GraphQL (Mercurius)│    │   REST (Swagger)    │            │
│   └─────────────────────┘    └─────────────────────┘            │
├─────────────────────────────────────────────────────────────────┤
│                       Domain Modules                             │
│  ┌──────┐ ┌───────┐ ┌──────┐ ┌───────┐ ┌───────┐ ┌──────────┐  │
│  │ Auth │ │Channel│ │Catalog│ │ Cart │ │ Order │ │ Inventory│  │
│  └──────┘ └───────┘ └──────┘ └───────┘ └───────┘ └──────────┘  │
│  ┌───────┐ ┌─────┐ ┌────────┐ ┌─────────┐ ┌───────┐ ┌──────┐  │
│  │Payment│ │ Tax │ │Shipping│ │Promotion│ │ Asset │ │System│  │
│  └───────┘ └─────┘ └────────┘ └─────────┘ └───────┘ └──────┘  │
├─────────────────────────────────────────────────────────────────┤
│                       Infrastructure                             │
│  ┌────────────┐ ┌───────────┐ ┌──────────┐ ┌───────┐ ┌──────┐  │
│  │CockroachDB │ │DragonflyDB│ │ Typesense│ │  NATS │ │MinIO │  │
│  │  (OLTP)    │ │  (Cache)  │ │ (Search) │ │(Events)││(Files)│  │
│  └────────────┘ └───────────┘ └──────────┘ └───────┘ └──────┘  │
└─────────────────────────────────────────────────────────────────┘
```

<details>
<summary><strong>📁 Project Structure</strong></summary>

```
src/
├── main.ts                 # Application entry point
├── app.module.ts           # Root module
├── config/                 # Configuration
├── common/                 # Shared utilities
│   ├── context/            # Request context (Channel/Lang/Currency)
│   ├── decorators/         # Custom decorators (@Permissions, @Idempotent)
│   ├── guards/             # Auth guards (JWT, RBAC, API Key)
│   ├── filters/            # Global exception filter
│   ├── interceptors/       # Logging, Idempotency
│   └── interfaces/         # Shared types
├── infrastructure/
│   ├── database/           # MikroORM + CockroachDB
│   ├── cache/              # DragonflyDB
│   ├── search/             # Typesense
│   ├── messaging/          # NATS JetStream
│   ├── storage/            # MinIO/S3
│   ├── queue/              # BullMQ
│   └── graphql/            # Mercurius setup
├── modules/
│   ├── auth/               # JWT, RBAC, Customer Groups
│   ├── channel/            # Multi-channel, Regions
│   ├── catalog/            # Products, Categories, Variants
│   ├── cart/               # Shopping cart
│   ├── order/              # Orders, Fulfillment, RMA
│   ├── inventory/          # Multi-warehouse, Reservations
│   ├── payment/            # Payment providers
│   ├── tax/                # Tax calculation
│   ├── shipping/           # Shipping methods
│   ├── promotion/          # Discounts, Vouchers
│   ├── asset/              # File management
│   ├── customer/           # Customer CRUD
│   └── system/             # Health, Audit
├── seed/                   # Mock data generator
└── cli/                    # CLI tools
```

</details>

---

## 🚀 Quick Start

### Prerequisites

- [Bun](https://bun.sh) v1.0+ (or Node.js v22+)
- [Docker](https://docker.com) & Docker Compose

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/ecom-engine.git
cd ecom-engine

# Install dependencies
bun install

# Copy environment file
cp .env.example .env

# Start infrastructure (CockroachDB, DragonflyDB, Typesense, NATS, MinIO)
docker compose up -d

# Run database migrations
bun run db:migrate

# Seed mock data (optional - 10k products, 50k orders, 100k customers)
bun run seed

# Start development server
bun run dev
```

### Verify Installation

```bash
# Health check
curl http://localhost:3000/api/v1/system/health

# Open Swagger UI
open http://localhost:3000/api/docs

# Open GraphQL Playground
open http://localhost:3000/graphql
```

---

## 📚 API Reference

### REST API

All REST endpoints are documented via Swagger at `/api/docs`.

| Endpoint | Description |
|----------|-------------|
| `POST /api/v1/auth/login` | Authenticate user/customer |
| `GET /api/v1/products` | List products with filters |
| `GET /api/v1/products/search?q=` | Search products |
| `POST /api/v1/cart/:id/items` | Add item to cart |
| `POST /api/v1/orders` | Place order |
| `GET /api/v1/inventory/stock/:productId` | Get stock levels |

### GraphQL API

```graphql
# Example: Search products with facets
query SearchProducts($query: String!) {
  searchProducts(input: { query: $query, pagination: { limit: 20 } }) {
    items {
      id
      name
      price { formatted }
      categories { name }
    }
    facets {
      categories { value count }
    }
    total
  }
}

# Example: Place order
mutation PlaceOrder($cartId: ID!, $paymentMethodId: ID!) {
  placeOrder(input: { 
    cartId: $cartId 
    paymentMethodId: $paymentMethodId 
  }) {
    id
    orderNumber
    grandTotal { formatted }
  }
}
```

### Request Headers

```http
x-channel-id: default          # Storefront channel
Accept-Language: en-US         # Language preference  
x-currency: USD                # Currency code
Authorization: Bearer <token>  # For authenticated endpoints
x-idempotency-key: <uuid>      # For mutations (prevents duplicates)
```

---

## 📦 Modules

<details>
<summary><strong>🔐 Auth & Identity</strong></summary>

- JWT + Session authentication (DragonflyDB-backed)
- Granular RBAC permissions (`product:create`, `order:read`)
- Customer groups for segmentation (VIP, Wholesale)
- Password hashing with bcrypt
- Refresh token rotation

</details>

<details>
<summary><strong>🌍 Channel & Region</strong></summary>

- Multiple storefronts (B2B, B2C) sharing inventory
- Regions with specific:
  - Currency
  - Tax provider
  - Payment methods
  - Shipping options
- Automatic context extraction from headers

</details>

<details>
<summary><strong>📦 Catalog (PIM)</strong></summary>

- Product types: Simple, Variable, Bundle
- Infinite-depth categories
- Dynamic product attributes
- Typesense-powered faceted search
- CSV/JSON bulk import with streaming

</details>

<details>
<summary><strong>🛒 Cart & Checkout</strong></summary>

- Server-side persistent carts
- Guest → authenticated merge
- Address management
- Shipping method selection
- Voucher application

</details>

<details>
<summary><strong>📋 Orders & RMA</strong></summary>

- Configurable order state machine
- Draft orders (telephone/B2B sales)
- Fulfillment with tracking
- Returns, Swaps, Claims
- Payment capture/refund

</details>

<details>
<summary><strong>📊 Inventory</strong></summary>

- Multi-warehouse stock tracking
- Ledger-based stock movements
- Redlock-powered reservations
- Low stock alerts

</details>

<details>
<summary><strong>💳 Payments & Tax</strong></summary>

- Strategy pattern for providers
- Auth → Capture → Refund flow
- Flat rate, percentage, or external tax
- Easy integration with Stripe, PayPal, etc.

</details>

<details>
<summary><strong>🎁 Promotions</strong></summary>

- Rule engine with conditions & actions
- Vouchers (fixed/percentage)
- Usage limits (global/per-customer)
- Stackable promotions

</details>

---

## 🛠️ CLI Commands

```bash
# Development
bun run dev              # Start with hot reload
bun run build            # Build for production
bun run start:prod       # Start production server

# Database
bun run db:migrate       # Run pending migrations
bun run db:generate      # Generate new migration

# Data
bun run seed             # Seed mock data

# Scaffolding
bun run scaffold module wishlist   # Generate new module

# Testing
bun run test             # Run all tests
bun run test:e2e         # Run E2E tests
```

---

## ⚙️ Configuration

All configuration via environment variables. See [`.env.example`](.env.example):

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | Server port | `3000` |
| `DATABASE_URL` | CockroachDB connection | - |
| `REDIS_URL` | DragonflyDB connection | - |
| `TYPESENSE_URL` | Typesense connection | - |
| `JWT_SECRET` | JWT signing key | - |
| `MINIO_ENDPOINT` | MinIO/S3 endpoint | - |

---

## 🤝 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**Built with ❤️ using NestJS, GraphQL, and modern TypeScript**

[⬆ Back to top](#-god-level-e-commerce-engine)

</div>
