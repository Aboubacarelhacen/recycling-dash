# Build Order & Review Gates

## Where the build actually stands

The order below was written before the build began. The real sequence put the
interface first, at the client's direction, then the database beneath it. That
turned out to work: the screens defined exactly which read models the database
had to serve, and the calculation layer was already written and tested when the
schema arrived.

| Gate | Status |
|---|---|
| 1. Domain foundation | Schema, migrations, seed, organisation boundary, roles: **done**. Authentication: **done** (task 3) |
| 2. Procurement core | Read side **done**; collectors, materials, contracts, purchases and calculations all query Postgres. Write side **not started** |
| 3. Management visibility | Dashboard, contract workspace and reports-as-screens **done**, reconciled to the transactions in `tests/read-layer.test.ts` |
| 4. Delivery and documents | **Not started** |
| 5. Field operations | **Not started** |
| 6. Intelligence | **Not started** |

Reports, in order: `docs/03-bento-dashboard.md`, `docs/04-database.md`,
`docs/05-read-layer.md`, `docs/06-authentication.md`, `docs/07-permissions.md`.


## Gate 1 — Domain foundation

Deliver:

- entities
- migrations
- authentication
- organization boundary
- roles
- seed data

Review:

- relationships make sense
- tenant isolation tested
- no accidental destructive deletes

## Gate 2 — Procurement core

Deliver:

- collectors
- materials
- contracts
- purchases
- calculations
- inventory movements

Review:

- purchase posting is transactional
- totals are canonical
- contract fulfillment is correct
- duplicate retry is safe

## Gate 3 — Management visibility

Deliver:

- dashboard
- contract workspace
- reports

Review:

- dashboard numbers reconcile to transactions
- every KPI can be drilled into

## Gate 4 — Delivery + documents

Deliver:

- deliveries
- invoices
- receipt/document templates
- payments

Review:

- invoice totals reconcile
- inventory decreases correctly
- issued documents are auditable

## Gate 5 — Field operations

Deliver:

- mobile UX
- offline queue
- sync
- photo evidence

Review:

- duplicate prevention
- sync recovery
- field speed

## Gate 6 — Intelligence

Only after gates 1–5 are stable.

Deliver:

- alerts
- AI Q&A
- anomaly detection
- document extraction

Review:

- AI uses canonical data
- permissions are enforced
- AI cannot bypass business rules

## Golden review gate

Before calling the product production-ready, perform a complete scenario:

```text
Create client
→ Create contract
→ Make 3 purchases
→ Add fuel expense
→ Verify stock
→ Verify contract fulfillment
→ Create delivery
→ Verify stock decrease
→ Generate invoice
→ Record payment
→ Verify margin
→ Inspect audit trail
```

The scenario must reconcile end to end.
