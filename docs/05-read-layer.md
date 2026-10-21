# 05 — Backend task 2: the read layer

**Date:** 30 August 2026
**Slice:** every screen reads Postgres; the bundled fixture is gone from the app
**Status:** 105 tests passing, figures reconciled against the previous fixture

---

## 1. Where the line sits between SQL and TypeScript

The rule in `claude.md` is that a formula lives in exactly one place. The
question this task had to answer was whether that means aggregating in SQL or
in the domain layer.

**SQL reduces. `lib/calc.ts` decides.**

Summing a column is not a business rule, so it happens wherever it is cheapest:
in Postgres for the database, in a loop for a fixture. Fulfilment, cost per
kilogram, projected margin, pace and health *are* business rules, so they happen
once, in the same function both paths call.

`contractMetrics` was split to make that literal:

```
ContractTotals                 the reduced facts: quantity, material cost,
                               trip cost, allocated expense, delivered, invoiced,
                               paid, posted count
      ↓
contractMetricsFromTotals()    every business rule, one implementation
```

The array-based `contractMetrics()` still exists as a thin adapter. It reduces
in TypeScript and calls the same core. That is what makes the reconciliation
test below possible.

## 2. What the research changed, and what it did not

**Next 16 cache components, declined for now.** `use cache` with `cacheLife` and
`cacheTag` is the current recommended way to cache database reads in Next 16. It
is the wrong default here: a manager who posts a purchase has to see it, and
there are no writes yet to invalidate a tag with. Every screen in the group is
`force-dynamic`, with a comment saying exactly when to revisit that. Three
screens had silently prerendered at build time before this was added, which
would have shown yesterday's figures until the next deploy.

**Test approach, taken.** The current advice for Drizzle is a real Postgres
instance with transaction rollback between cases, rather than mocking the
database. That is what `tests/integrity.test.ts` does: every attempted bad write
runs inside a transaction that is rolled back, and a final assertion proves the
dataset is untouched.

`docs/qa/test-strategy.md` and the research agreed on what to test. The research
only added *how*.

## 3. Tests

105 tests, three files.

| File | Covers |
|---|---|
| `tests/calc.test.ts` | 28 unit tests: purchase totals, fulfilment, over-fulfilment, cost per kg guards, margin, health at each threshold, velocity, rounding |
| `tests/read-layer.test.ts` | 42 reconciliation tests: every contract, dashboard, series, today, stock, collectors, purchases, invoices, plus tenant isolation |
| `tests/integrity.test.ts` | 35 integrity tests: the database refusing bad writes, posted purchases moving stock, invoices equalling their lines, audit completeness |

The reconciliation file is the important one. The fixture and the SQL are two
independent paths to the same numbers, so if they ever disagree, one is wrong.

## 4. What the tests caught

Three real defects, none of which would have been visible on screen.

**The daily series counted different money in each path.** SQL summed material
cost only; the fixture summed material plus the trip costs recorded on those
purchases. The chart said "spent". Trip cost is spend, so SQL was wrong, and
fixing it needed a second aggregate: joining lines first multiplies a trip cost
by the number of lines on the row.

**The demo data paid an invoice more than it billed.** Invoice totals became
derived from real delivered weight, while the payments stayed fixed amounts from
an earlier fixture: 1 120 560 MRU paid against a 1 042 912 MRU invoice. Payments
are now derived from the invoice they settle, in both the fixture and the seed.

**Three screens were prerendered at build time.** Caught by reading the build
output rather than by a test, and fixed with `force-dynamic`.

## 5. Reconciliation

Every figure on every screen is identical to the fixture, to the gram and to the
centime, with one deliberate change: the Collection card's "purchases posted"
now counts the 30-day window rather than all history, which is what the rest of
that card measures.

Proof the screens are live: a `UPDATE collectors SET name = ...` in psql shows
on the next request, with no rebuild.

## 6. Not in this task

No writes, no auth, no permissions. The organisation and the acting user are
still the first rows in their tables, which task 3 replaces with a session.
`OPERATING_TODAY` remains a constant so the demo stays reproducible.

## 7. Commands

```bash
npm run db:test:setup   # rebuild the test database from migrations and seed
npm test                # 105 tests
```

## 8. Next

Task 3, authentication and organisations: sessions, sign-in, and scoping every
query to the signed-in user's organisation rather than to the first row.
