# Technical Architecture

## Architectural goal

One dependable transactional core with clear boundaries for web, mobile, documents, reporting and future AI.

## Logical layers, as built

```text
Screens                     app/(app)/*        server components
        ↓
Read models                 server/queries/*   SQL, one query per view
Write path                  server actions     transactional, task 5 onward
        ↓
Canonical calculations      lib/calc.ts        every business formula, once
        ↓
Database                    Postgres via Drizzle
```

**There is no separate API service.** The Next.js application is the server:
pages are server components that query Postgres directly, and mutations are
server actions. Decision: `docs/decisions/0009-postgres-and-drizzle-in-the-app.md`.
A public API becomes route handlers in the same application when a mobile client
needs one; a service split only pays once that outgrows one deployment.

**The rule that keeps this honest:** SQL reduces, `lib/calc.ts` decides. Summing
a column is not a business rule and happens in Postgres. Fulfilment, cost per
kilogram, margin, pace and health are business rules and happen in one function
that both the database path and the tests call.

Supporting workers are **not built yet**:

```text
Jobs / Queue (not implemented)
   ├── PDF generation
   ├── notifications
   ├── imports
   ├── sync processing
   └── AI tasks
```

## Domain modules

Recommended module boundaries:

- Identity & Organizations
- Clients
- Suppliers/Collectors
- Materials
- Contracts
- Procurement
- Expenses
- Fleet
- Inventory
- Deliveries
- Invoicing
- Payments
- Reporting
- Audit
- Documents
- Notifications

## Database philosophy

Prefer a relational database for the transactional core.

Reasons:

- contracts and transactions have strong relationships
- financial records require consistency
- inventory changes require transactions
- permissions require relational modeling
- reporting needs reliable joins

## Multi-tenancy

**As built:** every business table carries `organization_id` from the first
migration, and every query takes its organisation from the session rather than
from a constant. Tenant isolation is asserted in `tests/read-layer.test.ts`,
which checks that an unknown organisation reads zero rather than someone
else's rows.

## Jobs

Asynchronous work should be used for:

- document/PDF generation
- large imports
- notifications
- scheduled summaries
- AI analysis
- media processing

Do not make critical financial writes depend on an unreliable background job.

## Storage

Database: structured operational data.

Object storage: receipts, photos, PDFs, source documents.

## API design

The API should expose business-oriented actions when they clarify invariants.

Examples:

- `POST /contracts`
- `POST /contracts/:id/purchases`
- `POST /purchases`
- `POST /expenses`
- `POST /deliveries`
- `POST /invoices`
- `POST /inventory/adjustments`

Avoid exposing raw table CRUD that allows clients to mutate business invariants directly.

## Observability

Capture:

- structured logs
- error tracking
- request IDs
- job status
- audit events
- key business metrics

## Security baseline

**As built** (`docs/decisions/0010-authentication.md`): Argon2id at the OWASP
baseline, opaque session tokens stored only as hashes, twelve-hour sessions that
can be revoked, identical answers to every failed sign-in, and a lockout after
five failures. Authorisation is a role-to-action matrix enforced on the server.

- secure authentication
- server-side authorization
- tenant isolation
- encrypted transport
- protected file access
- secrets outside source control
- least privilege
- backup/recovery strategy
