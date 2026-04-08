# Contributing to Ecommerce Microservice API

Thank you for your interest in contributing! This guide will help you get started.

## Getting Started

1. **Fork** the repository
2. **Clone** your fork:
   ```bash
   git clone https://github.com/<your-username>/ecommerce-microservice-api.git
   cd ecommerce-microservice-api
   ```
3. **Install dependencies:**
   ```bash
   pnpm install
   ```
4. **Set up environment variables:**
   - Copy `.env.example` to `.env` in each service:
     ```bash
     cp api-geteway/.env.example api-geteway/.env
     for svc in auth cart email inventorys order products user; do
       cp services/$svc/.env.example services/$svc/.env
     done
     ```
5. **Start infrastructure (PostgreSQL, Redis, RabbitMQ, MailHog):**
   ```bash
   pnpm infra:up
   ```
6. **Run database migrations:**
   ```bash
   pnpm migrate:dev
   ```
7. **Run the dev servers:**
   ```bash
   pnpm dev
   ```
   The API Gateway will be available at [http://localhost:8081](http://localhost:8081).

## Project Structure

```
ecommerce-microservice-api/
├── package.json                # Root scripts (pnpm workspace)
├── pnpm-workspace.yaml         # Workspace config
├── docker-compose.yml          # Infrastructure (postgres, redis, rabbitmq, mailhog)
├── docker-compose.prod.yml     # All app services for production
├── eslint.config.mjs           # ESLint flat config
├── commitlint.config.mjs       # Commitlint config
├── api-geteway/                # API Gateway (port 8081)
│   └── src/
├── services/
│   ├── auth/                   # Auth service (port 4003)
│   ├── user/                   # User service (port 4004)
│   ├── products/               # Products service (port 4001)
│   ├── inventorys/             # Inventory service (port 4002)
│   ├── cart/                   # Cart service (port 4006)
│   ├── order/                  # Order service (port 4007)
│   └── email/                  # Email service (port 4005)
```

## Useful Scripts

| Script | Description |
|--------|-------------|
| `pnpm dev` | Run all services in parallel with hot-reload |
| `pnpm dev:<service>` | Run a single service (`gateway`, `auth`, `cart`, `email`, `inventory`, `order`, `products`, `user`) |
| `pnpm build` | Build all services |
| `pnpm lint` | Run ESLint across all workspaces |
| `pnpm lint:fix` | Auto-fix ESLint issues |
| `pnpm infra:up` | Start infrastructure containers |
| `pnpm infra:down` | Stop infrastructure containers |
| `pnpm prod:up` | Build & start everything in Docker |
| `pnpm prod:down` | Stop all Docker containers |
| `pnpm migrate:dev` | Run Prisma migrations for all services |

## Commit Convention

This project uses [Conventional Commits](https://www.conventionalcommits.org/) enforced by **commitlint** and **Husky**.

Every commit message must follow this format:

```
<type>(optional scope): <description>
```

### Allowed types

| Type       | Description                                       |
| ---------- | ------------------------------------------------- |
| `feat`     | A new feature                                     |
| `fix`      | A bug fix                                         |
| `docs`     | Documentation-only changes                        |
| `style`    | Code style (formatting, missing semicolons, etc.) |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf`     | Performance improvement                           |
| `test`     | Adding or updating tests                          |
| `build`    | Changes to the build system or dependencies       |
| `ci`       | CI configuration changes                          |
| `chore`    | Other changes that don't modify src or test files |
| `revert`   | Revert a previous commit                          |

### Examples

```bash
git commit -m "feat: add dark mode toggle to navbar"
git commit -m "fix: resolve empty avatar src warning"
git commit -m "docs: update API documentation"
git commit -m "refactor(auth): simplify token validation"
```

> Bad commits like `"fixed stuff"` or `"updates"` will be **rejected** by the pre-commit hook.

## Branch Naming

Use descriptive branch names:

```
feat/add-discussion-page
fix/compiler-timeout-issue
docs/update-readme
```

## Pull Request Process

1. Create a new branch from `main`:
   ```bash
   git checkout -b feat/your-feature-name
   ```
2. Make your changes and commit using the convention above.
3. Push to your fork:
   ```bash
   git push origin feat/your-feature-name
   ```
4. Open a **Pull Request** against the `main` branch of [testorg-mindfire/ecommerce-microservice-api](https://github.com/testorg-mindfire/ecommerce-microservice-api).
5. Fill in the PR description explaining **what** you changed and **why**.
6. Wait for review — a maintainer will review and merge.

## Code Guidelines

- **TypeScript** — all new code should be typed.
- **Formatting** — follow the existing code style; ESLint runs as a pre-commit hook via lint-staged.
- **No console.log** in production code — use it only for debugging.
- **Keep PRs focused** — one feature or fix per PR.
- **Environment variables** — never hardcode service URLs; always use env vars with localhost defaults.
- **Prisma** — if you modify a schema, include the migration (`pnpm --filter <service> migrate:dev`).

## Adding a New Service

1. Create a new folder under `services/your-service/`.
2. Add `package.json`, `tsconfig.json`, `Dockerfile`, `.env.example`, and `src/index.ts`.
3. Register the service in `pnpm-workspace.yaml` (already covered by `services/*`).
4. Add routes in `api-geteway/src/config.json` and the service URL in `api-geteway/src/config.ts`.
5. Add the service to `docker-compose.prod.yml`.
6. Update the root `package.json` with a `dev:<service>` script.

## Reporting Issues

- Use [GitHub Issues](https://github.com/testorg-mindfire/ecommerce-microservice-api/issues) to report bugs or request features.
- Include steps to reproduce, expected behavior, and screenshots if applicable.

## License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).
