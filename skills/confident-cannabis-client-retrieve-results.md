---
name: Retrieve test orders and results as a Confident Cannabis client
description: As a testing client, list your labs and orders, page through samples, and pull detailed sample results including a Certificate of Analysis.
api: openapi/confident-cannabis-client-openapi.json
operations: [getClient, getLabs, getOrders, getSamples, getSampleDetails]
---

# Retrieve orders and results (client)

Operating instructions for an agent acting as an authenticated **client** on the
Confident Cannabis v0 API. Client endpoints are read-only (GET) and require only
`X-ConfidentCannabis-APIKey` (no signing).

## Steps
1. **getClient** — `GET /v0/clients/client` to confirm the authenticated client
   organization.
2. **getLabs** — `GET /v0/clients/labs` to list associated labs (paged).
3. **getOrders** — `GET /v0/clients/orders` to list orders. Supports filtering
   by status and modification time; paginate with `start`/`limit` and follow the
   `more_results` flag.
4. **getSamples** — `GET /v0/clients/samples` to list samples (filter by status,
   batch_id, harvest_id, modified time).
5. **getSampleDetails** — `GET /v0/clients/sample/{sample_id}` for full results.
   `sample_id` accepts `lab_internal_id`, `public_key`, or `regulator_id_to_attach`.

## Conventions
Offset pagination: `start` (default 0), `limit` (default/max 100), response
`more_results` boolean. See `conventions/confident-cannabis-conventions.yml`.
Errors use the custom `{success,error_code,error_message,error_details}` envelope.
