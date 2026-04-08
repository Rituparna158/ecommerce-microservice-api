# Inventory Service API

**Base URL:** `http://localhost:4002`
**Gateway Prefix:** `/api/inventorys`

---

## POST `/inventorys`

Create a new inventory record for a product. Typically called internally by the Products service.

### Request Body

| Field | Type | Required | Default | Validation |
|-------|------|----------|---------|------------|
| `sku` | string | Yes | — | Any string |
| `productId` | string | Yes | — | Any string |
| `quantity` | integer | No | `0` | Integer |

### Example Request

```json
{
  "sku": "SKU001",
  "productId": "clxyz...",
  "quantity": 100
}
```

### Responses

**201 Created**
```json
{
  "id": "clxyz...",
  "quantity": 100
}
```

**400 Bad Request** — Validation error
```json
{ "error": "..." }
```

---

## GET `/inventorys/:id`

Get the current quantity for an inventory record.

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | Inventory ID |

### Responses

**200 OK**
```json
{
  "quantity": 50
}
```

**404 Not Found**
```json
{ "error": "Inventory not found" }
```

---

## GET `/inventorys/:id/details`

Get full inventory details including stock history.

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | Inventory ID |

### Responses

**200 OK**
```json
{
  "id": "clxyz...",
  "sku": "SKU001",
  "productId": "clxyz...",
  "quantity": 50,
  "createdAt": "2025-04-01T00:00:00.000Z",
  "updatedAt": "2025-04-01T00:00:00.000Z",
  "histories": [
    {
      "id": "clxyz...",
      "actionType": "IN",
      "quantityChange": 100,
      "lastQuantity": 0,
      "newQuantity": 100,
      "inventoryId": "clxyz...",
      "createdAt": "2025-04-01T00:00:00.000Z",
      "updatedAt": "2025-04-01T00:00:00.000Z"
    },
    {
      "id": "clxyz...",
      "actionType": "OUT",
      "quantityChange": 50,
      "lastQuantity": 100,
      "newQuantity": 50,
      "inventoryId": "clxyz...",
      "createdAt": "2025-04-02T00:00:00.000Z",
      "updatedAt": "2025-04-02T00:00:00.000Z"
    }
  ]
}
```

**404 Not Found**
```json
{ "error": "Inventory not found" }
```

---

## PUT `/inventorys/:id`

Update inventory quantity (stock in or stock out). Creates a history entry.

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | Inventory ID |

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `quantity` | integer | Yes | Integer |
| `actionType` | string | Yes | `IN` or `OUT` |

### Example Request

```json
{
  "quantity": 25,
  "actionType": "IN"
}
```

### Responses

**200 OK**
```json
{
  "id": "clxyz...",
  "quantity": 75
}
```

**400 Bad Request** — Validation error or invalid action type
```json
{ "error": "Invalid action type" }
```

**404 Not Found**
```json
{ "error": "Inventory not found" }
```
