# 0017 — The browser tests refuse to run against a real book

Date: 31 August 2026
Status: accepted

## Context

The browser suites write. They record purchases, issue invoices, take payments
and void rows, because that is what makes them worth having: they exercise the
same paths a person does.

They also point at whatever server is running, which was fine while that server
held demo data. Once the real workbook was imported (report 15), the running
server held the company's actual book, and a routine test run put **six invoices
and six purchases into it**. It went unnoticed until the client looked at the
invoices screen and asked what `e2e tranche` was.

The rows were removed and the book verified back to 43 purchases, 9 costs,
10 448 kg. The hole stayed open.

## Decision

A server declares whether it is disposable, and the suites refuse anything else.

`/api/health` returns `scratch: true` only when the process was started with
`SCRATCH_DB=1`. The shared harness checks it before opening a browser and stops
with an instruction rather than a stack trace:

> `http://localhost:3000` is not a scratch server, and these tests write.
> Start one with: `npm run dev:scratch`

`SCRATCH_DB` is an environment variable on the server, not a property of the
data, so the endpoint leaks nothing about the business. It is public and says
only whether this process was started for testing.

Scripts that only look, such as screenshots, pass `readOnly: true` and skip the
check. The guard exists to stop writes; stopping reads would only tempt someone
to remove it.

## Consequences

Running the browser suites now takes a second server:

```bash
npm run db:scratch     # once: create and seed a throwaway database
npm run dev:scratch    # serves it on 4318 with SCRATCH_DB=1
npm run test:e2e       # and the rest
```

Next refuses two dev servers from one directory, so this means stopping the
ordinary one first. That friction is the point. The alternative is a suite that
silently edits the company's ledger, and that already happened once.

## What this does not cover

The unit and integration tests connect to `DATABASE_URL` directly and are
guarded differently: they run against `africa_aluminium_test`, rebuilt by
`npm run db:test:setup`, and each suite deletes the rows it created. A test that
forgets to clean up pollutes a throwaway database, not a real one.
