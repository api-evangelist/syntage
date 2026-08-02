---
name: Onboard an entity and extract SAT fiscal data
description: Register a Mexican business, attach SAT credentials, run an extraction, and read the resulting invoices.
api: openapi/syntage-openapi-original.yml
operations: [AddEntity, CreateCredential, CreateExtraction, GetExtraction, ListEntityEvents, ListTaxpayerInvoice]
---

# Onboard an entity and extract SAT fiscal data

Use the Syntage API to onboard a taxpayer and pull their fiscal data from Mexico's SAT.

## Conventions (read first)
- Authenticate every request with the `X-API-Key` header. Sandbox and production have **different** keys. Start in sandbox (`https://api.sandbox.syntage.com`), where SAT interaction is simulated and no real credentials are needed.
- Set `Accept: application/ld+json`. Responses are JSON-LD/Hydra; collections carry `hydra:member` and `hydra:view`.
- Optionally pin the API version with `Accept-Version: 2020-06-28`.
- Watch `X-RateLimit-Remaining`; a `429` means back off until `X-RateLimit-Reset`.

## Steps
1. **Create the entity** — `AddEntity` (`POST /entities`). Capture the returned entity `id` (used as `entityId`).
2. **Attach SAT credentials** — `CreateCredential` (`POST /credentials`) for the entity. In sandbox these are simulated.
3. **Start an extraction** — `CreateExtraction` (`POST /extractions`) scoped to the entity/credential. This is asynchronous.
4. **Track progress** — poll `GetExtraction` (`GET /extractions/{id}`) or `ListEntityEvents` (`GET /entities/{entityId}/events`), or subscribe to webhooks (see the webhooks skill). An extraction that fails returns an `ExtractionErrorCode` such as `invalid_credentials`, `login_failed`, `sat_unavailable`, or `unrecoverable`.
5. **Read the data** — once extraction events report completion, list invoices with `ListTaxpayerInvoice` (`GET /entities/{entityId}/invoices`). This collection supports cursor pagination via `X-Pagination-Style: cursor`.

## Error handling
- `401` — bad or wrong-environment API key.
- `409` — an extraction conflict (a concurrent/duplicate extraction is in flight); retry after it finishes.
- `422` — a Hydra `ConstraintViolation`; fix the reported fields.
