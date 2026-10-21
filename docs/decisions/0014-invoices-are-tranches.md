# 0014 — An invoice is a tranche, and what is owed is never stored

Date: 2026-08-31
Status: accepted

## Context

The supplied workbook keeps a block beside the daily ledger, headed `AHMEDOU`.
It is eight rows, numbered `1ER` to `8ER`, each with an amount, a `PAYE`
column and a `RESTE` column, and a figure of 1,760 MRU at the foot. It is the
only client-facing accounting the company does today.

Two shapes were available for it.

The first was a new table: a counterparty with tranches, separate from
`invoices`, because "tranche" is what the business calls it and the block does
not look like an invoice. The second was to recognise the block for what it
is — an invoice ledger written in a spreadsheet corner — and use the `invoices`,
`invoice_lines` and `payments` tables that the data dictionary already
specifies.

## Decision

The second. A tranche **is** an invoice. `AHMEDOU`'s eight rows are eight
invoices against one client, and `RESTE` is the invoice total less the payments
received against it.

Three rules follow.

**The invoice total is frozen at issue.** It is written once, to
`invoices.total`, and never recomputed. This is deliberately unlike every other
figure in the system, which is derived on read (decision 0004). An invoice is a
document that leaves the building; a correction made next week to a purchase
weight must not silently change a number a client already holds on paper.

**What is owed is never stored.** `outstanding` is `total - sum(payments)`,
computed on every read. There is no balance column anywhere, and no code path
that increments one. A client account is only ever the sum of its tranches, and
a test asserts exactly that.

**The status follows the money rather than being set by hand.** A payment that
leaves a balance moves the invoice to `PARTIALLY_PAID`; a payment that closes it
moves it to `PAID`. Nobody chooses the status, so it cannot disagree with the
payments table.

## Consequences

The `AHMEDOU` block imports without a schema change: one client, eight
invoices, their payments, and 1,760 falls out of the arithmetic.

Overpayment is refused rather than absorbed. A payment larger than the balance
is rejected with the amount still owed named in the message. If money genuinely
arrives in excess, it belongs to another tranche, or to a credit the company
does not yet model — either way it is a business question and not something to
swallow silently.

A lump-sum invoice still writes an `invoice_lines` row, at one unit and the
agreed price, so the printable document always has a line to show and the
existing invariant "every invoice total is the sum of its own lines" continues
to hold for every invoice in the table.

Whether `AHMEDOU` is a client or a financier is still open
(`docs/wiki/22-open-questions.md`). If the answer turns out to be financier, the
tranches move to a different counterparty type; the arithmetic and the rules
above do not change.
