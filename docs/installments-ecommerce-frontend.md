# Installments — E-commerce frontend guide

This version is based on the same implementation as the POS, and the same endpoints return the same shape
so that the frontend gets the same behavior. **Installment collection happens only at the branch / by the admin** — there is no
online payment for installments.

---

## 1) Before anything: installment settings

The admin sets the interest from the admin panel:

```
GET /api/admin/installments/settings
PUT /api/admin/installments/settings
```

`PUT` body:

```json
{
  "enabled": true,
  "interest_type": "percentage",   // "percentage" | "fixed"
  "interest_value": 10,            // 10% of the financed amount, or 50 EGP per installment
  "max_installments": 12,
  "min_down_payment_percent": 0
}
```

Meaning:

| Field | Meaning |
|---|---|
| `interest_type = "percentage"` | Interest = `(order total - down payment) × interest_value / 100` |
| `interest_type = "fixed"` | Interest = `interest_value × number of installments` |
| `max_installments` | Maximum allowed number of installments |
| `min_down_payment_percent` | Minimum allowed down payment as a percentage of the order total |

> Interest is calculated **only** if the customer chose to pay in installments. Full payment is not affected.

---

## 2) Endpoints available for e-commerce

### 2.1 Installment settings (public — before login)

```
GET /api/store/installments/settings
```

Response:

```json
{
  "success": true,
  "data": {
    "message": "Installment settings fetched successfully",
    "settings": {
      "enabled": true,
      "interest_type": "percentage",
      "interest_value": 10,
      "max_installments": 12,
      "min_down_payment_percent": 0
    }
  }
}
```

Use it to show/hide the "Installments" option in the checkout, and to determine the maximum number of installments.

### 2.2 Plan preview (before confirming the order)

```
POST /api/store/installments/preview
Content-Type: application/json
```

Body:

```json
{
  "total": 5000,
  "months": 6,
  "down_payment": 500
}
```

Response:

```json
{
  "success": true,
  "data": {
    "message": "Installment plan preview",
    "plan": {
      "order_total": 5000,
      "down_payment": 500,
      "financed_amount": 4500,
      "interest_type": "percentage",
      "interest_value": 10,
      "interest_amount": 450,
      "total_payable": 5450,
      "installments_count": 6,
      "start_date": "2026-10-25T10:00:00.000Z",
      "installments": [
        { "sequence": 1, "amount": 825, "due_date": "2026-11-25T10:00:00.000Z" },
        { "sequence": 2, "amount": 825, "due_date": "2026-12-25T10:00:00.000Z" },
        { "sequence": 3, "amount": 825, "due_date": "2027-01-25T10:00:00.000Z" },
        { "sequence": 4, "amount": 825, "due_date": "2027-02-25T10:00:00.000Z" },
        { "sequence": 5, "amount": 825, "due_date": "2027-03-25T10:00:00.000Z" },
        { "sequence": 6, "amount": 825, "due_date": "2027-04-25T10:00:00.000Z" }
      ]
    }
  }
}
```

Notes:

- The last installment absorbs the rounding difference, so the sum of the installments = `total_payable - down_payment` to the cent.
- The first installment is due one month after the order date.
- Any input error (down payment greater than the total, months > the limit, installments disabled…)
  returns `400` in this shape:

```json
{
  "success": false,
  "error": { "code": 400, "message": "Minimum down payment is 10% (500)" }
}
```

### 2.3 Creating an order with installments

The same existing endpoint:

```
POST /api/store/order/checkout
```

With these two fields added:

```json
{
  "orderType": "delivery",
  "shippingAddress": "<address id or object as is>",
  "paymentMethod": "<ObjectId of an active payment method>",
  "paymentType": "installment",
  "installment": {
    "months": 6,
    "down_payment": 500,
    "notes": "optional notes"
  }
}
```

Important rules:

1. `paymentType` defaults to `"full"`. If it is `"installment"`, then `installment` must be present.
2. **The customer must be logged in** (the order is linked to their account).
3. `paymentMethod` is required as usual, but **no amount will be charged from the gateway**;
   the backend forces `paymentGateway = "manual"` and `paymentStatus = "unpaid"`.
   The down payment and the installments are collected at the branch.
4. `down_payment` must:
   - `>= min_down_payment_percent%` of the order total
   - `<` the order total
5. `months` is an integer from `1` to `max_installments`.

Response (201):

