# 08 — What the real workbook and letterhead changed

**Date:** 30 August 2026
**Slice:** the product realigned to how the company actually works
**Decisions:** [0011](decisions/0011-one-operator-not-a-team.md),
[0012](decisions/0012-months-not-rolling-windows.md)
**Status:** 152 tests passing

---

## 1. What the sources said

Two files were supplied: the live workbook and the company letterhead. Both were
read in full and the findings are now in `docs/wiki/02` and `docs/wiki/03`.

**The workbook is one person's daily ledger.** Two month blocks in a single
sheet, each closed with a TOTAL row: July at 3 275 kg and 119 841 MRU, August at
7 081.8 kg and 279 480.5 MRU, with `TOTAL KG` adding the two to 10 356.8.

Three things in it contradicted assumptions the product had been built on:

- **Roughly half the rows have no supplier.** The manager records weight and
  price; who it came from is often not captured. A required collector field
  would stop him entering his own data.
- **Many rows are pure expense.** Eight rows in August carry only AUTRES and
  MONTANT, no weight. He enters purchases and expenses in one list, on the same
  day line. Two separate screens would be a step backwards from the sheet.
- **A client tranche block sits beside the ledger.** Columns O to W track one
  counterparty, AHMEDOU, across eight tranches with amount, PAYE and RESTE.
  That is what the company has instead of invoices, and the total still owed is
  1 760.

**The letterhead is blank.** It carries no invoice layout at all: no columns, no
numbering, no legal block. What it does carry is verified contact detail, which
had been marked "placeholder until confirmed" since the first day.

## 2. What changed in the product

**Verified company details replaced the placeholders.** Two phone numbers, two
email addresses, BP 8629, and the industrial-zone address are on the organisation
record and will print in the footer of every generated document.

**Only the operators can sign in.** The manager and the owner have passwords. The
accountant, the field worker and the driver remain as person records so history
stays attributable and a delivery can name its driver, but they have no password
hash and cannot sign in. The permission matrix stays as written and tested;
what stops is designing the product around a team that will not use it.

**Reporting moved to calendar months.** The dashboard opens on the current month,
offers the previous one, and compares the two. Every figure can now be checked
against the sheet, line for line.

## 3. What this means for the plan

The remaining build order changes shape. The old plan had a purchase form,
deliveries, then documents. The workbook says the order is:

**Next: the daily ledger.** One screen, one list, one row per entry, in date
order, with the workbook's own columns: date, supplier (optional), pieces,
weight, unit price, price, transport, fuel, other, total. A row may be a
purchase, an expense, or both. Month totals at the foot. This is the screen the
manager opens every morning, and until it exists the product cannot replace the
sheet.

**Then the month close and the Excel export.** He must be able to hand someone
the same workbook he hands them today.

**Then tranches and the invoice.** The AHMEDOU block becomes a client ledger of
tranches with amount, paid and remaining, and the invoice generator fills the
blank middle of the letterhead.

Deliveries, vehicles, the mobile app and the field-worker flows drop down the
order. They describe a company with staff on the system, and this one has a
manager with a phone.

## 4. Still unanswered

The workbook raised as many questions as it settled, and they are in
`docs/wiki/22`:

- What does one container weigh, so "1 container per month" can be measured?
- Is AHMEDOU a client, a financier, or both?
- Should a row with no supplier be allowed permanently, or nudged?
- Is the invoice a tax document, and what must it carry in Mauritania?

None of these are invented in code. Each has a provisional answer or an explicit
gap.
