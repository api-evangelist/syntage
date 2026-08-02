---
name: Subscribe to Syntage events with webhooks
description: Register a webhook endpoint, react to extraction/invoice events, and inspect delivery attempts.
api: openapi/syntage-openapi-original.yml
operations: [CreateWebhookEndpoint, ListWebhookEndpoint, GetWebhookEndpoint, UpdateWebhookEndpoint, ListWebhookRequest, GetWebhookRequest, ListEvent]
---

# Subscribe to Syntage events with webhooks

Instead of polling extractions, subscribe to events so your system reacts as fiscal data lands.

## Conventions
- `X-API-Key` header auth; `Accept: application/ld+json`.
- Delivery payloads are `application/ld+json` or `application/json` (configurable per endpoint).
- Verify inbound requests using the signature scheme documented at https://docs.syntage.com/guides/validating-webhook-requests before trusting a payload.

## Steps
1. **Create the endpoint** — `CreateWebhookEndpoint` (`POST /webhook-endpoints`) with your HTTPS URL, the content type, and the event types to receive (e.g. `extraction.updated`, `invoice.created`, `tax_return.updated`, `buro_de_credito_report.created`).
2. **List / confirm** — `ListWebhookEndpoint` (`GET /webhook-endpoints`) and `GetWebhookEndpoint` (`GET /webhook-endpoints/{id}`).
3. **Handle deliveries** — on each POST, verify the signature, then act on the event `type` and its linked resource `@iri`.
4. **Debug** — `ListWebhookRequest` (`GET /webhook-requests`) and `GetWebhookRequest` (`GET /webhook-requests/{id}`) show delivery attempts and response status codes. Reconcile missed events with `ListEvent` (`GET /events`).
5. **Adjust** — `UpdateWebhookEndpoint` (`PUT /webhook-endpoints/{id}`) to change subscribed event types or toggle enabled state.

## Event catalog
See `asyncapi/syntage-webhooks.yml` for the full list of event types (credential.*, extraction.*, invoice.*, tax_return.*, tax_status.*, tax_compliance_check.*, rpc_*, rug_garantia.*, buro_de_credito_report.*, background_check.created, company_verification_report.*, and more).
