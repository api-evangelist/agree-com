---
name: agree-com-webhook-intake
description: >-
  Register an Agree.com webhook endpoint, verify signed deliveries, and process the twelve
  invoice and agreement events safely. Use when an agent needs to react to payments landing,
  payments failing, or contracts being signed, instead of polling the API.
generated: '2026-09-12'
method: generated
source: >-
  openapi/agree-com-api-openapi.json (operationIds verified against the spec) +
  https://secure.agree.com/documentation#tag/Webhooks +
  asyncapi/agree-com-webhooks-asyncapi.yml
api: Agree API
base_url: https://secure.agree.com/api/v1
operations:
  - AgreeWeb.API.V1.WebhookEndpointController.create
  - AgreeWeb.API.V1.WebhookEndpointController.index
  - AgreeWeb.API.V1.WebhookEndpointController.test
---

# Receive and verify Agree.com webhooks

## Path warning

Agree's prose documentation writes this resource as `/api/v1/webhook_endpoints` in every
example. **The OpenAPI declares `/api/v1/webhooks`.** The declared paths are the ones that
exist; use those. Base URL is `https://secure.agree.com/api/v1`.

## 1. Register the endpoint

`AgreeWeb.API.V1.WebhookEndpointController.create` — `POST /api/v1/webhooks`

```json
{
  "webhook_endpoint": {
    "url": "https://your-app.example.com/webhooks/agree",
    "events": ["invoice.paid", "invoice.failed"]
  }
}
```

The response contains a `secret` (prefixed `whsec_`). **It is returned exactly once and is
never retrievable again.** Persist it to your secret store in the same operation that creates
the endpoint; if you lose it you must delete the endpoint and create a new one. HTTPS is
required in production.

Check for an existing endpoint first with
`AgreeWeb.API.V1.WebhookEndpointController.index` (`GET /api/v1/webhooks`) rather than
creating duplicates — there is no idempotency on this call either.

## 2. Subscribe to the right events

Twelve are published. Start with `invoice.paid` and `invoice.failed`, which the provider calls
"the most important for payment integrations".

| Event | Fires |
|---|---|
| `invoice.created` | after `POST /invoices` |
| `invoice.sent` | when delivery completes |
| `invoice.due` | when status becomes `due` |
| `invoice.paid` | after payment confirmation |
| `invoice.failed` | after payment rejection — customer can retry the same link |
| `invoice.canceled` | after `DELETE /invoices` |
| `invoice.refunded` | after a refund is processed **outside the API** |
| `agreement.created` | after `POST /agreements` |
| `agreement.sent` | when status becomes `sent` |
| `agreement.signed` | when **a** recipient signs (once per signer) |
| `agreement.executed` | when **all** signers have signed |
| `webhook.test` | when you trigger a test |

Treat `agreement.executed` as completion, not `agreement.signed`.

## 3. Verify every delivery

Headers on each POST:

- `X-Webhook-Signature` — HMAC-SHA256 of the **raw** request body, keyed by your endpoint
  secret, hex-encoded lowercase
- `X-Webhook-Timestamp` — Unix timestamp when sent

Procedure:

1. Capture the raw body **before** JSON parsing. Parsing and re-serializing changes the bytes
   and the signature will not match.
2. Compute `HMAC-SHA256(raw_body, secret)`, hex lowercase.
3. Compare against the header with a **constant-time** comparison
   (`crypto.timingSafeEqual`, `hmac.compare_digest`).
4. Reject with 401 on mismatch. Never skip verification.

A gap to be aware of: the timestamp header is sent but Agree's documented verification recipe
does not include it in the signed payload and states no tolerance window, so the published
procedure does not by itself defeat replay. If replay matters to you, record delivered event
identifiers and reject repeats on your side.

## 4. Process safely

Body shape:

```json
{ "event": "invoice.paid", "payload": { /* the complete resource object */ } }
```

`payload` is the full resource, so you do not need a follow-up API call to get the details.

- **Return 200 within 5 seconds**, then do the work asynchronously. Slow handlers get retried.
- **Delivery is at-least-once.** The provider states events "may occasionally be sent more than
  once; use idempotency" — on *your* side. Deduplicate on the resource `id` plus `event`, and
  make your handlers idempotent. This matters more than usual here, because the Agree write
  API offers you no idempotency to lean on either.
- Handle unknown event names without failing; the catalog can grow.

## 5. Retries and health

If your endpoint returns non-2xx or times out, Agree retries five times: immediate, ~1 minute,
~5 minutes, ~30 minutes, ~2 hours. After the fifth failure the delivery is marked failed and
the endpoint's `failure_count` increments.

Monitor `failure_count` via `GET /api/v1/webhooks` — a rising count is the only health signal
Agree gives you, and there is no status page to correlate it against.

## 6. Test it

`AgreeWeb.API.V1.WebhookEndpointController.test` — `POST /api/v1/webhooks/test`

Delivers a `webhook.test` event. Returns **404** with
`{"error": "No endpoints subscribed to webhook.test"}` if no endpoint subscribes to it — so
include `webhook.test` in your `events` array if you want to use this.

## Turning it off

Set `active: false` with `PUT /api/v1/webhooks/{id}` to pause delivery without losing the
endpoint and its secret. Deleting is permanent and costs you the secret.
