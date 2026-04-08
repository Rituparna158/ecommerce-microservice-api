# User Service API

**Base URL:** `http://localhost:4004`
**Gateway Prefix:** `/api/users`

---

## POST `/users`

Create a new user profile. Typically called internally by the Auth service during registration.

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `authUserId` | string | Yes | Non-empty string |
| `name` | string | Yes | Any string |
| `email` | string | Yes | Valid email |
| `phone` | string | No | Any string |
| `address` | string | No | Any string |

### Example Request

```json
{
  "authUserId": "clxyz...",
  "name": "John Doe",
  "email": "john@example.com",
  "phone": "+1234567890",
  "address": "123 Main St"
}
```

### Responses

**201 Created**
```json
{
  "message": "User created successfully",
  "data": {
    "id": "clxyz...",
    "authUserId": "clxyz...",
    "email": "john@example.com",
    "name": "John Doe",
    "phone": "+1234567890",
    "address": "123 Main St",
    "createdAt": "2025-04-01T00:00:00.000Z",
    "updatedAt": "2025-04-01T00:00:00.000Z"
  }
}
```

**400 Bad Request** — Validation error or user already exists
```json
{ "error": "User already exist" }
```

---

## GET `/users/:id`

Get a user profile by ID or by auth user ID.

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | User ID or Auth User ID |

### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `field` | string | Set to `authUserId` to look up by auth user ID instead of profile ID |

### Example Requests

```
GET /users/clxyz...
GET /users/clxyz...?field=authUserId
```

### Responses

**200 OK**
```json
{
  "user": {
    "id": "clxyz...",
    "authUserId": "clxyz...",
    "email": "john@example.com",
    "name": "John Doe",
    "phone": "+1234567890",
    "address": "123 Main St",
    "createdAt": "2025-04-01T00:00:00.000Z",
    "updatedAt": "2025-04-01T00:00:00.000Z"
  }
}
```

**404 Not Found**
```json
{ "error": "User not found" }
```
