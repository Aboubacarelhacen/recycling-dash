# 0009 - Postgres and Drizzle, inside the application

## Status

Accepted, 30 August 2026.

## Context

The frontend is a Next.js App Router application whose pages are already server
components. The backend had to start somewhere, and the choice was a separate
service or the server half of the application that exists.

## Decision

**Postgres, accessed with Drizzle, inside the Next.js app.** Schema in
`src/server/db/schema.ts`, migrations in `drizzle/`, one connection pool in
`src/server/db/index.ts` marked `server-only`.

Reasons, in the order that decided it:

1. Principle 9 in `docs/architecture/decision-principles.md`: minimal
   dependencies and boring infrastructure for V1. A second service is a second
   deployment, a second auth story and a network hop, for no capability we need.
2. Server components already read the domain layer directly. Swapping the fixture
   for a query changes one module, not an architecture.
3. `lib/calc.ts` stays a single definition shared by the write path, the read
   path and the screens, which is the rule the whole product hangs on.

**Money is `numeric(14,2)` and weight is `numeric(12,3)`.** Drizzle returns
numerics as strings and that is left alone: nothing may pass through a float on
the way in or out.

**Every business table carries `organization_id`.** The first deployment is one
company. A tenant boundary retrofitted later is a migration nobody survives.

**Invariants live in the database, not only in the service layer.** Non-negative
weights and prices, positive contract targets, a deadline at or after the start,
non-zero inventory movements, unique contract and invoice numbers per
organisation, and a unique client transaction id per organisation for
idempotency. A future caller that forgets a check still cannot write bad data.

**Stock has no stored balance.** There is no `current_stock` column anywhere.
Position is the sum of `inventory_movements`, so a voided purchase corrects
itself and no number can drift.

## Two departures from the data dictionary

- **`invoice_lines` added.** An invoice raised from a delivered quantity needs a
  priced line, and a header-level quantity breaks the moment one shipment mixes
  two materials.
- **`collector_aliases`, `import_batches`, `import_rows` added.** The workbook
  spells suppliers several ways and legacy rows must keep their provenance
  (`docs/wiki/02`, `docs/wiki/09`).

Both are refinements the dictionary invites; neither changes an existing table.

## Consequences

- One deployment, one language, one place where a business rule can live.
- If the platform later needs a public API or a mobile backend, route handlers in
  the same app serve it; only if that grows past comfort does a service split pay.
- `drizzle-kit` owns migrations. Nothing edits the database by hand.
