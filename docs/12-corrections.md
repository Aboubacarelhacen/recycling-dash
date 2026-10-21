# 12 — Corrections

**Date:** 30 August 2026
**Slice:** amend and void a recorded row, with the reason kept
**Decision:** [0013](decisions/0013-corrections-are-events.md)
**Status:** 240 tests and 7 correction browser checks passing

---

## 1. The gap this closes

Report 11 ended by naming this the most important gap in the product: the
manager would mistype a weight and the row would stand. In Excel he retyped the
cell. Until this existed, the product was worse than the thing it replaces.

## 2. How it works for him

Every row on the daily ledger carries a **Correct** link. Clicking it opens that
row in place, as a form, with its own values filled in. No modal, no separate
screen, no scrolling to find what he was looking at.

He changes what was wrong, writes why in one line, and saves. Or he presses
**Void this row**, which asks once before doing it.

The reason is a field, not an afterthought. Nothing saves without one.

## 3. What happens underneath

**Stock moves by the difference.** The movement already written stays written; a
compensating adjustment is added for the change, carrying the reason. Correcting
420 kg to 430 kg leaves two movements: `+420` and `+10`. Voiding leaves `+420`
and `-420`, netting to zero.

That is deliberate. Stock is the sum of movements, so a position can be
reconstructed for any past date, and the history shows what happened rather than
what somebody wishes had happened.

**The audit entry reads like the example in `docs/wiki/15`:**

```
Purchase corrected · Weight 300 to 310 · Corrected scale reading · Ahmed Ould Cheikh
Purchase voided    · 310 kg taken back out of stock · Recorded twice by mistake
```

Both the before and after values are stored, not just the sentence.

**A voided row stays on the page**, greyed and out of the month total, so the
correction can be read months later.

**Amending and voiding are separate rights.** They already existed in the
permission matrix because `docs/wiki/15` says an accountant may issue invoices
but may not alter a purchase weight. Those rules now do real work.

## 4. What is refused, and why

| Attempt | Answer |
|---|---|
| A correction with no reason, or a reason of one word | Refused: "Say what changed and why. It is recorded against your name." |
| Amending a row that is already void | Refused: record a new one instead |
| Turning a purchase into a costs-only row | Refused: void it and record the cost, so two clear events replace one row that changed species |
| A row belonging to another organisation | Refused as though it does not exist |
| Voiding twice | Accepted, and the stock is not reversed a second time |

## 5. Tests

240 tests, up from 222. The 17 new ones cover the arithmetic and the paperwork:

- nothing saves without a reason, on either path
- stock moves by the difference, up and down, and not at all when only the price
  changed
- the original movement is never edited: after a correction there are two
  movements, `+420` and `+10`
- a void reverses exactly what was added, and repeating it does not reverse twice
- the month total follows a correction and drops by a voided row
- the audit entry holds the before, the after, the reason and the actor
- refusals: an already-void row, a purchase becoming a cost, another
  organisation's row
- and a standing check that no voided purchase anywhere still holds stock

## 6. One stale invariant, corrected

An existing integrity test asserted that a purchase which is not posted has no
inventory movements. That was true only because nothing could be voided yet. A
voided purchase now carries its original movement and its reversal, so the test
was rewritten to state the real rule: **a voided purchase's movements net to
zero**, and a draft still has none.

Leaving the old assertion would have made the suite fail the first time someone
voided a row in the demo data, which is exactly the kind of test that teaches
people to ignore tests.

## 7. In a browser

`npm run test:e2e:corrections` records a row, corrects it and voids it:

| Check | Result |
|---|---|
| Every row offers a correction | pass |
| The row opens with its own values | pass |
| A correction without a reason is refused | pass |
| The month total follows the correction | pass |
| Voiding asks once before it happens | pass |
| A voided row leaves the month total | pass |
| The voided row stays on the page | pass |

Stock read 44 232 kg before the run and 44 232 kg after: recorded `+300`,
corrected `+10`, voided `-310`.

## 8. Three defects in the run itself

The first pass reported three failures. None were in the product:

- the checks read `input[name=quantityKg]` with `querySelector`, which found the
  **entry row at the top of the page** rather than the correction row, so the
  correction never changed anything
- a baseline was measured after recording the test row and then compared as
  though it had been measured before
- the void assertion used the pre-correction weight

The app had behaved correctly throughout, which the stock figures showed. The
assertions were fixed, not the code.

## 9. Not in this slice

Amending a costs-only row: it can be voided and re-recorded, which is two clicks
and reads more clearly in the audit trail than a mutated amount. No undo of a
void. No correction history shown on the screen yet: it is in `audit_logs` and
wants a place to be read.

## 10. Next

The tranche ledger and the invoice: the `AHMEDOU` block from the workbook, with
amount, paid and remaining per shipment, and a document on the company
letterhead.
