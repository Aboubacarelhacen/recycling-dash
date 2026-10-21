# Source Analysis — Uploaded Excel Workbook

## Source

Uploaded workbook: `ACHAT UBC (2).xlsx`

The workbook contains one visible sheet, `Feuil1`, spanning approximately `A1:W58`.

## Structure, read in full

One sheet, `Feuil1`, holding **two month blocks stacked vertically**, each with
its own header row and its own TOTAL row:

| Block | Rows | Weight | Material | Transport | Fuel | Other | Total |
|---|---|---|---|---|---|---|---|
| JUILLET | 2-14 | 3 275 kg | 113 856 | 1 500 | 2 500 | 1 985 | 119 841 |
| AOUT | 19-57 | 7 081.8 kg | 251 130.5 | 0 | 2 000 | 26 350 | 279 480.5 |

`TOTAL KG` in Q34 reads 10 356.8, which is the two months added together. The
manager closes a month, totals it, and starts the next block underneath.

## The goal

`A16` reads **GOAL 1 CONTENAIR PER MONTH**. The target is expressed in
containers, not kilograms, and neither month reached one.

## The client tranche block

Columns O to W, rows 17 to 23, are a separate small table headed **AHMEDOU**
with eight columns labelled 1ER through 8ER:

| | 1ER | 2ER | 3ER | 4ER | 5ER | 6ER | 7ER | 8ER |
|---|---|---|---|---|---|---|---|---|
| amount | 25 000 | 21 000 | 25 000 | 20 000 | 25 000 | 10 000 | 15 000 | 30 000 |
| PAYE | 24 787 | 20 215 | 24 425 | 19 640 | 19 705 | 10 000 | 15 000 | 28 240 |
| RESTE | 213 | 785 | 575 | 360 | 5 295 | 0 | 0 | 1 760 |

This is the receivable ledger: eight tranches against one counterparty, each
with an agreed amount, an amount paid and a remainder. It is what the company
has instead of invoices, and it is the shape the invoicing feature has to fit.

## What the sheet gets wrong

Checked against its own arithmetic before importing. Four faults, all imported
as they stand and reported rather than corrected (decision 0016):

| Fault | Where | Effect |
|---|---|---|
| A weight stored as text (`'90.7'`) | row 27 | Excel's `SUM` skips it, so the sheet's own August weight total is 90,7 kg short: it says 7 081,8 where the rows come to 7 172,5 |
| PRIX does not equal POIDS × P.U | rows 27, 30 | row 27 is 4,5 short, row 30 is 247,5 over. Together they are the 243 MRU by which the application's August total differs from the workbook's |
| A date typed as `28082026` | rows 53, 54 | stored as an integer, not a date; read as 28/08/2026 |
| No supplier at all | 34 of 52 rows | imported as "not recorded"; the column is optional by design (decision 0009) |

Two further things are noted rather than wrong: `MOUSSA`/`MOUSSA 2` and
`TEREIRA`/`TERREIRA` are probably one person each, and three purchases were made
at 33, 40 and 30 rather than the usual 35.

July is clean. Every column reconciles exactly.

## Primary operating columns

Primary operating columns include:

| Column | Observed meaning |
|---|---|
| DATE | transaction date |
| FOURNISSEUR | supplier/collector name |
| NBRE DE PIECE | count of pieces/items where used |
| POIDS | weight |
| P.U | unit purchase price |
| PRIX | purchase amount |
| TRANSPORT | transport cost |
| ESSENCE WAW | fuel cost |
| AUTRES | other cost |
| MONTANT | final row amount |

The workbook uses French labels, suggesting the operational interface/documentation may need French support or a French-language mode depending on client preference.

## Observed business examples

Rows show suppliers such as MOUSSA, HOURIYA, M BACKA YENZA, KEYTA, MOUSSA 2, TIJANI, ABDELLAHI, MODJIBOU, OUSMAN, TEREIRA/TERREIRA.

Weights are recorded in values such as 13, 25, 103, 270, 437, 452, 521, 563, 784 and similar entries. Unit prices observed include 30, 33, 35, 40. These are source examples, not universal business rules.

## Totals/targets

The sheet contains:

- a July section
- an August section
- a monthly container goal label such as `GOAL 1 CONTENAIR PER MONTH`
- a payment/remaining table with headings such as `PAYE`, `RESTE`, and `TOTAL`
- a total kilograms line in the August section

## What the rows reveal about the workflow

- **Roughly half the rows have no supplier.** The FOURNISSEUR column is blank on
  more rows than it is filled. The manager records the weight and the price; who
  it came from is often not captured at all.
- **Many rows carry no weight.** Rows 25, 28, 29, 38, 44, 45, 47 and 48 have only
  AUTRES and MONTANT: they are pure expenses, entered in the same list as the
  purchases, on the same day line.
- **The unit price is 35 almost everywhere**, with occasional 30, 33 and 40.
- **MONTANT = PRIX + TRANSPORT + ESSENCE + AUTRES** on every row that has them.
- Rows are entered in date order, several per day, and a day can appear four or
  five times.

The product implication is direct: the manager needs **one daily list** where a
row may be a purchase, an expense, or both, with the supplier optional. Splitting
purchases and expenses into two screens would be a step backwards from the sheet
he already has.

## Important source quality observations

The workbook contains rows with blank suppliers, blank purchase data, and rows used purely for expenses. It also contains dates represented differently in some rows: rows 53 and 54
carry the number `28082026` rather than a date, and row 27 stores the weight as
the text `'90.7'` rather than a number.

Some total formulas include combinations of purchase/transport/fuel/other values, but the historical sheet is not a safe contract for how every future record should be calculated. The software should define canonical rules and preserve imported legacy values as historical facts where necessary.

## Recommended import strategy

Do not convert this workbook into a database by making each column a table field with no domain structure.

Instead map:

`row → purchase event OR expense event OR target/payment record`

and preserve:

- source workbook
- source sheet
- source row number
- original values
- normalized values
- validation warnings

## Data-cleaning warnings

- Supplier may be missing.
- Date formats are inconsistent.
- Some rows contain expense-only entries with no weight.
- Unit-price semantics need confirmation (likely per kg, but do not assume permanently).
- `NBRE DE PIECE` may be optional and should not be used to infer weight.
- Monthly target/payment semantics require confirmation before implementing accounting rules.
