# Ecommerce Microservices

A modular eCommerce backend built with **Node.js**, **Express**, **TypeScript**, and **Prisma**, managed as a **pnpm workspace** monorepo. Each service runs independently and communicates via REST APIs.

---

## Services

| Service | Port | Database | Description |
|---------|------|----------|-------------|
| **API Gateway** | 8081 | — | Single entry point, routes requests to downstream services |
| **Products** | 4001 | PostgreSQL | Product catalog (CRUD) |
| **Inventory** | 4002 | PostgreSQL | Stock tracking and history |
| **Auth** | 4003 | PostgreSQL | Registration, login, JWT, email verification |
| **User** | 4004 | PostgreSQL | User profiles |
| **Email** | 4005 | PostgreSQL | Email sending and logging |
| **Cart** | 4006 | Redis | Shopping cart with TTL expiry |
| **Order** | 4007 | PostgreSQL | Checkout, order management |

---

## Project Structure

```
ecommerce-microservice/
├── package.json                # Root scripts (pnpm workspace)
├── pnpm-workspace.yaml         # Workspace config
├── .npmrc                      # pnpm settings
├── docker-compose.yml          # Infrastructure (postgres, redis, rabbitmq, mailhog)
├── docker-compose.prod.yml     # All app services for production
├── .dockerignore
├── .env.example
├── api-geteway/
│   ├── Dockerfile
│   ├── .env.example
│   ├── package.json
│   └── src/
│       ├── index.ts            # Express app entry
│       ├── config.ts           # Service URL env vars
│       ├── config.json         # Route definitions
│       ├── utils.ts            # Dynamic route registration
│       └── midlwares.ts        # Auth middleware (JWT verify)
├── services/
│   ├── auth/                   # Prisma + JWT + bcrypt
│   ├── user/                   # Prisma
│   ├── products/               # Prisma
│   ├── inventorys/             # Prisma
│   ├── cart/                   # Redis (ioredis)
│   ├── order/                  # Prisma
│   └── email/                  # Prisma + nodemailer
```

---

## Prerequisites

- **Node.js** >= 18
- **pnpm** >= 9
- **Docker** & **Docker Compose**

---

## Quick Start (Development)

### 1. Install dependencies

```bash
pnpm install
```

### 2. Start infrastructure

This starts PostgreSQL, Redis, RabbitMQ, and MailHog in Docker:

```bash
pnpm infra:up
```

### 3. Set up environment variables

Copy `.env.example` to `.env` in each service:

```bash
cp api-geteway/.env.example api-geteway/.env
for svc in auth cart email inventorys order products user; do
  cp services/$svc/.env.example services/$svc/.env
done
```

### 4. Run database migrations (Prisma services)

```bash
pnpm migrate:dev
```

### 5. Start all services locally (with hot-reload)

```bash
pnpm dev
```

Or run individual services:

```bash
pnpm dev:gateway     # API Gateway on :8081
pnpm dev:auth        # Auth service on :4003
pnpm dev:products    # Products service on :4001
pnpm dev:inventory   # Inventory service on :4002
pnpm dev:user        # User service on :4004
pnpm dev:email       # Email service on :4005
pnpm dev:cart        # Cart service on :4006
pnpm dev:order       # Order service on :4007
```

### 6. Stop infrastructure

```bash
pnpm infra:down
```

---

## Production (Docker)

Build and start everything (infrastructure + all app services):

```bash
pnpm prod:up
```

This runs:
```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up --build -d
```

Stop everything:

```bash
pnpm prod:down
```

In production mode:
- Each service is built as a **multi-stage Docker image** (builder → production)
- Prisma services auto-run `prisma migrate deploy` on startup
- Services communicate over Docker network using container names (e.g., `http://auth:4003`)
- The API gateway resolves service URLs from environment variables

---

## All Scripts

| Script | Description |
|--------|-------------|
| `pnpm dev` | Run all services in parallel with hot-reload |
| `pnpm dev:<service>` | Run a single service (`gateway`, `auth`, `cart`, `email`, `inventory`, `order`, `products`, `user`) |
| `pnpm build` | Build all services |
| `pnpm start` | Start all built services (production) |
| `pnpm infra:up` | Start infrastructure containers (postgres, redis, rabbitmq, mailhog) |
| `pnpm infra:down` | Stop infrastructure containers |
| `pnpm prod:up` | Build & start all services + infrastructure in Docker |
| `pnpm prod:down` | Stop all Docker containers |
| `pnpm migrate:dev` | Run Prisma migrations for all services |
| `pnpm docker:build` | Build all Docker images without starting |

---

## API Endpoints

All routes are prefixed with `/api` through the gateway.

### Auth (`/api/auth`)
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/api/auth/register` | No | Register a new user |
| POST | `/api/auth/login` | No | Login and receive JWT |
| POST | `/api/auth/verify-token` | No | Verify a JWT token |
| POST | `/api/auth/verify-email` | No | Verify email address |

### Products (`/api/products`)
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | `/api/products` | Yes | List all products |
| POST | `/api/products` | Yes | Create a product |
| GET | `/api/products/:id` | Yes | Get product by ID |

### Inventory (`/api/inventorys`)
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| PUT | `/api/inventorys/:id` | Yes | Update inventory |
| GET | `/api/inventorys/:id/details` | No | Get inventory details |

### Users (`/api/users`)
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/api/users` | No | Create user profile |
| GET | `/api/users/:id` | Yes | Get user by ID |

### Cart (`/api/cart`)
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/api/cart` | Yes | Add item to cart |
| GET | `/api/cart/me` | Yes | Get current user's cart |
| GET | `/api/cart/clear` | Yes | Clear cart |

### Email (`/api/email`)
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/api/email/send` | Yes | Send an email |
| GET | `/api/emails` | Yes | List sent emails |

### Orders (`/api/orders`)
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/api/orders/checkout` | Yes | Place an order |
| GET | `/api/orders` | Yes | List orders |
| GET | `/api/orders/:id` | Yes | Get order by ID |

### Health Check
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/health` | Gateway health check |

---

## Tech Stack

- **Runtime**: Node.js 18+
- **Language**: TypeScript
- **Framework**: Express.js
- **ORM**: Prisma
- **Cache**: Redis (ioredis)
- **Database**: PostgreSQL
- **Email**: Nodemailer + MailHog
- **Auth**: JWT + bcrypt
- **Package Manager**: pnpm (workspaces)
- **Containerization**: Docker + Docker Compose

---

## License

MIT