```json
{
  "success": true,
  "data": {
    "message": "Order created successfully",
    "order": {
      "_id": "6700...",
      "paymentType": "installment",
      "paymentStatus": "unpaid",
      "paymentGateway": "manual",
      "installmentPlan": "6701...",
      "totalPriceAfterDiscount": 5000
    },
    "payment": null,
    "installment_plan": {
      "_id": "6701...",
      "plan_number": "INS-261025-1234",
      "source": "ecommerce",
      "order_id": "6700...",
      "order_total": 5000,
      "down_payment": 500,
      "financed_amount": 4500,
      "interest_type": "percentage",
      "interest_value": 10,
      "interest_amount": 450,
      "total_payable": 5450,
      "installments_count": 6,
      "paid_amount": 0,
      "remaining_amount": 4950,
      "status": "active",
      "installments": [
        {
          "_id": "6702...",
          "sequence": 1,
          "amount": 825,
          "due_date": "2026-11-25T10:00:00.000Z",
          "paid_amount": 0,
          "remaining_amount": 825,
          "status": "pending"
        }
      ]
    }
  }
}
```

`remaining_amount` in the plan = `total_payable - down_payment - what has been paid from the installments`.
The `down_payment` itself is not part of `paid_amount` (it is recorded in the invoice/order).

### 2.4 Customer installments (after the order)

All of them require `Authorization: Bearer <customer token>`:

```
GET /api/store/installments
```

```json
{
  "success": true,
  "data": {
    "message": "Installment plans fetched successfully",
    "count": 1,
    "total_payable": 5450,
    "paid_amount": 0,
    "remaining_amount": 4950,
    "plans": [
      {
        "_id": "6701...",
        "plan_number": "INS-261025-1234",
        "source": "ecommerce",
        "order_id": "6700...",
        "order_total": 5000,
        "down_payment": 500,
        "total_payable": 5450,
        "installments_count": 6,
        "paid_amount": 0,
        "remaining_amount": 4950,
        "status": "active",
        "customer_id": { "_id": "...", "name": "..." },
        "warehouse_id": { "_id": "...", "name": "Branch" }
      }
    ]
  }
}
```

```
GET /api/store/installments/:id            // plan details + installment schedule
GET /api/store/installments/order/:orderId // plan for a specific order
```

Installment statuses (`status`): `pending` / `paid` / `overdue` / `cancelled`.
Plan status (`status`): `active` / `completed` / `cancelled`.

---

## 3) Required shape in the e-commerce frontend (like the POS)

### Payment step in the checkout

1. When the checkout loads, call `GET /api/store/installments/settings`.
2. If `enabled = true`, show the **"Installments"** option next to cash/card.
3. When the customer selects it:
   - A selector for the number of installments: `1..max_installments`.
   - An input for the down payment, with `min = min_down_payment_percent% × total`.
   - Debounce ~350ms, then call `POST /api/store/installments/preview`.
   - Show a summary:
     - The financed amount
     - The interest
     - The total installment amount
     - The monthly installment (and the last installment if different)
     - **Due now (the down payment)**
   - A "Show installment schedule" button (accordion) with the due dates.
4. The "Confirm order" button is disabled if:
   - installments are disabled
   - the number of installments is invalid
   - the down payment is below the allowed minimum
   - the preview is still loading or returned an error
5. On confirmation, send `paymentType: "installment"` + `installment` along with the same current checkout data.

### After the order is created

- Show `installment_plan.plan_number` on the success screen.
- Explain to the customer: **installments are collected at the branch/by the admin**, and there is no online payment for installments.
- Show the down payment and the first installment date.

### "My installments" page

- The plan list from `GET /api/store/installments`.
- For each plan: plan number, total installment amount, paid, remaining, status.
- Details: the installment schedule with dates and statuses (overdue = warning color).
- **No payment button** in the e-commerce frontend (collection is at the branch).

---

## 4) Important differences from the POS

| Item | POS | E-commerce |
|---|---|---|
| Plan creation | Automatic with `POST /api/admin/pos/sales` | Automatic with `POST /api/store/order/checkout` |
| Down payment | Collected immediately into a financial account (`financials`) | Collected later at the branch |
| Installment payment | `PUT /api/admin/pos/installments/plans/:planId/items/:itemId/pay` | Not available — from the branch/admin only |
| Viewing installments | The "Installments" page in the POS | The "My installments" page in the storefront |

---

## 5) Implementation notes

- Use the server `preview` instead of calculating the interest in the frontend — so that the displayed numbers
  match exactly what will be recorded.
- The order returns `paymentStatus: "unpaid"` with an installment plan; do not treat that as a payment error.
- If the API returns `400`, display `error.message` as is (clear messages in Arabic/English).
- Any change to the interest settings by the admin applies only to new orders;
  old plans keep the interest they had at the time of creation.
