---
name: Reconcile issued products and payments
description: List a business's issued products and reconcile their draw requests and payments back to your platform records.
api: openapi/kanmon-openapi-original.yml
operations: [getAllIssuedProducts, getAllDrawRequests, getPaymentIntents, getPaymentById]
---

# Reconcile issued products and payments

Use the Kanmon Public V2 API to reconcile funding activity for a business.

## Setup
- Auth: platform API key in the `Authorization` header.

## Steps
1. **List issued products** — `GET /api/platform/v2/issued-products` (`getAllIssuedProducts`), filter by business.
2. **List draw requests** — `GET /api/platform/v2/draw-requests` (`getAllDrawRequests`) to see draws against lines of credit.
3. **List payment intents** — `GET /api/platform/v2/payment-intents` (`getPaymentIntents`).
4. **Resolve a payment** — `GET /api/platform/v2/payments/{id}` (`getPaymentById`) for details; a `404 PaymentOrderNotFoundException` means the id is unknown.

## Rules
- Reconcile against the `platform*Id` fields on each resource (platformBusinessId, platformInvoiceId, ...).
- Paginate with `offset` / `limit`; honor `X-RateLimit-Remaining`.
- Subscribe to webhooks (USER.CREATED, BUSINESS.NEW_ACCOUNT — see asyncapi/kanmon-webhooks.yml) instead of tight polling where possible.
