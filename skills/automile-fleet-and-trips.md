---
name: List Automile fleet vehicles and their trips
description: Authenticate to the Automile API and read the fleet, a vehicle's detail and health, and its trips.
api: openapi/automile-openapi-original.json
operations: [GetResourceOwnerVehicles, GetResourceOwnerVehicle, GetResourceOwnerVehicleHealth, GetResourceOwnerTrips, GetResourceOwnerTrip]
---

# List Automile fleet vehicles and their trips

Read-only fleet visibility over the Automile telematics API (`https://api.automile.com`, `/v1`).

## Auth
OAuth2 (scopes `read`, `write`). Obtain a bearer token via a private OAuth client
(API client id + secret) plus account credentials, then send it as
`Authorization: Bearer <token>` on every request. See
`authentication/automile-authentication.yml`.

## Steps
1. `GetResourceOwnerVehicles` — list every vehicle in the account. Capture each `vehicleId`.
2. `GetResourceOwnerVehicle` — fetch one vehicle's detail by `vehicleId`.
3. `GetResourceOwnerVehicleHealth` — read OBD/vehicle-health for a `vehicleId` (some PIDs return `501 Not implemented` when the vehicle does not record them — treat as "unsupported", not an error).
4. `GetResourceOwnerTrips` — list trips; filter by `vehicleId` and a date range (`fromDate`/`toDate`) rather than paging.
5. `GetResourceOwnerTrip` — fetch a single `tripId` for detail.

## Conventions
- No idempotency key; these are reads so safe to retry.
- Collections filter by id + date range, not cursor pagination (`conventions/automile-conventions.yml`).
- Errors are HTTP-status keyed with a message body: `403` = no access to that vehicle, `404` = not found (`errors/automile-problem-types.yml`).
