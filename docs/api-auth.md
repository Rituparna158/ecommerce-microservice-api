# Auth Service API

**Base URL:** `http://localhost:4003`
**Gateway Prefix:** `/api/auth`

---

## POST `/auth/register`

Register a new user account. Creates user in auth DB, creates user profile via User service, generates a verification code, and sends a verification email.

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `name` | string | Yes | 3–100 characters |
| `email` | string | Yes | Valid email |
| `password` | string | Yes | 6–100 characters |

### Example Request

```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "secret123"
}
```

### Responses

**201 Created**
```json
{
  "message": "User created, check your email for verification code",
  "user": {
    "id": "clxyz...",
    "name": "John Doe",
    "email": "john@example.com",
    "role": "ADMIN",
    "veryfied": false,
    "status": "PENDING",
    "createdAt": "2025-04-01T00:00:00.000Z"
  }
}
```

**400 Bad Request** — Validation error or user already exists
```json
{ "error": "User already exists" }
```

---

## POST `/auth/login`

Authenticate a user and receive a JWT access token.

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `email` | string | Yes | Valid email |
| `password` | string | Yes | Any string |

### Example Request

```json
{
  "email": "john@example.com",
  "password": "secret123"
}
```

### Responses

**200 OK**
```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

**400 Bad Request** — Invalid credentials, unverified, or inactive account
```json
{ "error": "Invalid Creadentials" }
```

### Notes
- Login attempts (success and failed) are recorded in the history table.
- JWT token expires in 24 hours.

---

## POST `/auth/verify-token`

Verify a JWT access token and return the associated user.

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `token` | string | Yes | JWT string |

### Example Request

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

### Responses

**200 OK**
```json
{
  "user": {
    "id": "clxyz...",
    "email": "john@example.com",
    "name": "John Doe",
    "role": "ADMIN",
    "status": "ACTIVE"
  }
}
```

**401 Unauthorized** — Invalid token or inactive user
```json
{ "message": "Unauthorized" }
```

---

## POST `/auth/verify-email`

Verify a user's email address using the verification code sent during registration.

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `email` | string | Yes | Valid email |
| `code` | string | Yes | Exactly 5 characters |

### Example Request

```json
{
  "email": "john@example.com",
  "code": "48392"
}
```

### Responses

**200 OK**
```json
{ "message": "Email verified" }
```

**400 Bad Request** — Invalid code, expired, or already used
```json
{ "message": "Invalid Verification Code" }
```

### Notes
- Sets user status to `ACTIVE` and `veryfied` to `true`.
- Sends a confirmation email after successful verification.
