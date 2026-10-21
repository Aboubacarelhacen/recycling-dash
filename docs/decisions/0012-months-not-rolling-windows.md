# 0012 - Months, not rolling windows

## Status

Accepted, 30 August 2026. Corrects the reporting period chosen in
`docs/05-read-layer.md`.

## Context

The dashboard reported a rolling thirty days ending on the operating date. That
is a software habit. The company's workbook is written in **calendar month
blocks**: JUILLET, then a TOTAL row, then AOUT, then a TOTAL row, with a separate
`TOTAL KG` cell adding the two.

A manager who checks the screen against his own sheet would have found two
numbers that never agree, because they measure different windows.

## Decision

Every reported period is a **calendar month**. The dashboard opens on the current
month, offers the month before it, and compares the two, which is the comparison
the workbook itself invites.

The month range lives in `domain/period.ts` and is passed to the queries as a
value, so the fixture path and the SQL path measure the same window and the
reconciliation tests still hold.

## Amended, 30 August 2026

Months remain the default and the dashboard still opens on one, but they are no
longer the only period. The company settles its book in tranches as well as at
month end, so any two dates now make a period and the export takes one. A range
is compared against the equal stretch before it, and a monthly target is only
scored over a whole month. See `docs/11-any-period.md`.

## Consequences

- Figures on screen can be checked against the sheet, line for line.
- A day with no buying still appears in the series, because a month has a fixed
  set of days and a gap is information.
- "Last seven days" inside a month is a tail of that month rather than a rolling
  week. When the goal moves to containers rather than kilograms, this is the
  window it will be measured in.
