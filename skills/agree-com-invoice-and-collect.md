---
name: agree-com-invoice-and-collect
description: >-
  Create an invoice in Agree.com, send it to a customer with a payment link, and track it to
  paid. Use when an agent must bill a customer, set up a recurring retainer, or confirm that
  a payment landed. Covers the safe two-step send path, the missing-idempotency hazard, and
  how to read the result without polling.
generated: '2026-09-12'
method: generated
source: >-
  openapi/agree-com-api-openapi.json (operationIds verified against the spec) +
  https://secure.agree.com/documentation
api: Agree API
base_url: https://secure.agree.com/api/v1
operations:
  - AgreeWeb.API.V1.ContactController.index
  - AgreeWeb.API.V1.ContactController.create
  - AgreeWeb.API.V1.InvoiceController.create
  - AgreeWeb.API.V1.InvoiceController.send
  - AgreeWeb.API.V1.InvoiceController.create_and_send
  - AgreeWeb.API.V1.InvoiceController.show
  - AgreeWeb.API.V1.InvoiceController.index
  - AgreeWeb.API.V1.InvoiceController.delete
  - AgreeWeb.API.V1.InvoiceController.receipt_pdf
---

# Invoice a customer and collect payment (Agree.com)

## Before you start

**Base URL is `https://secure.agree.com/api/v1`.** Agree's own documentation tells you to use
`https://api.agree.com/api/v1`. That host does not resolve — it has no DNS record. Use the
host from the OpenAPI `servers[]` block.

Authenticate every request with `Authorization: Bearer <API_KEY>`. The key is
organization-wide and unscoped: it can read every contract, invoice and revenue figure in the
account, and it can move money. Treat it accordingly.

## The hazard you must plan for first

**This API has no idempotency mechanism.** There is no `Idempotency-Key` header and no request
deduplication anywhere. If `POST /api/v1/invoices/create_and_send` times out and you retry it,
you have sent your customer two invoices and two payment links, and there is no way to detect
that from the API.

So: **do not use `create_and_send` unattended.** Use the two-step path below, which the
provider itself recommends. It splits the reversible act (creating a draft) from the
irreversible one (emailing a customer), and gives you an invoice `id` to check before you
send.

If a `create` call times out, do **not** blind-retry. Call
`AgreeWeb.API.V1.InvoiceController.index` (`GET /api/v1/invoices`) filtered by status and
date first, and look for the invoice you may have already created. Set `external_id` on every
invoice you create so you have a handle to match on — it is the only correlation field the API
offers.

## Steps

### 1. Resolve or create the contact

You can skip this. Supplying `billing_contact.email` on the invoice creates or finds the
contact automatically ("Creates or updates a contact"). Resolve explicitly only when you need
the contact `id` for something else.

- `AgreeWeb.API.V1.ContactController.index` — `GET /api/v1/contacts`
- `AgreeWeb.API.V1.ContactController.create` — `POST /api/v1/contacts`

Email is unique within an organization, so creating a contact that already exists returns
`422` with `{"errors": {"email": ["has already been taken"]}}`. That is a benign outcome, not
a failure — fall through to listing.

### 2. Create the invoice without sending it

`AgreeWeb.API.V1.InvoiceController.create` — `POST /api/v1/invoices`

```json
{
  "invoice": {
    "billing_contact": { "email": "customer@example.com", "name": "John Doe", "company": "Acme Corp" },
    "amount": { "amount": 15000, "currency": "USD" },
    "payment_methods": ["card", "ach"],
    "due_at": "2026-10-15T00:00:00Z",
    "scheduled_at": "2026-10-01T00:00:00Z",
    "memo": "Website development - Phase 1",
    "external_id": "<your own correlation id>"
  }
}
```

`amount.amount` is **minor units** — 15000 is $150.00. On `create`, both `due_at` and
`scheduled_at` are required. All dates are ISO 8601 UTC.

For a recurring retainer, add `recurring_options` (`schedule`, `repeat_frequency`,
`repeat_unit`, `repeat_on_type`, `repeat_on_day`, `recurring_end_type`, `reminder_schedule`)
instead of managing a schedule yourself.

The response is `{"data": {...}}` carrying `id`, `status` and `payment_link`.

### 3. Check it, then send it

Read back what you created before it reaches a human.

- `AgreeWeb.API.V1.InvoiceController.show` — `GET /api/v1/invoices/{id}`
- `AgreeWeb.API.V1.InvoiceController.send` — `POST /api/v1/invoices/{id}/send`

Sending emails the payment link to the billing contact. **This is the irreversible step.**

`status` may briefly read `sending` — a temporary value that is *not* in the schema's status
enum. Handle it, and handle `processing`, which is also documented but not declared.

### 4. Know when it is paid — do not poll

Register a webhook once and react to `invoice.paid` and `invoice.failed`. See the
`agree-com-webhook-intake` skill. Polling `GET /api/v1/invoices` on a schedule is the wrong
shape here and the provider says so.

If you must reconcile in batch, filter:

```
GET /api/v1/invoices?statuses=paid&date_type=paid_at&date_start=2026-09-01&date_end=2026-09-30
```

`statuses` accepts a comma-separated list from `created, due, sent, canceled, paid, failed,
refunded, draft`. Pagination is `page` and `page_size` (default 10, **max 100**), with
`total_pages` and `total_entries` in the `pagination` envelope.

### 5. Receipt

`AgreeWeb.API.V1.InvoiceController.receipt_pdf` — `GET /api/v1/invoices/{id}/receipt_pdf`

Only valid when `status` is `paid`. Any other status returns **422**, not 404. Like the
invoice PDF, it may return **202** with `Retry-After` (default 3s) while rendering — repeat the
same GET until you get 200 with `data.url`.

## Undoing things

- **Before payment:** `AgreeWeb.API.V1.InvoiceController.delete` — `DELETE /api/v1/invoices/{id}`
  cancels the invoice and fires `invoice.canceled`. **No window is documented** — Agree does
  not state whether this works after the invoice is sent, due, or processing. Do not promise a
  user it will work; try it and read the response.
- **After payment: you cannot.** There is no refund, void, or reversal operation in this API.
  The `refunded` status and the `invoice.refunded` webhook exist, so refunds happen in the
  product — but not through the API. If a refund is needed, hand off to a human with the
  invoice `id`. Say this plainly rather than implying you can undo a collection.

## Errors

| Status | Envelope | What to do |
|---|---|---|
| 400 | `{"error": "..."}` | Check pagination bounds and date formats |
| 401 | `{"error": "..."}` | Key missing, invalid, or rotated |
| 403 | `{"error": "..."}` | The resource belongs to another organization |
| 404 | `{"error": "..."}` | Bad UUID, or the record was soft-deleted |
| 422 | `{"errors": {"field": ["msg"]}}` | Read the keys as request field names and fix each |

Not RFC 9457, and **no 5xx is documented anywhere** — treat any 5xx as unknown state and
reconcile by listing rather than retrying a write. There are no rate-limit headers and no
documented 429.
