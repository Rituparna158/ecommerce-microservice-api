# API Documentation

Complete API reference for all microservices. All endpoints are accessible through the **API Gateway** at `http://localhost:8081` with the `/api` prefix.

## Services

| Service | Docs | Direct URL | Gateway Prefix |
|---------|------|------------|----------------|
| Auth | [api-auth.md](api-auth.md) | `localhost:4003` | `/api/auth` |
| User | [api-user.md](api-user.md) | `localhost:4004` | `/api/users` |
| Products | [api-products.md](api-products.md) | `localhost:4001` | `/api/products` |
| Inventory | [api-inventory.md](api-inventory.md) | `localhost:4002` | `/api/inventorys` |
| Cart | [api-cart.md](api-cart.md) | `localhost:4006` | `/api/cart` |
| Order | [api-order.md](api-order.md) | `localhost:4007` | `/api/orders` |
| Email | [api-email.md](api-email.md) | `localhost:4005` | `/api/email`, `/api/emails` |

## Authentication

Protected endpoints require a JWT token in the `Authorization` header:

```
Authorization: Bearer <token>
```

Obtain a token via `POST /api/auth/login`. The API Gateway's auth middleware verifies the token and forwards user info as headers (`x-user-id`, `x-user-email`, `x-user-name`, `x-user-role`) to downstream services.

## Common Error Responses

**400 Bad Request** — Validation error (Zod)
```json
{ "error": [{ "code": "...", "message": "...", "path": ["field"] }] }
```

**401 Unauthorized** — Missing or invalid JWT
```json
{ "message": "Unauthorized" }
```

**404 Not Found** — Resource not found
```json
{ "error": "Not Found" }
```

**429 Too Many Requests** — Rate limited (100 req/min via gateway)
```json
{ "error": "Too many requests" }
```

**500 Internal Server Error**
```json
{ "error": "Internal Server Error" }
```
