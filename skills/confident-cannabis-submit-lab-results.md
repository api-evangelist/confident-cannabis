---
name: Submit lab test results on Confident Cannabis
description: As a testing lab, create an order, run it through its status lifecycle, submit per-compound test results, and attach a Certificate of Analysis.
api: openapi/confident-cannabis-lab-openapi.json
operations: [createOrder, verifyOrder, submitTestResults, uploadSampleCOA, completeOrder]
---

# Submit lab test results

Operating instructions for an agent acting as an authenticated **lab** on the
Confident Cannabis v0 API (`https://api.confidentcannabis.com`).

## Auth
Every request sends `X-ConfidentCannabis-APIKey`. If request signing is enabled
for the lab, also send `X-ConfidentCannabis-Signature`
(`CC0-HMAC-SHA256:x-confidentcannabis-timestamp:<hash>`, HMAC-SHA256) and
`X-ConfidentCannabis-Timestamp` (Unix seconds, must be within 30s). Exclude any
file field from signature generation. See
`authentication/confident-cannabis-authentication.yml` and
`conventions/confident-cannabis-conventions.yml`.

## Steps
1. **createOrder** — `POST /v0/labs/order` with one or more samples. The new
   order starts in status_id 1 (Placed).
2. **verifyOrder** — `POST /v0/labs/order/{order_id}/status/verify` to move the
   order to status_id 3 (In Progress). Results and files can only be added while
   In Progress.
3. **submitTestResults** — `POST /v0/labs/sample/{sample_id}/test_results`.
   Organize results by category (cannabinoids, terpenes, pesticides, ...) with
   compound `name` values drawn from `getCompounds`. Send all numeric values as
   strings to preserve precision. Resubmission overwrites prior data.
4. **uploadSampleCOA** — `POST /v0/labs/sample/{sample_id}/coa` (PDF only,
   multipart). Do not include the file field in the signature.
5. **completeOrder** — `POST /v0/labs/order/{order_id}/status/complete` to move
   the order to status_id 4 (Completed).

## Error handling
Errors return `{success:false, error_code, error_message, error_details}` (not
RFC 9457). Handle 400 (invalid request), 401 (auth/signature/timestamp), 403
(insufficient permissions), 404 (not found). See
`errors/confident-cannabis-problem-types.yml`.
