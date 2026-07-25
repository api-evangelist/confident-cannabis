---
name: Manage the Confident Cannabis order status lifecycle
description: Move a lab order through its reversible status transitions (verify, unverify, complete, revise, cancel, uncancel) and read the canonical status list.
api: openapi/confident-cannabis-lab-openapi.json
operations: [getOrderStatuses, verifyOrder, unverifyOrder, completeOrder, reviseOrder, cancelOrder, uncancelOrder]
---

# Manage the order status lifecycle

Operating instructions for an agent acting as an authenticated **lab**. Auth and
error semantics are identical to the submit-results skill (API key + optional
HMAC signing; custom error envelope).

## Status model
`getOrderStatuses` (`GET /v0/orderstatuses`) returns the canonical list:
1 = Placed, 3 = In Progress, 4 = Completed, 5 = Canceled.

## Transitions (all POST under /v0/labs/order/{order_id}/status/...)
- **verifyOrder** `/verify` — Placed (1) → In Progress (3).
- **unverifyOrder** `/unverify` — In Progress (3) → Placed (1).
- **completeOrder** `/complete` — In Progress (3) → Completed (4).
- **reviseOrder** `/revise` — Completed (4) → In Progress (3) to correct results.
- **cancelOrder** `/cancel` — → Canceled (5).
- **uncancelOrder** `/uncancel` — Canceled (5) → Placed (1).

## Rules
There is no idempotency key; each transition is an explicit state change, so
check the current status (via `getOrderDetails`) before transitioning to avoid a
400/403. Results and file uploads are only permitted while In Progress (3).
