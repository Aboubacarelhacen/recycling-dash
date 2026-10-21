# Data Integrity & Reliability

## Core invariants

### Contract
- target quantity > 0 for normal contracts
- currency is explicit
- deadline must make sense relative to start date

### Purchase
- quantity >= 0
- unit price >= 0 unless special adjustment workflow exists
- currency explicit
- posted purchase cannot change without audit/amendment

### Inventory
- movement quantity is explicit
- every movement has a type and source reference
- negative stock policy is explicit

### Invoice
- totals are server calculated
- issued invoice number is unique
- issued documents are immutable except through correction workflow

## Transaction boundaries

Example purchase posting transaction:

```text
BEGIN
  validate purchase
  create purchase
  create inventory movement
  update/derive contract fulfillment
  write audit event
COMMIT
```

If any required step fails, the transaction should roll back.

## Idempotency

Mobile and integrations may retry. A request with the same client idempotency key should not create duplicate purchase/invoice/delivery records.

## Concurrency

If two workers update the same inventory or contract concurrently, the application must use transaction/locking strategies appropriate to the database.

## Corrections, as built

A correction is an event, not an edit (`docs/decisions/0013`):

- the inventory movement already written is never changed or deleted
- amending a weight adds a compensating `ADJUSTMENT` movement for the difference
- voiding adds the full negative, so the row's movements net to zero
- both carry the reason, and the audit entry stores the before and the after
- a reason is required; nothing saves without one
- a voided row stays visible and out of the month total

## Historical truth

Never recompute history in a way that silently changes the meaning of old documents.

If a pricing rule changes today, yesterday's posted purchase keeps its original unit price.

## Import integrity

Legacy imports should produce:

- accepted rows
- rejected rows
- warnings
- duplicate candidates
- normalized values
- original source reference
