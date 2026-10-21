# 0013 - A correction is an event, not an edit

## Status

Accepted, 30 August 2026.

## Context

The manager records twenty rows in a sitting and will mistype a weight. Until
now the row stood, which made the product worse than the workbook it replaces:
in Excel he could simply retype the cell.

`docs/wiki/18` says a posted row cannot change without an audit or amendment
path, and `docs/wiki/11` says stock is the accumulated result of movements,
with corrections made through controlled adjustments.

## Decision

**A correction changes the row and adds a movement. It never rewrites history.**

When a weight is amended, the original inventory movement stays exactly as
written and a compensating `ADJUSTMENT` movement is inserted for the difference,
carrying the reason. Voiding writes the full negative. The position is still the
sum of movements, and the history still shows what happened rather than what
somebody wishes had happened.

**Nothing saves without a reason.** Four characters minimum, refused otherwise,
on both amend and void. A weight that changed without a sentence explaining it
is worse than the wrong weight, because nobody can tell which one to trust.

**The before and the after are both stored**, in the `before` and `after` columns
the audit table has carried since the first migration. The detail line is
written for a person: `Weight 300 to 310`.

**A voided row stays visible**, greyed and out of the month total, so the
correction can be read months later. It is not deleted and its movements are not
deleted.

**Amending and voiding are separate rights.** `purchases.edit_weight` and
`purchases.void` already existed in the matrix precisely because
`docs/wiki/15` says an accountant may issue invoices but may not alter a
purchase weight. Those rules now do real work.

## Consequences

- A voided purchase carries movements that net to zero rather than no movements
  at all. The integrity test was updated to state that rule; the old one, which
  asserted no movements for any non-posted purchase, was true only by accident.
- Stock can be reconstructed for any past date by replaying movements up to it,
  because nothing is ever removed.
- Turning a purchase into a costs-only row is refused. Void it and record the
  cost: two events that read clearly, rather than one row that changed species.
