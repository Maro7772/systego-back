# Online Orders API — E-commerce integration (actual status)

> **Last updated:** November 2026
>
> This file documents **what actually exists in the code**, not the proposed design.
> There is an important difference between previous versions of the documentation and reality:
>
> - ❌ There is **no** `POST /api/public/online-orders` for creating an order from the e-commerce platform.
> - ✅ Systego is the one that creates the order via `POST /api/store/order/checkout`.
> - ✅ Systego sends the order to the external platform (Outbound).
> - ✅ The external platform only changes the status via `accept` / `reject` (Inbound).

Base URL: `/api`

---

## 1) Lifecycle overview

```text
Customer
  │  POST /api/store/order/checkout
  ▼
Systego creates Order (status = pending)
  │
  │  POST $ECOMMERCE_WEBHOOK_URL   (best-effort, form-urlencoded)
  ▼
External platform
  │
  │  GET|POST /api/public/orders/accept?id=...   (x-api-key)
  │  GET|POST /api/public/orders/reject?id=...   (x-api-key)
  ▼
Systego updates status + statusHistory + Socket.IO
```

- Systego is the **primary source** of order data (customer, price, shipping, payment).
- The external platform only controls **accepting/rejecting** the order.
- No new order is created from the outside.

---

## 2) Configuration (`config`)

```env
# This tenant's key from Super Systego Back.
# The external platform sends it as x-api-key when calling accept/reject.
SUPER_SYSTEGO_API_KEY=sk_...

# Optional: a different key for the outbound direction only.
# If empty, SUPER_SYSTEGO_API_KEY is used.
ECOMMERCE_WEBHOOK_API_KEY=

# Optional: the external platform's webhook URL.
# The default in the code: https://bcknd.smartego.org/api/webhooks/custom/order
ECOMMERCE_WEBHOOK_URL=
```

Important rules:

- `x-api-key` validation is **local** (constant-time comparison) and does not call Super Systego.
- If `SUPER_SYSTEGO_API_KEY` is not set → accept/reject return **503** (fail closed).
- `ECOMMERCE_API_KEY` is **not used** in the current code despite being mentioned in old documentation.
- Plesk's `X_API_KEY` is **not used** here.

---

## 3) Outbound direction: Systego → platform

It happens automatically after a successful `POST /api/store/order/checkout`, **without waiting for the response**.

```http
POST https://bcknd.smartego.org/api/webhooks/custom/order
Content-Type: application/x-www-form-urlencoded
Accept: application/json
X-API-KEY: <ECOMMERCE_WEBHOOK_API_KEY or SUPER_SYSTEGO_API_KEY>
```

Body (form-urlencoded, not JSON):

```text
id=11088739
customer_name=Ahmed Mahmoud
customer_phone=01206610346
total_price=650
currency=EGP
```

| Field | Source | Notes |
|---|---|---|
| `id` | `order.reference` or `order._id` | The value that the platform later sends in `?id=` |
| `customer_name` | Customer data or `Guest Customer` | — |
| `customer_phone` | `phone_number` or empty | — |
| `total_price` | `totalPriceAfterDiscount` | Number |
| `currency` | `order.currency` | Defaults to `EGP` |

Additional behavior:

- `timeout = 10s`.
- Any failure (network/HTTP) only logs `[externalOrderSync] ...` and does not fail the checkout.
- If the platform responds with any of `id`, `_id`, `order_id`, `orderId` → it is stored in `externalOrderId`
  (provided it is not already present), after which it can use **its own identifier** in accept/reject.

---

## 4) Inbound direction: accepting the order

```http
GET  /api/public/orders/accept?id=11088739
POST /api/public/orders/accept?id=11088739
X-API-KEY: <SUPER_SYSTEGO_API_KEY>
```

### 4.1 Resolving the order

The identifier can be sent in:

- Query: `id`, `order_id`, `orderId`, `reference`
- JSON body: the same names
- Route param (supported in the code, but no route with a parameter currently exists)

Lookup order:

1. `externalOrderId`
2. `reference`
3. `_id` (if the value is a valid ObjectId)

### 4.2 Result

- `pending` → `processing`
- `statusDescription = "Order accepted by the e-commerce platform"`
- Adds an entry to `statusHistory` with `source: "system"`
- Emits `order_status_changed` via Socket.IO
- Does not touch inventory, payment, or shipping

Response:

```json
{
  "success": true,
  "data": {
    "message": "Order accepted successfully",
    "changed": true,
    "previous_status": "pending",
    "order": {
      "order_id": "11088739",
      "id": "6700...",
      "reference": "11088739",
      "external_order_id": null,
      "status": "processing",
      "status_description": "Order accepted by the e-commerce platform",
      "total_price": 650,
      "currency": "EGP",
      "created_at": "2026-11-11T09:00:00.000Z",
      "updated_at": "2026-11-11T10:00:00.000Z"
    }
  }
}
```

