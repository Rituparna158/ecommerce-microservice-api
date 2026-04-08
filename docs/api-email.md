# Email Service API

**Base URL:** `http://localhost:4005`
**Gateway Prefix:** `/api`

---

## POST `/email/send`

Send an email and log it in the database. Uses SMTP (MailHog in development).

### Request Body

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `recipient` | string | Yes | Valid email |
| `subject` | string | Yes | Any string |
| `body` | string | Yes | Any string |
| `source` | string | Yes | Any string (identifies the caller, e.g. `"checkout"`, `"User Registion"`) |
| `sender` | string | No | Valid email (defaults to `DEFAULT_EMAIL` env var) |

### Example Request

```json
{
  "recipient": "john@example.com",
  "subject": "Order Confirmation",
  "body": "Your order has been placed successfully. Order ID: clxyz...",
  "source": "checkout"
}
```

### Responses

**200 OK**
```json
{ "message": "Email sent successfully" }
```

**400 Bad Request** — Validation error
```json
{ ... }
```

**500 Internal Server Error** — SMTP rejected the email
```json
{ "message": "Failed to send email" }
```

---

## GET `/emails`

List all sent emails from the database.

### Responses

**200 OK**
```json
[
  {
    "id": "clxyz...",
    "sender": "admin@gmail.com",
    "recipient": "john@example.com",
    "subject": "Order Confirmation",
    "body": "Your order has been placed successfully.",
    "source": "checkout",
    "sentAt": "2025-04-01T00:00:00.000Z"
  }
]
```
