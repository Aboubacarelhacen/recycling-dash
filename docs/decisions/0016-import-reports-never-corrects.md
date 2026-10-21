# 0016 — The import reports what is wrong; it never corrects it

Date: 31 August 2026
Status: accepted

## Context

`ACHAT UBC (2).xlsx` is the company's real book for July and August 2026: two
month blocks in one worksheet, 52 rows, plus a client tranche block off to the
right. It is the record the business actually ran on, and it has faults.

Checking the sheet's own arithmetic before writing any importer turned up four:

- **A weight stored as text.** Row 27 holds `'90.7'` as a string. Excel's `SUM`
  skips text, so the sheet's own August weight total says 7 081,8 kg when the
  rows come to 7 172,5. The sheet has been under-reporting its own August by
  90,7 kg.
- **Two rows where PRIX does not equal POIDS × P.U.** Row 27 is 4,5 short; row
  30 is 247,5 over (10 275 written where 286,5 × 35 is 10 027,5).
- **Two dates typed as `28082026`** into a General cell, stored as an integer.
- **34 rows of 52 name no supplier at all**, and two of the eleven names that
  are present carry stray leading spaces.

Every one of these is a decision about somebody's money or somebody's stock.

## Decision

The importer parses, reports, and writes the rows exactly as they stand. It
corrects nothing.

`claude.md` already says questionable historical data is never silently fixed,
and this is what that costs: **the August month total in the application reads
279 238 MRU where the workbook says 279 480,5.** The 243 MRU difference is
entirely the two rows whose PRIX does not multiply out. The application derives
the total from the weight and the price, which are the two facts the manager
wrote down; the workbook's MONTANT used a third number that agrees with neither.

Reporting that and leaving it is right, and the alternatives are both worse.
Deriving a unit price from PRIX ÷ POIDS would silently invent a rate nobody
agreed. Overriding the computed total with MONTANT would make one row's
arithmetic unlike every other row's, and the discrepancy would never surface
again. The manager now has the row numbers and can amend either row in ten
seconds, because corrections already exist (decision 0013).

The same applies to the weight: the application says August is 7 172,5 kg, which
is what his own rows add up to. The report says the sheet disagrees and why.

## Suppliers

One collector per distinct name, with the name stored as an alias so a later
import of the same sheet maps to the same person.

`MOUSSA` and `MOUSSA 2`, and `TEREIRA` and `TERREIRA`, are almost certainly one
person each. They are **reported as possible duplicates and left separate**.
Merging two suppliers changes who the company owes and who it bought from;
that is the company's call. The report names the pairs.

## What is not imported

**The tranche block.** Eight tranches against `AHMEDOU`, 8 988 MRU outstanding.
It carries no dates, and an invoice needs an issue date. Inventing one would put
a fabricated date on a document that can be printed and handed to a client. It
stays in the report until the company supplies dates and answers whether
AHMEDOU is a client or a financier (`docs/wiki/22` #32).

**The monthly goal.** `GOAL 1 CONTENAIR PER MONTH` is real, but no container
weight is known (#31), so a real load leaves `monthly_target_kg` null and the
dashboard says "one container, not yet weighed in kilograms" rather than showing
a number nobody agreed to. The demo seed keeps a target so the progress bar has
something to draw.

## Re-running it

Each row is written through `recordEntry` with `import:<file>:<row>` as the
idempotency key, so the import is safe to run twice: the second run reports
every row as already present and writes nothing. That also makes the dry run
honest, because it asks the same question the write path would.

Nothing has a second write path: an imported purchase moves stock and writes an
audit entry through exactly the same function as a row typed in today.