If the order is already `processing`:

```json
{
  "success": true,
  "data": {
    "message": "Order is already processing",
    "changed": false,
    "previous_status": "processing",
    "order": { "...": "..." }
  }
}
```

---

## 5) Inbound direction: rejecting the order

```http
GET  /api/public/orders/reject?id=11088739
POST /api/public/orders/reject?id=11088739
X-API-KEY: <SUPER_SYSTEGO_API_KEY>
```

- Same order resolution rules.
- Result: `pending` → `canceled`
- `statusDescription = "Order rejected by the e-commerce platform"`
- idempotent: if it is already `canceled` → `200` with `changed: false` and the message `Order is already canceled`.

---

## 6) Order statuses

| Status | Meaning | Set by |
|---|---|---|
| `pending` | New order awaiting a decision | Storefront checkout |
| `processing` | Accepted | accept or the admin |
| `confirmed` | Payment confirmed | Payment webhooks |
| `canceled` | Rejected/canceled | reject or the admin |
| `rejected` | Payment/order failed | Payment webhooks |
| `out_for_delivery` | Out for delivery | Admin/shipping |
| `delivered` | Delivered | Admin/shipping |
| `returned` | Returned | Admin/shipping |
| `failed_to_deliver` | Delivery failed | Admin/shipping |
| `scheduled` | Scheduled | Admin |

> `accept` and `reject` do not enforce a specific previous status in the current code; they set the status directly while preventing duplication. However, the intended use is `pending` only.

---

## 7) Managing orders from the admin panel

| Method | Path | Permission | Function |
|---|---|---|---|
| `GET` | `/api/admin/online-orders` | `orders.View` | Storefront order list (`?status=&shippingMethod=`) |
| `GET` | `/api/admin/online-orders/:id` | `orders.View` | Order details |
| `PATCH` | `/api/admin/online-orders/:id/status` | `orders.Edit` | Update status |
| `PATCH` | `/api/admin/online-orders/:id/bosta-address` | `orders.Edit` | Edit the Bosta address |
| `POST` | `/api/admin/online-orders/bulk-create-shipments` | `orders.Edit` | Create bulk shipments |
| `DELETE` | `/api/admin/online-orders/:id` | — | Soft delete (`isDeleted=true`) |
| `GET` | `/api/admin/pos-online-orders` | `POS.View` + `orders.View` | Online orders from the POS |
| `PUT` | `/api/admin/pos-online-orders/:id/status` | `POS.Edit` + `orders.Edit` | Update status from the POS |

Statuses accepted in the admin filter:

`pending`, `confirmed`, `processing`, `out_for_delivery`, `delivered`, `returned`, `failed_to_deliver`, `canceled`, `scheduled`.

---

## 8) Errors

All errors use the standard shape:

```json
{
  "success": false,
  "error": {
    "code": 401,
    "message": "Invalid or missing x-api-key"
  }
}
```

| Code | Message | Cause |
|---|---|---|
| `400` | `id is required...` | No identifier in the query/body |
| `401` | `Invalid or missing x-api-key` | Missing or wrong key |
| `404` | `Order not found` | No order with this identifier |
| `503` | `Webhook API key is not configured...` | `SUPER_SYSTEGO_API_KEY` is not set |

> Note: the current `accept`/`reject` do not reject a transition from `confirmed` or `delivered`;
> the code sets the status directly. Review the business logic before relying on them as a strict state machine.

---

## 9) cURL examples

```bash
# Accept
curl -X POST "https://<systego-domain>/api/public/orders/accept?id=11088739" \
  -H "Accept: application/json" \
  -H "X-API-KEY: $SUPER_SYSTEGO_API_KEY"

# Reject
curl -X POST "https://<systego-domain>/api/public/orders/reject?id=11088739" \
  -H "Accept: application/json" \
  -H "X-API-KEY: $SUPER_SYSTEGO_API_KEY"
```

---

## 10) Why was the old documentation wrong?

The following files exist but are **not mounted** in any router:

- `src/controller/public/onlineOrders.ts`
- `src/middlewares/ecommerceApiKey.ts`

Therefore:

- ❌ `POST /api/public/online-orders` returns `404 Route not found`.
- ❌ `ECOMMERCE_API_KEY` is not read.
- ❌ There is no endpoint for creating an order from the e-commerce platform.

The actual public router (`src/routes/public.ts`) contains only:

```text
GET  /api/public/products
GET  /api/public/orders/accept
POST /api/public/orders/accept
GET  /api/public/orders/reject
POST /api/public/orders/reject
```

---

## 11) Security notes

1. accept/reject do not require a JWT, only `x-api-key`.
2. The key is shared across all operations of this tenant; there is no rotation/expiry.
3. There is no rate limiting on the public routes.
4. Accepting/rejecting an order only affects the status, but it may trigger notifications/real-time updates for the admin.
5. Do not rely on `?id=` alone for protection; the key is the boundary line.
