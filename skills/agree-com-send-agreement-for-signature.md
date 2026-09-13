---
name: agree-com-send-agreement-for-signature
description: >-
  Send a contract from an Agree.com template out for electronic signature, track signers
  through to full execution, and retrieve the executed PDF. Use when an agent must get a
  document signed, check who has signed, or attach an invoice so the counterparty signs and
  pays in one flow.
generated: '2026-09-12'
method: generated
source: >-
  openapi/agree-com-api-openapi.json (operationIds verified against the spec) +
  https://secure.agree.com/documentation
api: Agree API
base_url: https://secure.agree.com/api/v1
operations:
  - AgreeWeb.API.V1.AgreementController.templates
  - AgreeWeb.API.V1.AgreementController.create
  - AgreeWeb.API.V1.AgreementController.send
  - AgreeWeb.API.V1.AgreementController.show
  - AgreeWeb.API.V1.AgreementController.pdf
---

# Send an agreement for signature (Agree.com)

## Before you start

Base URL is `https://secure.agree.com/api/v1` — **not** the `api.agree.com` host the
documentation's examples use, which does not resolve. Auth is
`Authorization: Bearer <API_KEY>`.

**You cannot author a contract through this API.** Templates are read-only: there is no
create, update or delete operation for them. An agent can send a contract; a human must first
create the template in the dashboard. Plan the workflow around that constraint.

## Steps

### 1. Find the template

`AgreeWeb.API.V1.AgreementController.templates` — `GET /api/v1/agreements/templates`

Returns the templates available to the organization. Take the `id` of the one you need. If you
cannot find a matching template, stop and ask a human — do not substitute a different one.

### 2. Create the agreement

`AgreeWeb.API.V1.AgreementController.create` — `POST /api/v1/agreements`

```json
{
  "template_id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Service Agreement",
  "recipients": [
    { "contact_id": "770e8400-e29b-41d4-a716-446655440000", "role": "owner" },
    { "email": "counterparty@example.com", "name": "Jane Smith", "role": "signer" }
  ]
}
```

Rules that will fail your request if you miss them:

- **Exactly one recipient must have the `owner` role** — the account holder. Not zero, not two.
- Recipients are supplied either by `contact_id` or inline with contact details. Inline
  details create or resolve a contact by email.
- Fields are bound to specific recipients through `assigned_fields`. A signature field with no
  assignee has no one to sign it.
- `delivery_mode` controls email: `managed` means Agree emails the recipients, `embedded`
  suppresses emails because you are hosting the signing experience yourself.

Creating does **not** send. That separation is deliberate and you should use it.

### 3. Send it

`AgreeWeb.API.V1.AgreementController.send` — `POST /api/v1/agreements/{id}/send`

This is the irreversible step — it puts a contract in front of a counterparty.

**There is no idempotency key on this API.** If the call times out, do not blind-retry; fetch
the agreement with `show` and read its `status` before deciding. A duplicate send is a contract
your counterparty receives twice.

`POST /api/v1/agreements/create_and_send` does both steps at once. Avoid it for unattended
work, for the same reason.

### 4. Track signing

`AgreeWeb.API.V1.AgreementController.show` — `GET /api/v1/agreements/{id}`

Read `status`, `current_signing_order` and `recipients[]`. When `signing_order_enabled` is
true, recipients sign in sequence and `current_signing_order` tells you whose turn it is.

Prefer webhooks over polling — `agreement.sent`, `agreement.signed` (fires once **per
signer**) and `agreement.executed` (fires once, when **all** signers have signed). Treat
`agreement.executed` as the completion signal; `agreement.signed` is not completion.

Agreement webhook payloads carry flat `recipients[].email` and `recipients[].name` plus
`field_values`, a map of `field_id` to filled plain-text values. Nested `recipients[].user`
and `recipients[].contact` objects are still sent for backwards compatibility — **use the flat
fields**, per the provider's own guidance.

### 5. Retrieve the executed PDF

`AgreeWeb.API.V1.AgreementController.pdf` — `GET /api/v1/agreements/{id}/pdf`

May return **202** with `Retry-After` (default 3 seconds) and
`{"data": {"status": "pending", "retry_after_seconds": 3}}` while the document renders. Repeat
the same GET until 200 with `data.url`. Honor `Retry-After`; do not tighten the loop.

## Sign-and-pay in one flow

An Invoice carries an `agreement_id`. Attaching an invoice to an agreement is what lets a
counterparty sign and pay in a single flow — it is the product's core differentiator. Create
the agreement first, then create the invoice referencing it. See the
`agree-com-invoice-and-collect` skill.

## Undoing things

`DELETE /api/v1/agreements/{id}` is a **soft delete** — the record is retained and `deleted_at`
is stamped, but it stops resolving through the API and there is **no restore operation**. From
an agent's point of view it is one-way.

**No window is documented.** Agree does not say whether an already-executed agreement can be
deleted, or what deletion means for a contract that is legally in force. Do not assume you can
retract a signed contract; escalate to a human.

## Caveats worth stating to your user

Agree.com publishes no ESIGN/UETA or eIDAS conformance signal in its contract — no audit
trail, certificate of completion, tamper-evidence hash, or signer-authentication assertion is
exposed through the API. If the legal defensibility of a signature matters for the task at
hand, say so rather than implying it is established.
