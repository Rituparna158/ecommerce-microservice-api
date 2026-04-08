# Products Service API

**Base URL:** `http://localhost:4001`
**Gateway Prefix:** `/api/products`

---

## GET `/products`

List all products.

### Responses

**200 OK**
```json
{
  "data": [
    {
      "id": "clxyz...",
      "sku": "SKU001",
      "name": "Wireless Mouse",
      "price": 29.99,
      "inventoryId": "clxyz..."
    }
  ]
}
```

---

## GET `/products/:id`

Get product details including inventory stock info.

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | Product ID |

### Responses

**200 OK**
```json
{
  "id": "clxyz...",
  "sku": "SKU001",
  "name": "Wireless Mouse",
  "description": "Ergonomic wireless mouse",
  "price": 29.99,
  "inventoryId": "clxyz...",
  "status": "PUBLISHED",
  "createdAt": "2025-04-01T00:00:00.000Z",
  "updatedAt": "2025-04-01T00:00:00.000Z",
  "stock": 50,
  "stockStatus": "In Stock"
}
```

**404 Not Found**
```json
{ "message": "Product not found" }
```

---

## POST `/products`

Create a new product. Automatically creates an associated inventory record.

### Request Body

| Field | Type | Required | Default | Validation |
|-------|------|----------|---------|------------|
| `sku` | string | Yes | — | 3–10 characters |
| `name` | string | Yes | — | 3–255 characters |
| `description` | string | No | — | Max 100 characters |
| `price` | number | No | `0` | — |
| `status` | string | No | `DRAFT` | One of: `DRAFT`, `PUBLISHED`, `UNPUBLISHED` |

### Example Request

```json
{
  "sku": "SKU001",
  "name": "Wireless Mouse",
  "description": "Ergonomic wireless mouse",
  "price": 29.99,
  "status": "PUBLISHED"
}
```

### Responses

**201 Created**
```json
{
  "id": "clxyz...",
  "sku": "SKU001",
  "name": "Wireless Mouse",
  "description": "Ergonomic wireless mouse",
  "price": 29.99,
  "status": "PUBLISHED",
  "inventoryId": "clxyz...",
  "createdAt": "2025-04-01T00:00:00.000Z",
  "updatedAt": "2025-04-01T00:00:00.000Z"
}
```

**400 Bad Request** — Validation error or duplicate SKU
```json
{ "message": "Product already exists" }
```
