---
name: Finance an invoice
description: Submit an invoice for financing against a business's issued invoice-financing product and track the resulting payment schedule.
api: openapi/kanmon-openapi-original.yml
operations: [getAllIssuedProducts, financeInvoice, getInvoice, getPaymentScheduleForAIssuedProduct]
---

# Finance an invoice

Use the Kanmon Public V2 API to finance an invoice for an onboarded business.

## Setup
- Auth: platform API key in the `Authorization` header.

## Steps
1. **Confirm the product** — `GET /api/platform/v2/issued-products` (`getAllIssuedProducts`) for the business to confirm it holds an invoice-financing product. A `BusinessHasNoInvoiceFinancingProductException` on step 2 means it does not.
2. **Finance the invoice** — `POST /api/platform/v2/invoices/finance` (`financeInvoice`). Provide your unique `platformInvoiceId`; a `409` (`PlatformInvoiceIdAlreadyExistsException`) means that invoice was already submitted. Watch for `InvalidInvoiceDueDateException`, `IncorrectFinancingAmountException`, and `InsufficientCreditLimitException`.
3. **Read the invoice** — `GET /api/platform/v2/invoices/{id}` (`getInvoice`) to confirm status.
4. **Track repayment** — `GET /api/platform/v2/issued-products/{id}/payment-schedule` (`getPaymentScheduleForAIssuedProduct`) for the repayment plan.

## Rules
- There is no idempotency key; the `platformInvoiceId` uniqueness constraint is the duplicate guard.
- Errors use `{ errorCode, message, timestamp }`; branch on `errorCode`.
