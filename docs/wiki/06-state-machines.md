# State Machines

## Contract

Recommended statuses:

- `DRAFT`
- `PENDING_APPROVAL`
- `ACTIVE`
- `AT_RISK`
- `FULFILLED`
- `CLOSED`
- `CANCELLED`

Valid transitions should be explicit. A `CLOSED` contract should not be silently reopened; use a controlled workflow.

## Purchase

- `DRAFT`
- `POSTED`
- `VOIDED`

Once `POSTED`, the purchase participates in inventory and financial calculations. Corrections should create adjustments or a controlled amendment path.

## Expense

- `DRAFT`
- `SUBMITTED`
- `APPROVED`
- `REJECTED`
- `VOIDED`

An MVP may omit formal approval if the client explicitly wants a simpler flow, but schema boundaries should not prevent adding it later.

## Delivery

- `DRAFT`
- `READY`
- `DISPATCHED`
- `DELIVERED`
- `CANCELLED`

## Invoice

- `DRAFT`
- `ISSUED`
- `SENT`
- `PARTIALLY_PAID`
- `PAID`
- `VOIDED`

## Contract health

Health should be a **derived operational signal**, not a manual status a user must maintain.

Possible derived statuses:

- `HEALTHY`
- `ATTENTION`
- `AT_RISK`

The underlying reasons should be inspectable, e.g.:

- low fulfilment rate
- high effective cost/kg
- deadline approaching
- procurement velocity declining
