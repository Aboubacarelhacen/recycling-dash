# 04 — Backend task 1: the database

**Date:** 30 August 2026
**Slice:** Postgres schema, constraints, migrations, seed
**Decision:** [0009](decisions/0009-postgres-and-drizzle-in-the-app.md)
**Status:** applied to a local database and reconciled against the screens

---

## 1. What exists now

24 tables covering the whole chain in `docs/wiki/04`: organisations and users,
clients, collectors and their aliases, materials, warehouses, vehicles, expense
categories, contracts and contract items, purchases and purchase items,
expenses, inventory movements, deliveries and delivery items, invoices and
invoice lines, payments, attachments, audit logs, and import provenance.

Statuses are Postgres enums matching `docs/wiki/06` exactly, so a status that is
not in the state machine cannot be written.

## 2. The rules the database enforces by itself

Service code can forget a check. The database cannot.

| Invariant | Enforced by |
|---|---|
| Purchase weight and unit price are never negative | `purchase_items_quantity_non_negative`, `purchase_items_price_non_negative` |
| Trip costs are never negative | `purchases_costs_non_negative` |
| A contract targets more than zero | `contracts_target_positive` |
| A deadline is not before the start | `contracts_deadline_after_start` |
| A contract has a price or a value | `contracts_price_or_value` |
| An inventory movement is never zero | `movements_delta_not_zero` |
| Contract and invoice numbers are unique per organisation | `contracts_org_number_unique`, `invoices_org_number_unique` |
| A retry from the field app cannot create a second record | `purchases_org_client_txn_unique` and the same on expenses, deliveries, payments |

All eight were tested by trying to violate them. Each was rejected; a valid
insert of the same shape succeeded.

## 3. Decisions worth knowing

**Money is `numeric(14,2)`, weight is `numeric(12,3)`,** and Drizzle hands both
back as strings. Nothing passes through a float.

**No stored stock balance.** Position is the sum of `inventory_movements`. A
voided purchase corrects the position by removing its movement, so no number can
drift out of agreement with the transactions beneath it.

**Unit price is a snapshot on the purchase line,** never a lookup, so yesterday's
posted purchase keeps yesterday's price when today's changes.

**`organization_id` on every business table** from the first migration.

**Invoice totals are nullable while a draft, frozen at issue.** An issued
document must not move when a later correction changes today's figures.

## 4. Seed

`npm run db:seed` loads the same deterministic dataset the screens already show,
so the figures do not change as the app moves from fixture to database. Inventory
movements are derived during the seed exactly as the application derives them,
from posted purchases and delivered deliveries, because stock is never authored.

It loaded 5 users, 4 clients, 9 collectors, 5 contracts, 118 purchases with 133
lines, 41 expenses, 132 movements, 3 deliveries, 3 invoices and 3 payments.

## 5. Reconciliation

Fulfilment computed in SQL against fulfilment shown on the dashboard:

| Contract | Database | Dashboard |
|---|---|---|
| ALU-014 | 25 187 kg, 84.0 % | 25 187 kg, 84 % |
| ALU-015 | 10 040 kg, 66.9 % | 10 040 kg, 67 % |
| ALU-016 | 7 064 kg, 35.3 % | 7 064 kg, 35 % |

Stock from movements alone: 36 183 kg of UBC at the depot, 5 922 kg at the port
yard, plus cast aluminium at both.

## 6. Commands

```bash
createdb africa_aluminium     # once
npm run db:generate           # build a migration from the schema
npm run db:migrate            # apply migrations
npm run db:seed               # load the demo dataset
npm run db:studio             # browse the data
```

`DATABASE_URL` lives in `.env.local`; `.env.example` shows the shape.

## 7. Not in this task

No reads from the database yet: the screens still use the bundled fixture. That
is task 2, and it is deliberately separate so this task could be checked on its
own. No auth, no writes, no server actions.

## 8. Next

Task 2, the read layer: `domain/selectors.ts` becomes SQL against these tables,
`lib/calc.ts` stays exactly as it is, and the screens stop importing the fixture.
