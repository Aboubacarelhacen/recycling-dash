# 11 — Any period, and a lighter hand on entry

**Date:** 30 August 2026
**Slice:** export over any date range, and an entry row that keeps up with the person using it
**Status:** 222 tests, 10 ledger browser checks and 8 export browser checks passing

---

## 1. What the company said

> He just comes to the project, records what was collected, clicks OK, and can
> export a very organised report of the month, or of half of the month, or
> whatever the situation is.

Two things: entry has to be effortless, and the export must not be limited to
whole months. Report 10 had said "months only". That was wrong, and this
corrects it.

## 2. Any two dates

A period is no longer a calendar month. It is any range, named the way a person
would say it:

| Range | Reads as |
|---|---|
| A whole month | August 2026 |
| Part of a month | 1 to 15 August 2026 |
| Across months | 20 July to 5 August 2026 |
| One day | 30 August 2026 |

The export screen offers the ranges he actually asks for, and two date boxes for
anything else:

**This month · Last month · 1st to 15th · 16th to 31st · Last 7 days · Last 30 days**

The half-month presets are there because the book is settled in tranches, not
only at month end: the `AHMEDOU` block in the source workbook tracks eight of
them.

Everything downstream already took a from-and-to pair, so the ledger, the
dashboard, the preview and the file all accept a range without a special case.
The custom range is a plain form with no JavaScript: it works before React
loads.

**A range is compared against the stretch of equal length before it.** For a
whole month that is the month before, which is what it always was. For 16 to 31
August it is 31 July to 15 August, rather than a month it does not correspond to.

**A monthly target is only scored over a month.** Fifteen days against a
thirty-one day target would read as failure, so a partial period states its
length instead of inventing a percentage.

**An absurd range is trimmed, not refused.** Ten years asked for by a stray
bookmark returns the last 366 days rather than reading the whole ledger into one
file.

## 3. A lighter hand on entry

Three changes, all from watching how a person actually fills a day's rows:

**The price carries.** It used to reset to a constant 35. It now starts from the
last price actually posted, and after a row is saved it keeps the price that row
used. In the source workbook the price holds at one figure for weeks.

**The day carries.** He often enters yesterday evening's rows this morning, and
several days in one sitting. The date no longer snaps back to today after each
save.

**The day's total is in front of him.** `Today: 7 rows · 1 885 kg · 97 705 MRU`
sits beside the entry row, which is the check he does at the foot of his own
page before closing the book.

The weight field still clears and takes focus after each save, so the next row
starts with the only thing that always changes.

## 4. Tests

222 tests, up from 192. The 30 new ones are the period model in full: month
boundaries including February in a leap year, the four label shapes, dates given
backwards, a full month written as two dates, the preceding range across a month
and a year boundary, the presets including a February second half, reading a
period off a link, nonsense in the query string, and the range trim.

The export tests now also cover a half-month file: only the rows inside it, the
two halves summing to the whole month, the comparison naming the preceding
stretch, the target not being scored over a partial period, and a single day.

## 5. In a browser

The ledger run grew to ten checks, four of them new: the day carries to the next
row, the price carries, the weight clears, and the running total is shown while
entering.

A half-month file was downloaded and opened with an independent reader: sheets
`Résumé`, `1 to 15 August 2026` and `Fournisseurs`, first row 1 August, last row
15 August, TOTAL 20 286 kg and 916 484 MRU.

## 6. One copy defect worth recording

A cross-month label read `31 Jul to 15 August 2026`, mixing a short month with a
long one. Both ends now use the full month name. It was caught by a test
assertion that had written it the way it should read.

## 7. Still missing for "he just records and clicks OK"

**A row cannot be corrected yet.** He will mistype a weight, and today the only
answer is that the row stands. This is now the most important gap in the
product, ahead of invoices: a correction path with the reason recorded against
his name, which the audit trail is already built to hold.

## 8. Next

Corrections: edit and void a recorded row, with the reason captured and the
stock movement reversed in the same transaction.
