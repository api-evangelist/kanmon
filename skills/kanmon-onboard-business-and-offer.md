---
name: Onboard a business and start an embedded lending session
description: Create a business and its primary owner, then hand the borrower into the embedded Kanmon Connect flow to see prequalified offers.
api: openapi/kanmon-openapi-original.yml
operations: [createBusiness, createUser, createConnectToken, createEmbeddedSession, getAllOffers]
---

# Onboard a business and start an embedded lending session

Use the Kanmon Public V2 API to onboard a borrower and launch Kanmon Connect.

## Setup
- Base URL: `https://api.kanmon.dev` (sandbox) or `https://api.kanmon.com` (production).
- Auth: send your platform API key in the `Authorization` header on every request.

## Steps
1. **Create the business** — `POST /api/platform/v2/businesses` (`createBusiness`). Pass your own `platformBusinessId`; a `409 BusinessAlreadyExistsException` means it already exists — fetch it with `getAllBusinesses` filtered by `platformBusinessIds`.
2. **Create the primary owner** — `POST /api/platform/v2/users` (`createUser`) with the business id. A `409` (`PrimaryBusinessOwnerAlreadyExistsForBusinessException` / `UserAlreadyExistsWithEmailException`) means the user already exists.
3. **Mint a Connect token** — `POST /api/platform/v2/connect-tokens` (`createConnectToken`) for the user. This short-lived token initializes the embedded UI.
4. **Start the embedded session** — `POST /api/platform/v2/embedded-session` (`createEmbeddedSession`), then mount `@kanmon/web-sdk` (or `@kanmon/react-native-sdk`) with the token so the borrower completes onboarding inside your app.
5. **Read offers** — poll `GET /api/platform/v2/offers` (`getAllOffers`) to see financing offers extended to the business.

## Rules
- Paginate collection reads with `offset` / `limit`.
- Respect `X-RateLimit-Remaining`; back off on `429 TooManyRequestsException`.
- Errors use `{ errorCode, message, timestamp }` — branch on `errorCode` (see errors/kanmon-problem-types.yml).
