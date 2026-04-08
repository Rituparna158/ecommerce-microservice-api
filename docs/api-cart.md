# Cart Service API

**Base URL:** `http://localhost:4006`
**Gateway Prefix:** `/api/cart`

The cart service uses **Redis** for storage. Cart sessions have a configurable TTL (default 60 seconds) and are identified by a session ID passed via the `x-cart-session-id` header.

---

## POST `/cart`

Add an item to the cart. Creates a new cart session if one doesn't exist. Validates inventory availability and deducts stock.

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-cart-session-id` | No | Existing cart session ID. A new session is created if not provided. |

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `productId` | string | Yes | Any string |
| `inventoryId` | string | Yes | Any string |
| `quantity` | number | Yes | Minimum 1 |

### Example Request

```json
{
  "productId": "clxyz...",
  "inventoryId": "clxyz...",
  "quantity": 2
}
```

### Responses

**200 OK**
```json
{
  "message": "Item added to cart",
  "cartSesstionId": "550e8400-e29b-41d4-a716-446655440000"
}
```

**400 Bad Request** — Validation error or insufficient inventory
```json
{ "message": "Inventory is not available" }
```

### Notes
- The `x-cart-session-id` is also returned in the response header for new sessions.
- Adding an item automatically deducts inventory via the Inventory service (`PUT /inventorys/:id` with `actionType: OUT`).

---

## GET `/cart/me`

Get all items in the current cart.

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-cart-session-id` | Yes | Cart session ID |

### Responses

**200 OK**
```json
{
  "data": [
    {
      "productId": "clxyz...",
      "inventoryId": "clxyz...",
      "quantity": 2
    }
  ]
}
```

**400 Bad Request** — No session ID or session expired
```json
{ "data": [] }
```

**404 Not Found** — Session exists but cart is empty
```json
{ "message": "Cart not found" }
```

---

## GET `/cart/clear`

Clear all items from the cart and delete the session.

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-cart-session-id` | Yes | Cart session ID |

### Responses

**200 OK**
```json
{ "message": "Cart cleared successfully" }
```

**400 Bad Request** — No session ID or session expired
```json
{ "data": [] }
```
