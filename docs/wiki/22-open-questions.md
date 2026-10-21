# Open Questions

These are intentionally unresolved. Do not invent answers in code.

**Some now have a provisional answer**, taken because the build needed one. Each
is marked below with where it lives, and each is a settings change rather than a
rewrite. They still need confirming with the company; a provisional answer is not
a decision.

| Question | Provisional answer | Where |
|---|---|---|
| 3. Several materials on one contract | Supported: header material plus `contract_items` | `schema.ts` |
| 6. Is unit price always per kg | Assumed per kg throughout | `lib/calc.ts` |
| 11-15. Cost attribution | Trip costs on posted purchases plus contract-tagged expenses; untagged spend is overhead | `contractMetricsFromTotals` |
| 16. One warehouse or several | Several, and stock is held per material and location | `inventory_movements` |
| 19. Available versus reserved | Reserved is weight on deliveries that are READY or DISPATCHED; available is on hand less reserved | `queries/inventory.ts` |
| 20. Currencies | MRU throughout, with an explicit currency column everywhere | `schema.ts` |
| 27. Interface language | English, with French number formatting from the source ledger | `lib/format.ts` |
| Health thresholds | Pace 0.9 / 0.7, deadline 14 days, margin 12 % / 5 % | `HEALTH_THRESHOLDS` |
| Monthly target | An organisation setting, not a rule | `organizations.monthly_target_kg` |
| 25. How many employees use the system | One: the manager. The owner may look | `decisions/0011` |
| 26. Field workers and connectivity | Not applicable. The field is a phone call to the manager | `decisions/0011` |
| Reporting period | Calendar months, matching the workbook | `decisions/0012` |

## Raised by reading the real workbook

31. What does one container weigh, so "GOAL 1 CONTENAIR PER MONTH" can be
    measured in kilograms? **A real data load now leaves the target unset** and
    the dashboard says so, rather than showing a figure nobody agreed to
    (decision 0016).
32. Is AHMEDOU a client, a financier, or both? The tranche block tracks amount,
    paid and remaining against eight shipments. **Built as a client account
    pending the answer** (decision 0014): the arithmetic is identical either
    way, so only the counterparty type would change.
33. Should a purchase row without a supplier be allowed permanently? Half the
    historical rows have none.
34. Are the expense-only rows in the ledger a category of their own, or general
    running costs that happen to be recorded on a day?

## Commercial

1. Is a client offer always a formal contract, or can it be an informal order?
2. Is client revenue defined by fixed contract value or quantity × selling price?
3. Can a contract include several materials/grades?
4. Is there a fixed deadline and what happens when it is missed?
5. Are partial deliveries billed?

## Purchasing

6. Is purchase unit price always per kg?
7. Are there quality/deduction rules?
8. Is `NBRE DE PIECE` important operationally or legacy-only?
9. Can one purchase be split across contracts?
10. Can one transaction contain multiple materials?

## Costs

11. How should fuel be attributed?
12. How should transport be attributed?
13. Is food a direct contract cost or company overhead?
14. Are worker payments included in contract profitability?
15. How are shared expenses allocated?

## Inventory

16. Does the company use one warehouse or several?
17. Is all purchased material physically stored before delivery?
18. Are quantities tracked before/after sorting or processing?
19. What is the official definition of available vs reserved stock?

## Finance

20. What currencies are used?
21. What payment methods are common?
22. Are invoices tax/VAT documents?
23. What legal invoice fields are required in Mauritania for this business?
24. When is revenue considered earned/received?

## Operations

25. How many employees use the system?
26. Do field workers have smartphones and what connectivity is typical?
27. Is Arabic, French, English or multilingual UI required?
28. Is WhatsApp used for operational reporting?

## Documents

29. Which exact document(s) from the current Word/PDF process must be replicated?
30. Are signatures/stamps required?
31. Does the company want email/WhatsApp sending from the platform?

## Raised by importing the real workbook

35. Are `MOUSSA` and `MOUSSA 2` the same supplier? And `TEREIRA` and
    `TERREIRA`? Both pairs are imported as separate collectors and reported as
    possible duplicates; merging them changes who the company bought from.
36. Row 30 records 286,5 kg at 35 but a PRIX of 10 275, where the arithmetic
    gives 10 027,5. Row 27 is 4,5 the other way. Which is right, the weight or
    the price? Until this is answered the application's August total is 243 MRU
    below the workbook's.
37. What dates belong to the eight `AHMEDOU` tranches? Without them they cannot
    become invoices, because an invoice carries an issue date.
