---
name: Subscribe to Automile events and configure triggers
description: Register a webhook subscription for fleet events and create alert triggers on the Automile API.
api: openapi/automile-openapi-original.json
operations: [GetPublishSubscribe, CreatePublishSubscribe, GetResourceOwnerTriggers, CreateResourceOwnerTrigger]
---

# Subscribe to Automile events and configure triggers

Set up event delivery and alerting over the Automile API (`https://api.automile.com`, `/v1`).

## Auth
OAuth2 bearer token with `write` scope (`authentication/automile-authentication.yml`).

## Steps
1. `GetPublishSubscribe` — list existing webhook subscriptions to avoid duplicates.
2. `CreatePublishSubscribe` — register a callback URL and the event types to receive. Automile then HTTPS-POSTs events to that URL. Available events: `trip.start`, `trip.end`, `vehicle.created`, `vehicle.modified`, `contact.created`, `contact.modified`, `device.connected`, `device.disconnected` (`asyncapi/automile-webhooks.yml`).
3. Verify delivery by calling the per-subscription test triggers (e.g. `GET /v1/resourceowner/publishsubscribe/{publishSubscribeId}/testtripstart`) which fire a sample payload at your URL.
4. `GetResourceOwnerTriggers` — list configured alert triggers.
5. `CreateResourceOwnerTrigger` — create a trigger (e.g. speeding, disconnect) for a vehicle.

## Conventions
- No idempotency contract — check `GetPublishSubscribe` before `CreatePublishSubscribe` to avoid duplicate subscriptions.
- Errors: `400` bad body, `403` no access to the referenced vehicle, `404` subscription not found (`errors/automile-problem-types.yml`).
