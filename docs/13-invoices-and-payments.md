# 13 — Invoices and payments

Date: 31 August 2026
Slice: bill a shipment, receive money against it, and see what each client owes.

## 1. What the workbook already does

Beside the daily ledger, in columns O to W, the workbook keeps a block headed
`AHMEDOU`. Eight columns, `1ER` through `8ER`, and three rows: an agreed
amount, `PAYE`, and `RESTE`.

| | 1ER | 2ER | 3ER | 4ER | 5ER | 6ER | 7ER | 8ER |
|---|---|---|---|---|---|---|---|---|
| amount | 25 000 | 21 000 | 25 000 | 20 000 | 25 000 | 10 000 | 15 000 | 30 000 |
| PAYE | 24 787 | 20 215 | 24 425 | 19 640 | 19 705 | 10 000 | 15 000 | 28 240 |
| RESTE | 213 | 785 | 575 | 360 | 5 295 | 0 | 0 | 1 760 |

That is the whole of the company's client-facing accounting. It is also,
read plainly, an invoice ledger: eight amounts billed, the money received
against each, and the remainder.

## 2. The decision the slice turned on

Whether to build a new "tranche" table, because that is what the business calls
these rows, or to recognise the block as invoices and use the tables the data
dictionary already specifies.

The second. A tranche is an invoice. Written up as
[decision 0014](decisions/0014-invoices-are-tranches.md), which also fixes the
two rules the rest of the slice rests on:

**The invoice total is frozen at issue.** Every other figure in this system is
derived on read — that is decision 0004, and it is why a correction recomputes
the month instantly. An invoice is the exception, and deliberately so. It is a
document that leaves the building. A weight corrected next week must not change
a number a client already holds on paper.

**What is owed is never stored.** `outstanding` is the total less the payments
received, computed on every read. No balance column exists, and no code path
increments one. A test asserts the client account equals the sum of its own
tranches rather than trusting they agree.

## 3. What was built

`src/server/invoices/validate.ts` — a tranche is billed either by weight and
price or as one agreed amount; one or the other must be present. Payment falling
due before the invoice exists is refused. A payment larger than the balance is
refused with the amount still owed named in the message, with half a centime of
slack so a payment that settles exactly is never rejected by a rounding
artefact.

`src/server/invoices/record.ts` — `issueInvoice` allocates the number, writes
the invoice, its line and an audit entry in one transaction under an advisory
lock on `<org>:invoice`. A lump sum still writes a line, at one unit and the
agreed price, so the invariant "an invoice total is the sum of its own lines"
holds for every row in the table and the printable document always has
something to print. `recordPayment` reads what has already been received,
validates against it, writes the payment, and moves the status: a balance left
is `PARTIALLY_PAID`, a balance closed is `PAID`. Nobody sets the status by hand,
so it cannot disagree with the payments table.

Numbering is `FA-<year>-<0000>`, continuing the series already in the data. The
unique index on `(organization_id, invoice_number)` is the backstop under the
lock.

`src/server/queries/invoices.ts` — `clientAccounts` groups the tranches by
client, in the order they were agreed, and reports invoiced, paid, outstanding
and the oldest unpaid shipment. A voided invoice stays visible but bills nothing
and owes nothing.

`/invoices` — one block per client, headed with the name and what they still
owe, over a table in the workbook's own columns: invoice, for, quantity, amount,
paid, remaining, state. Billing sits above it, with the amount shown before it
is committed. Paying opens in place of the row, pre-filled with the balance, so
the figure stays on screen while the amount is typed; the button reads **Settle**
rather than **Record** when the amount entered would close it.

## 4. What was verified

**266 tests**, 10 files, all passing. 24 are new in `tests/invoices.test.ts`:
what makes a valid invoice, numbering in one unbroken series, the line
multiplying out to the total, the account adding up to what is owed, overpayment
refused with nothing written, a balance closed in three parts, a settled invoice
refusing more.

Two new invariants in `tests/integrity.test.ts`: a part-paid invoice holds
something and still owes something, and an invoice number is issued once per
organisation. The four invoice invariants that already existed — total equals
its lines, line equals quantity times price, payment never exceeds the total,
`PAID` only when settled — now cover this write path unchanged.

**13 browser checks** in `scripts/e2e-invoices.mjs`, all passing: issue 40,000,
watch the account rise by exactly 40,000, be refused at 40,001, pay 15,000,
watch it fall by exactly 15,000 with the row reading 15 000 paid and 25 000
remaining, settle the rest, and find the account back where it started with the
row marked settled and no longer offering to be paid.

Two of those checks failed on the first run. Both were the test's arithmetic:
it asserted on the "Invoiced" figure at the top of the page, which renders
compact (`20 M MRU`) and cannot carry a 40,000 move. The outstanding deltas were
exact throughout. The assertions now read the figure that carries the precision.

`npm run build` succeeds and ESLint is clean. The rows the browser runs created
were deleted from the demo database afterwards.

## 5. Open questions this touched

Question 32 — whether AHMEDOU is a client or a financier — is annotated rather
than answered. It is built as a client account: the arithmetic is identical
either way, so only the counterparty type would move if the answer comes back
differently.

Questions 22, 23 and 30 — whether the invoice is a tax document, what
registration line Mauritania requires on it, whether a signature or stamp is
needed — are still unanswered, and they gate the printable document rather than
the ledger. That is why this slice stops at the account.

## 6. Next

The invoice as a document, on the company letterhead. It is now a rendering
problem: the data is here, frozen at issue, with a line that multiplies out.
What it must say legally is the part still waiting on the client.
