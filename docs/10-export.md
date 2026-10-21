# 10 — Preview and export the month

**Date:** 30 August 2026
**Slice:** the month as a spreadsheet, previewed on screen and downloaded on demand
**Status:** 192 tests and 8 end-to-end browser checks passing

---

## 1. What the company asked for

To see the workbook at any moment, and to download it at any moment, in the same
shape they already use, or better organised.

## 2. What downloads

`africa-aluminium-journal-2026-08.xlsx`, three sheets:

**Résumé.** The figures they used to work out with a calculator: weight
collected, material, transport, essence, autres, the month total, the average
price per kilogram, the true cost per kilogram including costs, the month's
target and how far through it they are, and the change against the month before.

**The month.** Their journal, in their columns and their order: `DATE`,
`RÉFÉRENCE`, `FOURNISSEUR`, `NBRE DE PIÈCE`, `POIDS`, `P.U`, `PRIX`,
`TRANSPORT`, `ESSENCE`, `AUTRES`, `MONTANT`, `OBSERVATION`. Oldest first, closed
with a `TOTAL` line.

**Fournisseurs.** What the sheet cannot do today: every supplier with their
rows, weight, amount, average price and last purchase. Rows with no supplier are
reported as `Non renseigné` rather than dropped, because in the real ledger that
is the largest line of all.

Headings are in French. The person who opens this file is the person who wrote
the sheet it replaces.

## 3. The finish they were doing by hand

- Dates are **real dates** and figures are **real numbers**, so the file sorts,
  filters and adds up on its own.
- Number formats per column: weight to three decimals, money to two.
- The header row is **frozen** and carries an **autofilter**, so a long month
  stays usable and one supplier or one day can be pulled out without touching
  the data.
- The header green is taken from the company letterhead, so a printed sheet
  matches their paper.

## 4. Preview and file are one thing

`server/exports/ledger-report.ts` builds the report as data. The screen renders
it, and the spreadsheet writer writes it. Neither computes anything of its own.

If the preview and the download could disagree, the manager would stop trusting
both, so they cannot: same rows, same columns, same headings, same TOTAL line.

The preview is at `/reports`, reachable in one click from the ledger, with a
sheet switcher and the download button beside it.

## 5. Tests

192 unit and integration tests, up from 173. The 19 new ones write the file and
**read it back with a spreadsheet library** rather than trusting the writer:

- the report: the company's column names in their order, the journal oldest
  first, the TOTAL line equal to the ledger screen, the columns adding across to
  the month total, suppliers balancing against the journal, an empty month
  producing headings and a zero rather than invented rows
- the file: a real xlsx archive, three sheets in order, headers in row four
  where the frozen pane expects them, dates written as dates and figures as
  numbers with the right formats, the TOTAL line under the last row in bold, a
  frozen header, an autofilter, and the column summing in the file to the same
  figure the screen shows

## 6. End-to-end

`npm run test:e2e:export`:

| Check | Result |
|---|---|
| An anonymous download is refused | pass, 401 |
| A signed-in download succeeds | pass |
| Served as a spreadsheet, named after the month | pass |
| The bytes are a real xlsx archive | pass |
| The preview shows the company's own column names | pass |
| The preview names the file that will download | pass |
| An earlier month exports too | pass |

The file was also downloaded through the browser and opened with an independent
reader: three sheets, 98 rows, frozen at A5, filter over A4:L97, and a TOTAL of
38 259 kg and 1 680 067 MRU, matching the screen.

## 7. Three defects the file itself revealed

**Every row landed a day early.** A date built at local midnight is written to
the file as the previous evening in UTC, so `2026-08-01` opened as
`31/07/2026`. Dates are now built at UTC midnight, and a test asserts every
written row matches its own day.

**The TOTAL label was written as a date.** The first column is a date column, so
the writer tried to parse the word `TOTAL` and produced `Invalid Date`. It now
converts only what actually looks like a date.

**The purchases query hid half the ledger.** It joined collectors with an inner
join, so the 52 rows with no supplier never appeared. Caught by the
reconciliation test comparing 66 rows against 118. It is a left join now, and
rows without a supplier read "not recorded".

The demo data was also corrected: it gave every row a supplier, which is not
what the real ledger looks like. Roughly half now have none, as in the workbook.

## 8. Not in this slice

No PDF. No emailing the file. No custom date range: months only, because months
are how the company closes its books. No import of the historical workbook yet,
which is what would put July and August of the real sheet into the system.

## 9. Next

The tranche ledger and the invoice: the `AHMEDOU` block from the workbook, with
amount, paid and remaining per shipment, and a document generated onto the
company letterhead.
