# 09 — The daily ledger

**Date:** 30 August 2026
**Slice:** the first write path: one screen, one list, one row at a time
**Status:** 173 tests and 6 end-to-end browser checks passing

---

## 1. The screen the manager opens every morning

The company's workbook is one list in date order where a row may be a purchase
or a running cost, the supplier is often blank, and the month closes with a
TOTAL row. This screen is that list.

The month total is stated in the workbook's own columns, in its own order:
weight, material (PRIX), transport, fuel (ESSENCE), other (AUTRES), and the
month total (MONTANT). A manager can hold his sheet beside the screen and read
across.

## 2. Three rules taken from the sheet, not from a data model

**A weight makes it a purchase; costs alone make it an expense.** Eight rows in
the company's August have only AUTRES and MONTANT. They are entered on the same
day line as everything else, so the screen accepts them from the same form and
files them as an expense behind the scenes.

**The supplier is optional.** Roughly half the historical rows have none.
`purchases.collector_id` was made nullable in migration `0002` for exactly this
reason: requiring it would have stopped the manager entering his own data. Rows
without one read "not recorded" rather than being hidden or guessed at.

**The total is the sheet's arithmetic**, computed as you type:
`PRIX + TRANSPORT + ESSENCE + AUTRES`, where `PRIX = weight x price`. It is the
number he checks before committing the row, so it appears before he commits it.

## 3. What happens when a row is saved

One transaction, following the boundary in `docs/wiki/18`:

```
BEGIN
  validate the row
  take the organisation's reference lock
  allocate the next reference        P-0236
  insert the purchase
  insert its line, price snapshotted
  insert the inventory movement      stock moves here and nowhere else
  insert the audit entry             who, what, when
COMMIT
```

If any step fails, none of it happened. A rejected row moves no stock, which is
asserted rather than assumed.

**A retry cannot record the row twice.** The form carries an idempotency key
generated per attempt; the unique index on
`(organization_id, client_transaction_id)` is the real guard, and the action
checks first so a retry returns the original reference instead of an error.

**References are allocated under an advisory lock**, so two rows entered in the
same second cannot take the same number.

## 4. Tests

173 unit and integration tests, up from 152. The 21 new ones cover:

- what a row is: weight makes a purchase, costs make an expense, and the total
  matches row 4 of the real sheet (13 kg at 35, fuel 500, other 100, MONTANT
  1 055)
- refusals: a row that records nothing, weight without a price, negatives,
  a date that is not a date
- the transaction: purchase, line, movement and audit written together; stock
  unchanged when the row is rejected; references in sequence
- costs: an expense moves no stock, a single-column cost is filed under that
  column, a mixed cost under "other"
- idempotency: the same key returns the first row, different keys record two
- the month: purchases and costs in one list, newest first; column totals sum to
  the month total; a voided row stays visible but out of the total

## 5. End-to-end

`npm run test:e2e:ledger` drives a browser through a real entry:

| Check | Result |
|---|---|
| The total computes as you type: 250 x 36 + 500 = 9 500 | pass |
| The row saves and is confirmed by reference | pass |
| The month total grows by exactly the row's total | pass |
| The row appears in the list | pass |
| Stock moved with the purchase | pass |

Stock read 44 906 kg before the run and 45 156 kg after: the 250 kg the browser
typed.

## 6. Two defects worth recording

**An effect was resetting the form after a save**, which React's own guidance
calls a cascading render. The fields now remount on the saved reference instead,
which clears the row without an effect and refocuses the weight field on mount.

**The first end-to-end run reported two failures that were not defects**: one
regex matched "MONTH TOTAL" instead of the form's total, and another was
over-escaped. The underlying write had worked, which the stock movement proved.
The assertions were fixed rather than the code, and the run is honest now.

## 7. Not in this slice

No editing or voiding of a recorded row yet: a correction goes through the audit
path, and that is the next thing this screen needs. No Excel export yet, which
is what turns this into a replacement for the workbook rather than a copy of it.

## 8. Next

The month close and the Excel export, so the manager can hand someone the same
workbook he hands them today. Then the tranche ledger and the invoice on the
company letterhead.
