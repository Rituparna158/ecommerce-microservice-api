# Order Service API

**Base URL:** `http://localhost:4007`
**Gateway Prefix:** `/api/orders`

---

## POST `/orders/checkout`

Place an order from the current cart. Fetches cart items, resolves product details, calculates totals, creates the order, clears the cart, and sends a confirmation email.

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `userId` | string | Yes | Any string |
| `userName` | string | Yes | Any string |
| `userEmail` | string | Yes | Any string |
| `userPhone` | string | Yes | Any string |
| `userAddress` | string | Yes | Any string |
| `cartSessionId` | string | Yes | Any string |

### Example Request

```json
{
  "userId": "clxyz...",
  "userName": "John Doe",
  "userEmail": "john@example.com",
  "userPhone": "+1234567890",
  "userAddress": "123 Main St",
  "cartSessionId": "550e8400-e29b-41d4-a716-446655440000"
}
```

### Responses

**201 Created**
```json
"Order placed successfully"
```

**400 Bad Request** — Validation error or empty cart
```json
{ "error": "Cart is empty" }
```

### Flow
1. Fetches cart items from Cart service (`GET /cart/me`)
2. Fetches product details from Products service for each item
3. Calculates subtotal, tax (0), and grand total
4. Creates order with order items in the database
5. Clears the cart via Cart service (`GET /cart/clear`)
6. Sends order confirmation email via Email service

---

## GET `/orders`

List all orders.

### Responses

**200 OK**
```json
[
  {
    "id": "clxyz...",
    "userId": "clxyz...",
    "userName": "John Doe",
    "userEmail": "john@example.com",
    "userPhone": "+1234567890",
    "userAddress": "123 Main St",
    "subtotal": 59.98,
    "tax": 0,
    "grandTotal": 59.98,
    "status": "PENDING",
    "createdAt": "2025-04-01T00:00:00.000Z",
    "updatedAt": "2025-04-01T00:00:00.000Z"
  }
]
```

---

## GET `/orders/:id`

Get a single order with its items.

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | Order ID |

### Responses

**200 OK**
```json
{
  "id": "clxyz...",
  "userId": "clxyz...",
  "userName": "John Doe",
  "userEmail": "john@example.com",
  "userPhone": "+1234567890",
  "userAddress": "123 Main St",
  "subtotal": 59.98,
  "tax": 0,
  "grandTotal": 59.98,
  "status": "PENDING",
  "createdAt": "2025-04-01T00:00:00.000Z",
  "updatedAt": "2025-04-01T00:00:00.000Z",
  "orderItems": [
    {
      "id": "clxyz...",
      "orderId": "clxyz...",
      "productId": "clxyz...",
      "productName": "Wireless Mouse",
      "sku": "SKU001",
      "price": 29.99,
      "quantity": 2,
      "total": 59.98
    }
  ]
}
```

**404 Not Found**
```json
{ "error": "Order not found" }
```

### Order Statuses

| Status | Description |
|--------|-------------|
| `PENDING` | Order placed, awaiting processing |
| `COMPLETED` | Order fulfilled |
| `CANCELLED` | Order cancelled |
| `SHIPPED` | Order shipped |
| `DELIVERED` | Order delivered |
| `RETURNED` | Order returned |
