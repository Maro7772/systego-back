# 📡 Systego API Reference — Actual API Guide

> **Last updated:** November 2026
>
> This file explains **how to work with the API as it is actually implemented in the code**:
> authentication, headers, response and error shapes, the permissions system, branch/warehouse scope,
> and the core flows (login, selling in POS, shifts, orders, installments,
> shipping, payment gateways, and sync).
>
> 📌 **The complete list of all Endpoints** (568 routes) is in
> [`docs/API_ENDPOINTS.md`](docs/API_ENDPOINTS.md), and the field reference is in
> [`docs/DATABASE_SCHEMA.md`](docs/DATABASE_SCHEMA.md). Do not rely on this file
> as a comprehensive route list, but as a guide to behavior and rules.

---

## Table of Contents

1. [Overview and Running the App](#1-overview-and-running-the-app)
2. [Authentication and Headers](#2-authentication-and-headers)
3. [Response and Error Shapes](#3-response-and-error-shapes)
4. [Permissions System (RBAC)](#4-permissions-system-rbac)
5. [Branch/Warehouse Scope](#5-branchwarehouse-scope)
6. [Public Routes `/api/public`](#6-public-routes-apipublic)
7. [POS Sync `/api/sync`](#7-pos-sync-apisync)
8. [Storefront `/api/store`](#8-storefront-apistore)
9. [Admin Panel `/api/admin`](#9-admin-panel-apiadmin)
10. [Core Flows in Detail](#10-core-flows-in-detail)
11. [Webhooks and Payment Gateways](#11-webhooks-and-payment-gateways)
12. [Common Errors and Fixes](#12-common-errors-and-fixes)
13. [Security Notes and Known Gaps](#13-security-notes-and-known-gaps)

---

## 1. Overview and Running the App

| Item | Value |
|---|---|
| Runtime | Node.js + Express 5 + TypeScript |
| Database | MongoDB via Mongoose |
| Server | `http://localhost:3000` by default (`PORT` from `.env`) |
| Base Path | All API routes are under `/api` |
| Static Files | `/uploads` (product images, logos, payment proofs) |
| Socket.IO | Same server, `cors: *`, and events such as `new_order` and `order_status_changed` |
| Body Limit | `express.json` and `express.urlencoded` with a **20MB** limit |
| CORS | `origin: "*"` |
| Security | `helmet` (with `crossOriginResourcePolicy` disabled) + `cookie-parser` |
| Health/Diagnostic | `GET /test-updater` returns `"all right 2"` (outside `/api`) |

### 1.1 Request Path Inside the Application

```text
Request
  └─ /api
      ├─ /sync    → no authentication (offline sync)
      ├─ /public  → no JWT (products + webhook with x-api-key)
      ├─ languageMiddleware
      ├─ /admin   → long chain: auth → roles → tenant → warehouse → permission
      └─ /store   → customer storefront (JWT optional/required depending on the route)
  └─ 404 → { success:false, error:{ code:404, message:"Route not found" } }
  └─ errorHandler
```

### 1.2 Admin Middleware Sequence (Very Important)

The actual order in `src/routes/admin/index.ts`:

```text
/api/admin/tenant-info      (public)
/api/admin/plesk            (public, before authentication — the key is injected by the server)
/api/admin/auth             (public: login/signup)
        ↓
authenticated               (JWT)
authorizeRoles("admin","superadmin")
/api/admin/subscription     (authentication + role only, before the subscription check)
        ↓
requireActiveTenant         (blocks an expired subscription)
enforceWarehouseScope       (enforces warehouse_id)
        ↓
all remaining /api/admin routes
```

Any new admin route added after this chain automatically inherits authentication, role, subscription, and branch scope.

---

## 2. Authentication and Headers

### 2.1 Authentication Types in the System

| Type | Used in | Header |
|---|---|---|
| **Admin JWT** | `/api/admin/*` (except public routes) and `/api/store/auth/edit-profile` | `Authorization: Bearer <token>` |
| **Store JWT (optional/required)** | `/api/store/*` | `Authorization: Bearer <token>` |
| **Webhook API Key** | `/api/public/orders/accept|reject` | `x-api-key: <SUPER_SYSTEGO_API_KEY>` |
| **No authentication** | `/api/sync/*`, `/api/public/products`, storefront signup/login, storefront checkout | — |
| **Plesk API Key** | Internal only inside `/api/admin/plesk/proxy` | Injected from `.env` and never sent from the browser |

### 2.2 Accepted Headers

| Header | Usage | Notes |
|---|---|---|
| `Authorization` | `Bearer <JWT>` | Required on protected routes |
| `x-pos-client` | `"true"` when logging in from the cashier app | Prevents login if the user is not linked to a warehouse, and returns `400` with `details.code = USER_NO_WAREHOUSE` |
| `x-session-id` | Guest session in the storefront | Used for the guest cart and for checkout without login |
| `x-api-key` | Order webhook | Compared locally and in constant time |
| `x-lang` / `x-language` | Response language | `ar` or `en` (any value starting with `ar`/`en`) |
| `?lang=` / `?language=` / `?locale=` | Response language via query | Takes precedence over the header |
| `Content-Type` | `application/json` by default | Some payment integrations use form-urlencoded |

### 2.3 Response Language (Localization)

`languageMiddleware` runs after `/sync` and `/public` and before `/admin` and `/store`:

- If the client does not explicitly specify a language → data is returned as-is from the database.
- If `ar` → the fields `ar_name` / `name_ar` are converted to `name` and the other language fields are removed.
- If `en` → the opposite.
- The conversion recurses inside objects and arrays, and excludes `Date`, `ObjectId`, and `Buffer`.

> ⚠️ Therefore do not assume that `ar_name` exists in the response when sending `x-lang: ar`; the field will become `name`.

### 2.4 JWT

**Admin token** (from `POST /api/admin/auth/login`):

```json
{
  "id": "<user._id>",
  "name": "<username>",
  "role": "admin | superadmin",
  "role_id": "<role _id | null>",
  "warehouse_id": "<warehouse _id | null>",
  "iat": 1760000000,
  "exp": 1760604800
}
```

- Duration: **7 days**.
- The permissions are **not inside the token**; they are loaded from the database on every request via `authenticated`.
- `superadmin` bypasses all permission checks.
- An `admin` linked to a warehouse with no `role_id` automatically gets every permission of every module (Warehouse Admin).
- An `admin` with a `role_id` → the role's permissions + the user's permissions are merged.

**Store token** (from storefront signup/login): duration **3 days**, and it contains only `id`.

---

## 3. Response and Error Shapes

### 3.1 Success

All successful responses go through `SuccessResponse`:

```json
{
  "success": true,
  "data": {
    "...": "..."
  }
}
```

The default status is `200`, and some routes return `201` (such as creating a storefront order).

### 3.2 Error

All errors (including 404 and Joi) share the same shape:

```json
{
  "success": false,
  "error": {
    "code": 400,
    "message": "Validation failed",
    "details": ["..."]
  }
}
```

`details` may be:
- an array of messages (Joi errors in `validate`).
- a structured object (such as `PERMISSION_DENIED` or `WAREHOUSE_SCOPE_DENIED`).
- a string or `undefined`.

### 3.3 Status Codes Used

| Code | Meaning | Examples |
|---|---|---|
| `200` | Success | Most reads and updates |
| `201` | Created | `POST /api/store/order/checkout` |
| `400` | Invalid request / validation | Joi, `BadRequest`, `ValidationError` |
| `401` | Unauthenticated | Missing/wrong/expired token, wrong `x-api-key` |
| `403` | Authenticated but forbidden | Role not allowed, missing permission, expired subscription, another warehouse |
| `404` | Not found | `Route not found`, product/order/user not found |
| `409` | Conflict | Duplicate email/phone, `unique` constraint |
| `413` | Request too large | `entity.too.large` |
| `500` | Internal error | `DatabaseError`, unexpected errors |
| `503` | Service not configured | `x-api-key` not set in `.env` |

### 3.4 Permission Error Details

`403` from `authorizePermissions`:

```json
{
  "success": false,
  "error": {
    "code": 403,
    "message": "No permission to Add in POS",
    "details": {
      "code": "PERMISSION_DENIED",
      "module": "POS",
      "required_actions": ["Add"],
      "granted_actions": ["View"]
    }
  }
}
```

`403` from `authorizeRoles`:

```json
{
  "error": {
    "code": 403,
    "message": "You are not authorized to access this resource",
    "details": {
      "code": "ROLE_NOT_ALLOWED",
      "required_roles": ["admin", "superadmin"],
      "current_role": "cashier"
    }
  }
}
```

`403` from branch scope:

```json
{
  "error": {
    "code": 403,
    "message": "You are not allowed to access another warehouse",
    "details": {
      "code": "WAREHOUSE_SCOPE_DENIED",
      "required_warehouse_id": "...",
      "received_warehouse_ids": ["..."]
    }
  }
}
```

> Behavioral note: 401 means "log in again", and 403 means "you are logged in but do not have the permission". The frontend must distinguish between them; mixing 401 with permissions caused an incorrect logout in the POS.

---

## 4. Permissions System (RBAC)

### 4.1 Modules and Actions

- **80 modules** defined in `src/types/constant.ts` (`MODULES`).
- **5 actions**: `View`, `Add`, `Edit`, `Delete`, `Status`.
- The module names, their Arabic names, and their groups are in `src/types/moduleMeta.ts` (10 groups).

### 4.2 Who Has What

| Case | Permissions |
|---|---|
| `superadmin` | Bypasses all checks in `authorizePermissions`, `authorizeAnyPermission`, and `enforceWarehouseScope` |
| `admin` + `warehouse_id` + no `role_id` | All actions for all modules (treated as Warehouse Admin) |
| `admin` + `role_id` | A merge of `Role` permissions + `User` permissions (user permissions win on conflict) |
| Any other role | Rejected by `authorizeRoles("admin","superadmin")` at the admin router level |

### 4.3 Routes Without Permission Checks

Not every admin route calls `authorizePermissions`. Currently, **96 admin routes** have no explicit permission check (some are intentional, such as `/auth`, `/tenant-info`, `/subscription`, and some are a gap, such as parts of the payment settings).

Examples of admin routes without a module-level permission check:

- `POST/PUT/GET/DELETE /api/admin/geidea/*`
- `POST/PUT/GET /api/admin/fawry/*`
- `GET /api/admin/ecommerce-data/*`
- `GET /api/admin/permission/selection` and `/modules-actions`
- `DELETE /api/admin/online-orders/:id`

These routes are protected by authentication, role, and branch scope, but they do not require a specific module permission. See Section 13.

### 4.4 `authorizeAnyPermission`

Some routes accept any module from a list (such as installments):

```ts
authorizeAnyPermission(["installments", "POS", "Admin"], "View")
```

---

## 5. Branch/Warehouse Scope

`enforceWarehouseScope` runs for every `/api/admin/*` after authentication:

1. `superadmin` → no restrictions.
2. A user without `warehouse_id` → no restrictions (but they will most likely not reach a specific branch's data).
3. A user linked to a warehouse:
   - checks `warehouse_id`, `warehouseId`, `WarehouseId`, `fromWarehouseId`, `toWarehouseId` in `params`, `query`, and `body` (up to 5 levels deep).
   - any different warehouse → `403 WAREHOUSE_SCOPE_DENIED`.
   - in `GET/DELETE` without a specified warehouse → `warehouse_id` is added automatically to the query.
   - in `POST/PUT/PATCH` without a specified warehouse → `warehouse_id` is added automatically to the body.
4. Intentional exceptions:
   - `/api/admin/transfer` (transfer between branches): creation only from the user's branch, and receiving only into their branch.
   - `/api/admin/pos/transfer`: handled inside the controller (from/to the cashier's branch).

### 5.1 In POS

- `warehouseId` is taken from the JWT (`req.user.warehouse_id`), not from the body.
- If the user is not linked to a warehouse → `400 "Warehouse is not assigned to this user"`.

---

## 6. Public Routes `/api/public`

The actual router in `src/routes/public.ts` contains **only 3 routes**:

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/public/products` | No | Public storefront products (legacy compatibility) |
| `GET`/`POST` | `/api/public/orders/accept` | `x-api-key` | Accept an e-commerce order |
| `GET`/`POST` | `/api/public/orders/reject` | `x-api-key` | Reject an e-commerce order |

### 6.1 accept / reject

```http
POST /api/public/orders/accept?id=11088739
X-API-KEY: <SUPER_SYSTEGO_API_KEY>
```

- The identifier can be sent in:
  - query: `id`, `order_id`, `orderId`, `reference`
  - body JSON with the same names
  - route param (not currently used)
- Lookup order: `externalOrderId` → `reference` → `_id` (if a valid ObjectId).
- `accept` → status `processing`.
- `reject` → status `canceled`.
- The change is idempotent: if the same status is requested, it returns `200` with `changed: false`.
- A record is added to `statusHistory` with `source: "system"`.
- Emits a Socket.IO event named `order_status_changed`.
- It does not touch inventory, payment, or shipping.

Example response:

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
      "currency": "EGP"
    }
  }
}
```

### 6.2 What Is **Not Present**

- ❌ `POST /api/public/online-orders` (creating an order from e-commerce) is **not mounted** in the router.
- The files `src/controller/public/onlineOrders.ts` and `src/middlewares/ecommerceApiKey.ts` exist but are **unused**.
- The only creation of orders comes from `POST /api/store/order/checkout` and is then sent externally (Outbound).

Full details are in [`docs/custom-order-webhook.md`](docs/custom-order-webhook.md).

---

## 7. POS Sync `/api/sync`

⚠️ **These routes require no authentication.** Anyone who knows the URL can read and write. See Section 13.

### 7.1 `GET /api/sync/bootstrap/:table`

Fetch a snapshot of a table:

- `:table` is the logical table name accepted by `getSyncModel` (39 allowed tables).
- `?cursor=<last_id>` for incremental loading.
- `PAGE_SIZE = 500`.

Response:

```json
{
  "success": true,
  "data": {
    "rows": [{ "id": "...", "...": "...", "created_at": "...", "updated_at": "..." }],
    "nextCursor": "6700...",
    "serverSnapshotAt": "2026-11-11T10:00:00.000Z",
    "totalCount": 1200,
    "loadedCount": 500
  }
}
```

### 7.2 `POST /api/sync/push`

```json
{
  "clientId": "pos-branch-1",
  "changes": [
    {
      "id": "change-uuid",
      "table_name": "Product",
      "record_id": "6700...",
      "op": "insert | update | delete | upsert",
      "payload": "{\"id\":\"...\",\"name\":\"...\"}",
      "created_at": "2026-11-11T09:59:00.000Z"
    }
  ]
}
```

- `insert` → upsert with `_id = record_id`.
- `update` → `payload` in the form `{ "fields": { "name": { "op": "set", "value": "..." }, "quantity": { "op": "inc", "value": -1 } } }`, and supports `arrayPatch`.
- `delete` → delete by `_id`.
- `upsert` → legacy full-row path with last-write-wins protection.
- idempotency via `ChangeLogModel.sourceChangeId`.
- Response:

```json
{
  "success": true,
  "data": {
    "applied": ["change-uuid"],
    "failed": [{ "id": "change-uuid-2", "reason": "..." }]
  }
}
```

- Errors do not fail the entire request; they are returned in `failed`.
- In `development`, the full `stack` is returned in `reason`.

### 7.3 `GET /api/sync/pull?since=<ISO>&clientId=<id>`

- `clientId` is **required**, otherwise `400`.
- `since` is optional; the default is `1970-01-01`.
- Excludes changes that originated from the same `clientId`.
- Response:

```json
{
  "success": true,
  "data": {
    "changes": [
      { "table_name": "Product", "op": "insert", "record_id": "...", "data": { "...": "..." } },
      { "table_name": "Product", "op": "update", "record_id": "...", "data": { "fields": { "...": "..." } } },
      { "table_name": "Product", "op": "delete", "record_id": "..." }
    ],
    "serverTime": "2026-11-11T10:00:00.000Z"
  }
}
```

---

## 8. Storefront `/api/store`

The router `src/routes/users/index.ts` is mounted on `/api/store`.

### 8.1 Authentication

| Method | Path | Auth | Notes |
|---|---|---|---|
| `POST` | `/api/store/auth/signup` | No | `name`, `email`, `password`, `phone`, `image?` |
| `POST` | `/api/store/auth/login` | No | `identifier` (email/phone/name) + `password` |
| `POST` | `/api/store/auth/verify-otp` | No | `identifier`, `otp` (6 digits) |
| `POST` | `/api/store/auth/resend-otp` | No | `identifier` |
| `POST` | `/api/store/auth/google-login` | No | Google sign-in |
| `POST` | `/api/store/auth/apple-login` | No | Apple sign-in |
| `POST` | `/api/store/auth/complete-profile` | No | `userId`, `name`, `email`, `password`, `confirmPassword` |
| `PUT` | `/api/store/auth/edit-profile/:id` | Bearer | Edit customer data |
| `GET` | `/api/store/auth/get-profile` | Bearer | Customer data |

> The actual route names are `google-login` and `apple-login`, not `google`/`apple`.

Login behavior:
- If the account requires OTP → it returns `requires_otp: true` and `action_required: "GO_TO_OTP_SCREEN"`.
- After success → `token` + user data.

### 8.2 Catalog and Browsing (Public)

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/store/category` | All categories |
| `GET` | `/api/store/category/:id` | A single category |
| `GET` | `/api/store/product` | Products (search/filter/pagination) |
| `GET` | `/api/store/product/best-sellers` | Best sellers (POS + online) |
| `GET` | `/api/store/product/category/:id` | Category products |
| `GET` | `/api/store/product/:id` | Product details |
| `GET` | `/api/store/brand` | Brands |
| `GET` | `/api/store/brand/:id` | A single brand |
| `GET` | `/api/store/banner` | Banners |
| `GET` | `/api/store/offer` | Offers/bundles |
| `GET` | `/api/store/offer/:id` | A single offer |
| `GET` | `/api/store/payment-methods` | Active payment methods |
| `GET` | `/api/store/order-type` | Order types |
| `GET` | `/api/store/warehouse` | Available branches |
| `GET` | `/api/store/tenant-info` | Organization data and subscription |
| `GET` | `/api/store/store-settings` | Storefront settings |
| `GET` | `/api/store/ecommerce-data` | E-commerce data |
| `GET` | `/api/store/installments/settings` | Installment settings |
| `POST` | `/api/store/installments/preview` | Preview an installment plan |

### 8.3 Cart (Optional JWT)

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/store/cart` | The user's cart or the guest cart (`x-session-id`) |
| `POST` | `/api/store/cart/sync-cart` | Sync cart items |
| `POST` | `/api/store/cart/apply-coupon` | Apply/remove a coupon |
| `DELETE` | `/api/store/cart/clear` | Clear the cart |

`sync-cart` body:

```json
{
  "items": [
    {
      "productId": "24-hex",
      "productVariantId": "24-hex-or-null",
      "quantity": 2,
      "options": [{ "variationId": "24-hex", "optionId": "24-hex" }]
    }
  ]
}
```

> There are no `POST /cart` or `PUT /cart/item/:id` routes as in some old documentation; modification is done via `sync-cart`.

### 8.4 Addresses

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/store/address/lists` | No | Helper lists (countries/cities/areas) |
| `GET` | `/api/store/address` | Bearer | Customer addresses |
| `POST` | `/api/store/address` | Bearer | Add an address |
| `PUT` | `/api/store/address/:id` | Bearer | Edit an address |
| `DELETE` | `/api/store/address/:id` | Bearer | Delete an address |

### 8.5 Wishlist

| Method | Path | Auth |
|---|---|---|
| `POST` | `/api/store/wishlist/toggle` | Bearer |
| `GET` | `/api/store/wishlist` | Bearer |
| `DELETE` | `/api/store/wishlist/clear` | Bearer |

### 8.6 Orders

| Method | Path | Auth | Description |
|---|---|---|---|
| `POST` | `/api/store/order/checkout` | Optional (guest via `x-session-id`) | Create an order |
| `GET` | `/api/store/order/my-orders` | Bearer | Customer orders |
| `GET` | `/api/store/order/:id` | Optional | Order details |
| `GET` | `/api/store/order/status/:ref` | No | Order status by `reference` |
| `GET` | `/api/store/order/verify-payment/paymob/:orderId` | Bearer | Verify a Paymob payment |

### 8.7 Shipping

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/store/shipping/active-method` | `self` or `bosta` |
| `GET` | `/api/store/shipping/bosta/cities` | Bosta cities |
| `GET` | `/api/store/shipping/bosta/districts/:cityId` | Bosta districts |
| `GET` | `/api/store/shipping/bosta/pricing` | Shipping price |

### 8.8 Installments

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/store/installments` | Bearer | Customer plans |
| `GET` | `/api/store/installments/order/:orderId` | Bearer | Order plan |
| `GET` | `/api/store/installments/:id` | Bearer | A single plan |

### 8.9 Store Webhooks

| Method | Path | Auth |
|---|---|---|
| `GET`/`POST` | `/api/store/order/webhook/paymob` | HMAC signature |
| `GET`/`POST` | `/api/store/order/webhook/geidea` | HMAC signature |
| `GET`/`POST` | `/api/store/order/webhook/fawry` | SHA-256 signature |

---

## 9. Admin Panel `/api/admin`

### 9.1 Group Map (Mount Prefixes)

| Prefix | Group |
|---|---|
| `/api/admin/auth` | Login/signup (public) |
| `/api/admin/tenant-info` | Organization data (public) |
| `/api/admin/plesk` | Secure proxy for Plesk (public before authentication) |
| `/api/admin/subscription` | Subscription |
| `/api/admin/profile` | Current user profile |
| `/api/admin/admin` | User management |
| `/api/admin/permission` | Roles and permissions |
| `/api/admin/product`, `/category`, `/brand`, `/units`, `/variation` | Catalog |
| `/api/admin/warehouse`, `/stock`, `/adjustment`, `/transfer`, `/stocktake`, `/wasted`, `/product_warehouse` | Inventory |
| `/api/admin/pos`, `/pos/warehouse`, `/pos/transfer`, `/pos/installments`, `/pos-home`, `/pos-booking`, `/pos_customer`, `/pos-wasted` | Points of sale |
| `/api/admin/cashier-shift`, `/cashiershift`, `/cashier` | Shifts and cashier |
| `/api/admin/customer`, `/gift-card`, `/point`, `/redeem-points` | Customers and loyalty |
| `/api/admin/supplier`, `/purchase`, `/return-purchase`, `/recipe`, `/category_material` | Purchases and manufacturing |
| `/api/admin/bank_account`, `/ledger`, `/revenue`, `/expense`, `/expenseAdmin`, `/expensecategory`, `/payable`, `/receivable`, `/finicial-report` | Finance |
| `/api/admin/dashboard`, `/product-report`, `/product-movement`, `/report-filters` | Reports |
| `/api/admin/orders`, `/online-orders`, `/pos-online-orders` | Orders |
| `/api/admin/installments` | Installments |
| `/api/admin/shipping`, `/courier`, `/delivery-man`, `/delivery-assignment`, `/zone`, `/city`, `/country`, `/pickup-point`, `/label` | Shipping and delivery |
| `/api/admin/payment`, `/payment_method`, `/paymob`, `/geidea`, `/fawry` | Payments |
| `/api/admin/offer`, `/coupon`, `/discount`, `/taxes`, `/service-fees`, `/popup`, `/banner`, `/store-settings`, `/ecommerce-data` | Marketing and storefront |
| `/api/admin/notification`, `/booking`, `/order-type`, `/decimal-setting`, `/version-updater` | Miscellaneous |
| `/api/admin/db` | DB Gateway (raw) |
| `/api/admin/qztray` | QZ Tray printing |

### 9.2 Important Route Examples

| Method | Path | Permission |
|---|---|---|
| `POST` | `/api/admin/auth/login` | public |
| `POST` | `/api/admin/auth/signup` | public |
| `GET` | `/api/admin/profile` | authenticated |
| `GET/POST` | `/api/admin/admin` | `Admin` |
| `GET/POST` | `/api/admin/permission` | `permission` |
| `GET` | `/api/admin/product` | `product.View` |
| `POST` | `/api/admin/pos/sales` | `POS.Add` |
| `GET` | `/api/admin/pos/sales` | `POS.View` |
| `GET` | `/api/admin/pos/sales/pending` | `POS.View` |
| `POST` | `/api/admin/pos/sales/pay-due` | `POS.Add` |
| `POST` | `/api/admin/pos/apply-coupon` | `POS.Add` |
| `POST` | `/api/admin/cashier-shift/start` | `POS.Add` + `cashier_shift.Add` |
| `PUT` | `/api/admin/cashier-shift/end` | `POS.Edit` + `cashier_shift.Edit` |
| `GET` | `/api/admin/online-orders` | `orders.View` |
| `PATCH` | `/api/admin/online-orders/:id/status` | `orders.Edit` |
| `GET` | `/api/admin/installments/plans` | `installments` or `POS` or `Admin` |
| `POST` | `/api/admin/shipping/bosta/deliveries/from-order` | `zone.Edit` |
| `GET` | `/api/admin/shipping/bosta/track/:trackingNumber` | `zone.View` |
| `POST` | `/api/admin/db/gateway` | authenticated + role only |

> The complete list (Method + Path + Permission + Handler) is in
> [`docs/API_ENDPOINTS.md`](docs/API_ENDPOINTS.md).

### 9.3 DB Gateway

`POST /api/admin/db/gateway` allows raw CRUD operations on **any** Mongoose model:

```json
{
  "table": "Product",
  "operation": "read | count | write | edit | delete",
  "filter": { "warehouse_id": "..." },
  "data": { "...": "..." },
  "options": { "sort": { "createdAt": -1 }, "limit": 50, "skip": 0, "projection": { "name": 1 } }
}
```

- `edit` uses `updateMany` + `$set` with `runValidators`.
- `delete` uses `deleteMany`.
- It does not check a module permission or `warehouse_id` automatically. See Section 13.

---

## 10. Core Flows in Detail

### 10.1 Admin Login

```http
POST /api/admin/auth/login
Content-Type: application/json
x-pos-client: true        # optional, for the cashier app
```

```json
{
  "email": "admin@example.com",
  "password": "secret123"
}
```

Successful response:

```json
{
  "success": true,
  "data": {
    "message": "Login successful",
    "token": "eyJ...",
    "user": {
      "id": "...",
      "username": "admin",
      "email": "admin@example.com",
      "status": "active",
      "role": "admin",
      "role_id": null,
      "role_name": "Warehouse Admin",
      "warehouse_id": "...",
      "permissions": [
        { "module": "POS", "actions": [{ "id": "...", "action": "View" }] }
      ]
    },
    "hasOpenShift": true,
    "shift": { "...": "..." },
    "cashier": { "...": "..." },
    "financialAccounts": [{ "_id": "...", "name": "Cash", "balance": 1000 }]
  }
}
```

Rules:
- `email` + `password` are required.
- `status` must be `active`.
- `x-pos-client: true` + a user without `warehouse_id` → `400 USER_NO_WAREHOUSE`.
- An expired subscription **does not prevent** login (so the user can reach the renewal page), but it blocks the rest of the admin routes via `requireActiveTenant`.
- `hasOpenShift`, `shift`, `cashier`, and `financialAccounts` are returned to make opening the POS screen directly easier.

### 10.2 Creating a POS Sale

```http
POST /api/admin/pos/sales
Authorization: Bearer <admin-token>
Content-Type: application/json
```

```json
{
  "customer_id": "6700...",
  "order_pending": 0,
  "products": [
    {
      "product_id": "6700...",
      "product_price_id": "6700...",
      "quantity": 2,
      "discount": 10,
      "discount_type": "percentage"
    }
  ],
  "bundles": [],
  "service_fee_ids": [],
  "order_tax": "6700...",
  "order_discount": "6700...",
  "free_discount": 0,
  "password": "user-password-if-free-discount",
  "coupon_code": "SAVE10",
  "shipping": 0,
  "tax_rate": 0,
  "Due": 0,
  "payment_type": "full",
  "installment": { "months": 6, "down_payment": 100, "notes": "" },
  "note": "Test invoice",
  "financials": [
    { "account_id": "6700...", "amount": 500 }
  ]
}
```

#### Mandatory Rules Enforced by the Code

1. **Open shift**: the cashier must have a `CashierShift` with status `open`, otherwise:
   `400 "You must open a cashier shift before creating a sale"`.
2. **Warehouse from the token**: `warehouse_id` comes from the JWT, not from the body.
3. **At least one product or bundle**.
4. `order_pending=1` and `Due=1` cannot be combined.
5. Installments (`payment_type=installment`) cannot be combined with `Due=1` or with `order_pending=1`.
6. **Manual discount (free_discount > 0)** requires a correct `password` matching the current user's password, otherwise `400`.
7. **Wholesale price**: if the quantity exceeds the wholesale threshold, the price is calculated using the
   `whole_price / product.price` ratio and applied to the line price.
8. **Discounts**:
   - `Discount` doc: the percentage is a decimal fraction (`0.10` = 10%).
   - `Taxes` doc: the percentage is a decimal fraction as well.
   - `Coupon` doc: the percentage is a whole number (`10` = 10%).
   - Line discount (`discount_type`) supports `fixed` and `percentage`.
9. **Payments**:
   - In a completed sale (`order_pending=0` and `Due=0`): the sum of `financials[].amount` after converting it to piasters must equal `grand_total` after converting it to piasters, otherwise `400`. The account must be `status: true`, `in_POS: true`, and belong to the same warehouse.
   - In installments: the sum of payments must equal `down_payment` (the down payment).
   - In `order_pending=1` or `Due=1`: `financials` is not required.
   - Every payment line must have `amount > 0` and a valid `account_id` ObjectId.
10. In installments: paid = `down_payment`, and remaining = `grand_total - down_payment`.
11. In `Due=1`: no Payment is created, and the remaining amount is recorded as debt.
12. In `order_pending=1`: inventory is not deducted and no Payment is created.

The response returns:

```json
{
  "success": true,
  "data": {
    "message": "Sale created successfully",
    "store": { "...": "..." },
    "printer_settings": { "printer_type": "USB", "printer_IP": null, "printer_port": null, "Printer_name": null },
    "sale": { "_id": "...", "reference": "...", "grand_total": 500, "paid_amount": 500, "remaining_amount": 0, "...": "..." },
    "items": [{ "...": "..." }],
    "service_fees": [],
    "wholesale_applied": false,
    "installment_plan": null,
    "pricing_details": {
      "products_total": 500,
      "bundles_total": 0,
      "subtotal": 500,
      "discount_amount": 0,
      "order_discount_amount": 0,
      "free_discount": 0,
      "coupon_amount": 0,
      "gift_card_amount": 0,
      "service_fee_total": 0,
      "tax_amount": 0,
      "shipping": 0,
      "grand_total": 500
    }
  }
}
```

#### Supporting POS Routes

| Method | Path | Function |
|---|---|---|
| `GET` | `/api/admin/pos/sales/next-invoice-number` | Next invoice number |
| `GET` | `/api/admin/pos/sales` | All completed invoices (`order_pending=0`) |
| `GET` | `/api/admin/pos/sales/pending` | Pending invoices |
| `GET` | `/api/admin/pos/sales/dues` | Due (credit) invoices |
| `POST` | `/api/admin/pos/sales/pay-due` | Pay a due amount |
| `POST` | `/api/admin/pos/apply-coupon` | Check/apply a coupon |
| `GET` | `/api/admin/pos/sales/:id` | Invoice details |
| `DELETE` | `/api/admin/pos/sales/pending/:id` | Delete a pending invoice |

> Note the actual route is `/api/admin/pos/sales`, not `/api/admin/pos`.

### 10.3 Shifts

| Method | Path | Function |
|---|---|---|
| `GET` | `/api/admin/cashier-shift/current` | Current shift |
| `POST` | `/api/admin/cashier-shift/start` | Open a shift |
| `POST` | `/api/admin/cashier-shift/logout` | Log out |
| `PUT` | `/api/admin/cashier-shift/end` | Close the shift |
| `PUT` | `/api/admin/cashier-shift/end/report` | Close with a report |
| `PUT` | `/api/admin/cashier-shift/close/:id` | Close a specific shift |
| `GET` | `/api/admin/cashiershift` | All shifts |
| `GET` | `/api/admin/cashiershift/:id` | Shift details |
| `PUT` | `/api/admin/cashiershift/close/:id` | Close a shift (alternative route) |

Rules:
- `start` requires `POS.Add` + `cashier_shift.Add`.
- `end` requires `POS.Edit` + `cashier_shift.Edit`.
- A sale cannot be created without an `open` shift.

### 10.4 Storefront Checkout

```http
POST /api/store/order/checkout
Content-Type: application/json
Authorization: Bearer <store-token>     # optional for guests
x-session-id: <guest-session-id>       # required for guests without a token
```

```json
{
  "orderType": "delivery",
  "shippingAddress": {
    "street": "9th Street",
    "buildingNumber": "12",
    "floorNumber": "3",
    "apartmentNumber": "5",
    "uniqueIdentifier": "Next to the mosque",
    "bostaCityId": "1",
    "bostaDistrictId": "2",
    "bostaCityName": "Cairo",
    "bostaDistrictName": "Nasr City"
  },
  "paymentMethod": "6700...",
  "paymentType": "full",
  "proofImage": "data:image/png;base64,...",
  "sessionId": "guest-session-id",
  "installment": { "months": 6, "down_payment": 100, "notes": "" }
}
```

Joi conditions:
- `paymentMethod` is required (ObjectId).
- `orderType` = `delivery` (default) or `pickup`.
- `pickup` requires `warehouseId`.
- `delivery` requires `shippingAddress`:
  - an ObjectId of a saved address, or
  - an object containing `street` + (`city` for self shipping or `bostaCityId` for Bosta).
- `paymentType` = `full` or `installment`.
- `installment` is required if `paymentType=installment`: `months` (1–60), `down_payment` (default 0), `notes`.

Controller behavior:
1. Determines the cart: the user's cart or the `x-session-id` cart.
2. Calculates product prices after `applyIn: "E-commerce"` discounts.
3. Calculates shipping:
   - pickup → 0.
   - delivery self → from the Zone/City settings.
   - delivery Bosta → `bostaService.getShipmentPricing` + markup.
4. Applies the delivery service, taxes, and the coupon.
5. Creates an `Order` with status `pending` and `paymentStatus` according to the payment method.
6. Creates a notification for the admin and emits the Socket.IO `new_order` event.
7. Creates an installment plan immediately if `installment`.
8. If there is an automatic payment gateway:
   - Paymob → returns `paymobOrderId` + `iframeUrl`.
   - Geidea → returns `geideaSessionId` + `iframeUrl`.
   - Fawry → returns `referenceNumber` + `iframeUrl`.
   - If initialization fails → inventory is restored, the order is set to `rejected` and `paymentStatus=failed`, and `400` is returned.
9. Deletes the cart after success.
10. Sends the order externally best-effort without waiting.

Response `201`:

```json
{
  "success": true,
  "data": {
    "message": "Order created successfully",
    "order": { "_id": "...", "reference": "...", "status": "pending" },
    "payment": { "paymobOrderId": "...", "iframeUrl": "..." },
    "installment_plan": null
  }
}
```

### 10.5 Installments

#### Settings and Preview (Storefront)

```http
GET  /api/store/installments/settings
POST /api/store/installments/preview
```

`preview` example:

```json
{
  "total": 6000,
  "months": 6,
  "down_payment": 1000
}
```

#### Installments Management (Admin)

| Method | Path | Function |
|---|---|---|
| `GET` | `/api/admin/installments/settings` | Installment settings |
| `PUT` | `/api/admin/installments/settings` | Edit the settings |
| `POST` | `/api/admin/installments/preview` | Preview |
| `GET` | `/api/admin/installments/due` | Due installments |
| `GET` | `/api/admin/installments/plans` | All plans |
| `GET` | `/api/admin/installments/plans/:id` | A plan |
| `PUT` | `/api/admin/installments/plans/:planId/items/:itemId/pay` | Pay an installment |
| `PUT` | `/api/admin/installments/plans/:planId/items/:itemId` | Edit an installment |
| `PUT` | `/api/admin/installments/plans/:id/cancel` | Cancel a plan |
| `GET` | `/api/admin/installments/customer/:customerId` | A customer's plans |

Rules:
- Storefront installments require login (`400 "You must be logged in to pay in installments"`).
- A storefront plan is created with `source: "ecommerce"`.
- `interest_type`, `interest_value`, and `max_installments` are applied from the settings.
- In POS: `payment_type=installment` creates a plan linked to the invoice, and the down payment is recorded as paid.

### 10.6 Shipping and Bosta

#### Storefront Settings

| Method | Path |
|---|---|
| `GET` | `/api/admin/shipping/settings` |
| `PUT` | `/api/admin/shipping/settings` |
| `GET` | `/api/admin/shipping/free-products` |
| `PUT` | `/api/admin/shipping/free-products` |

#### Bosta

| Method | Path | Function |
|---|---|---|
| `POST` | `/api/admin/shipping/bosta/test` | Test the connection |
| `GET` | `/api/admin/shipping/bosta/cities` | Cities |
| `GET` | `/api/admin/shipping/bosta/districts/:cityId` | Districts |
| `GET` | `/api/admin/shipping/bosta/pricing/shipment` | Shipment pricing |
| `GET` | `/api/admin/shipping/bosta/pricing/sector` | Sector pricing |
| `GET` | `/api/admin/shipping/bosta/pricing/insurance` | Insurance estimate |
| `POST` | `/api/admin/shipping/bosta/deliveries/from-order` | Create a shipment from an order |
| `POST` | `/api/admin/shipping/bosta/deliveries/bulk` | Create bulk shipments |
| `POST` | `/api/admin/shipping/bosta/deliveries/return` | Return shipment |
| `GET` | `/api/admin/shipping/bosta/orders/:orderId/shipment` | Order shipment |
| `GET` | `/api/admin/shipping/bosta/track/:trackingNumber` | Tracking |
| `POST` | `/api/admin/shipping/bosta/shipments/:shipmentId/refresh` | Refresh tracking |
| `GET` | `/api/admin/shipping/bosta/shipments/:shipmentId/history` | Tracking history |
| `POST` | `/api/admin/shipping/bosta/shipments/:shipmentId/sync` | Sync |
| `DELETE` | `/api/admin/shipping/bosta/shipments/:shipmentId` | Cancel a shipment |
| `GET` | `/api/admin/shipping/bosta/shipments/:shipmentId/label` | Shipping label |
| `GET` | `/api/admin/shipping/bosta/pickups/pending` | Pending pickups |
| `POST` | `/api/admin/shipping/bosta/pickups/daily` | Daily pickup |
| `POST` | `/api/admin/shipping/bosta/pickups` | Create a pickup |
| `GET` | `/api/admin/shipping/bosta/pickup-locations` | Pickup locations |
| `PUT` | `/api/admin/shipping/bosta/pickup-locations/:locationId/default` | Set as default |
| `POST` | `/api/admin/shipping/bosta/pickup-locations/sync` | Sync locations |

- All Bosta routes require `zone.View` or `zone.Edit`.
- In storefront checkout: if the address is Bosta, shipping is calculated from the Bosta API + markup, and free shipping is not applied to Bosta.

### 10.7 Admin Orders

| Method | Path | Function |
|---|---|---|
| `GET` | `/api/admin/orders` | All orders |
| `GET` | `/api/admin/orders/report` | Orders report |
| `GET` | `/api/admin/orders/:id` | A single order |
| `GET` | `/api/admin/online-orders` | Storefront orders |
| `GET` | `/api/admin/online-orders/:id` | A storefront order |
| `PATCH` | `/api/admin/online-orders/:id/status` | Update the status |
| `POST` | `/api/admin/online-orders/bulk-create-shipments` | Bulk shipments |
| `PATCH` | `/api/admin/online-orders/:id/bosta-address` | Edit the Bosta address |
| `DELETE` | `/api/admin/online-orders/:id` | Delete an order |
| `GET` | `/api/admin/pos-online-orders` | Online orders from POS |
| `PUT` | `/api/admin/pos-online-orders/:id/status` | Update their status |

---

## 11. Webhooks and Payment Gateways

### 11.1 Paymob

```http
GET|POST /api/store/order/webhook/paymob?hmac=<hmac>
```

- Supports a callback with `obj`, a flat body, or query parameters.
- Verifies HMAC-SHA512 using any of:
  `PAYMOB_HMAC_KEY`, `PAYMOB_HMAC_SECRET`, `PAYMOB_SECRET_KEY`, or `hmac_key` from the Paymob settings.
- `PAYMOB_STRICT_HMAC=true` → rejects an invalid signature with `400`.
- Matches the order by `merchant_order_id` or `paymobOrderId`.
- Rejects if the order is not Paymob or the amount differs.
- Success → `paymentStatus=paid` and `status=confirmed`.
- Failure → `paymentStatus=failed` and `status=rejected`.
- Returns `{ "status": "ok" }`.

### 11.2 Geidea

```http
GET|POST /api/store/order/webhook/geidea
```

- Reads `signature` from the body, `x-signature`, or query.
- Signature: HMAC-SHA256 over
  `${merchantReferenceId}${amount}${currency}${orderId}${status}`.
- The key is from `Geidea.webhookSecret` or `GEIDEA_HMAC_KEY` or `GEIDEA_SECRET_KEY`.
- `GEIDEA_STRICT_SIGNATURE=true` → rejects an invalid signature.
- `SKIP_GEIDEA_HMAC=true` for development only.
- Paid statuses: `success`, `paid`, `captured`, `authorized`.
- Failed statuses: `failed`, `declined`, `cancelled`, `canceled`, `error`.
- Returns text: `OK` or `Already processed`.

### 11.3 Fawry

```http
GET|POST /api/store/order/webhook/fawry
```

- Requires `merchantRefNumber` and `fawryRefNumber`.
- Signature: SHA-256 over
  `${fawryRefNumber}${merchantRefNumber}${paymentAmount.toFixed(2)}${orderAmount.toFixed(2)}${orderStatus}${paymentMethod}${paymentRefrenceNumber}${secureKey}`.
- `FAWRY_STRICT_SIGNATURE=true` → rejects an invalid signature.
- `paid`/`new` → paid and confirmed.
- `canceled`/`failed`/`expired` → rejected and failed.
- Returns `OK` or `Already processed`.

### 11.4 Manual Paymob Verification

```http
GET /api/store/order/verify-payment/paymob/:orderId
Authorization: Bearer <store-token>
```

- Fetches Paymob transactions directly.
- If the latest transaction is successful and the order is `pending` → `processing` + `paid`.
- Useful when the webhook does not arrive.

### 11.5 External E-commerce Integration

Both directions:

1. **Systego → External platform** at checkout:
   - `POST $ECOMMERCE_WEBHOOK_URL` (default `https://bcknd.smartego.org/api/webhooks/custom/order`)
   - `Content-Type: application/x-www-form-urlencoded`
   - `X-API-KEY: $ECOMMERCE_WEBHOOK_API_KEY` or `SUPER_SYSTEGO_API_KEY`
   - Fields: `id` (reference), `customer_name`, `customer_phone`, `total_price`, `currency`.
   - It does not wait for the response; its failure does not fail checkout.
   - If the platform responds with `id`/`_id`/`order_id`/`orderId` → it is stored in `externalOrderId`.

2. **External platform → Systego** via accept/reject with the `x-api-key` key.

> Detailed documentation: [`docs/custom-order-webhook.md`](docs/custom-order-webhook.md).

---

## 12. Common Errors and Fixes

| Message | Cause | Fix |
|---|---|---|
| `Invalid Token` | No `Bearer` or a wrong token | Send `Authorization: Bearer <token>` |
| `Token expired` | 7 days (admin) or 3 days (storefront) have passed | Log in again |
| `Your account is not active` | `user.status != active` | Activate the user from the admin panel |
| `You are not authorized to access this resource` | The role is not `admin`/`superadmin` | Use an admin account |
| `No permission to Add in POS` | The module permission is missing | Edit the role/user permissions |
| `You are not allowed to access another warehouse` | The `warehouse_id` in the request conflicts with the user's warehouse | Remove the field or use your own warehouse |
| `Warehouse is not assigned to this user` | A POS user without a warehouse | Link the user to a warehouse |
| `You must open a cashier shift before creating a sale` | No open shift | `POST /api/admin/cashier-shift/start` |
| `Password is required to apply a free discount` | `free_discount > 0` without `password` | Send the user's password |
| `Invalid password — free discount is not allowed` | Wrong password | Correct it |
| `Sum of payments (...) must equal grand_total (...)` | `financials` does not equal the total | Adjust the amounts |
| `At least one product or bundle is required` | No products and no bundles | Add an item |
| `A sale cannot be both pending and due` | `order_pending=1` with `Due=1` | Choose one |
| `Installment sales must be completed` | Installments with `order_pending=1` | Set `order_pending=0` |
| `No active automatic gateway config found` | An automatic payment method without configuration | Add Paymob/Geidea/Fawry settings |
| `Coupon is out of stock` / `Coupon is expired` | The coupon is unavailable | Use a valid coupon |
| `Not enough stock (concurrent sale)` | A concurrent sale consumed the inventory | Retry/check inventory |
| `Invalid or missing x-api-key` | Wrong webhook key | Use `SUPER_SYSTEGO_API_KEY` |
| `Webhook API key is not configured` | `SUPER_SYSTEGO_API_KEY` is not set | Add it in `.env` |
| `Your subscription has expired` | The organization's subscription has expired | Renew the subscription |
| `The uploaded image is too large` | The request exceeds the limit | Reduce the image size (the limit is actually 20MB) |

---

## 13. Security Notes and Known Gaps

These are real notes extracted from the code, not assumptions:

1. **`/api/sync/*` without authentication**: any party can access the allowed tables' data and push changes. It should be restricted to the local network or protected with a key/token.
2. **`/api/admin/db/gateway`**: raw CRUD on any Model without a module or branch permission check. High risk, and it must be restricted or disabled in production.
3. **`/api/admin/plesk/proxy` before authentication**: the intent is that the key is injected by the server, but the route itself is public. Review whether it is exposed.
4. **96 admin routes without `authorizePermissions`**: they are protected by authentication and role but do not verify the module permission. The most notable are the payment settings (Geidea/Fawry) and some storefront routes.
5. **`DELETE /api/admin/online-orders/:id`** without an explicit permission check.
6. **`public/onlineOrders.ts` and `ecommerceApiKey.ts` are not mounted**: there is no endpoint to create an order from e-commerce. The old documentation mentioned `POST /api/public/online-orders`, which is incorrect.
7. **`ECOMMERCE_API_KEY` is unused**: incoming verification uses only `SUPER_SYSTEGO_API_KEY`. The `ECOMMERCE_WEBHOOK_API_KEY` variable is for the outbound direction only.
8. **No Rate Limiting**: there is no limit on the number of requests on any route.
9. **CORS is open to everyone** (`*`).
10. **Sensitive fields**: some error responses may contain `details` with internal details (especially in `development` for POS sync).
11. **Subscription**: the absence of the `SUPER_SYSTEGO_*` variables makes `requireActiveTenant` pass requests through (`not configured` → `next()`).
12. **The organization data cache is actually 5 minutes** even though the comment in the code mentions 10 minutes.
13. **`entity.too.large`**: the error message says the limit is 10MB while the actual Express setting is 20MB.

---

## 14. Quick References

| File | Content |
|---|---|
| [`docs/API_ENDPOINTS.md`](docs/API_ENDPOINTS.md) | **568 Endpoints** with every Method, Path, Permission, and Handler |
| [`docs/DATABASE_SCHEMA.md`](docs/DATABASE_SCHEMA.md) | **98 Schema/Model** with all fields, types, and references |
| [`docs/custom-order-webhook.md`](docs/custom-order-webhook.md) | External order integration accept/reject |
| [`docs/ecommerce-online-orders-api.md`](docs/ecommerce-online-orders-api.md) | The actual state of the e-commerce integration and the limitations |
| [`docs/installments-ecommerce-frontend.md`](docs/installments-ecommerce-frontend.md) | The installments guide for the e-commerce frontend |
| [`PROJECT_DOCUMENTATION.md`](PROJECT_DOCUMENTATION.md) | The complete architecture guide |
| [`README.md`](README.md) | Quick start and documentation index |

---

> **Final note:** Any route not mentioned here exists in `docs/API_ENDPOINTS.md`.
> When this file conflicts with the code, **the code is the final source of truth**.
