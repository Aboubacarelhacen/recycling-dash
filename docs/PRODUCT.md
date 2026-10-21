# Product

## Register

product

## Users

**Operations manager (primary, all day).** Owns contracts, procurement, inventory and
deliveries. Sits at a desk in a bright Nouakchott office, moving between phone calls and
the screen. Lives in this tool: wants to see many rows at once, wants keyboard speed, and
resents anything that costs a click.

**Owner / executive (daily, minutes at a time).** Wants margin, exposure and exceptions.
Reads, rarely writes.

**Accountant (daily).** Expenses, invoices, payments, reconciliation. Needs every number
traceable to the transaction that produced it.

**Field buyer (many times a day, phone, harsh sun).** Records a purchase at the
weighbridge in seconds. Not the primary surface for this build, but the data model and
components must not make that screen impossible later.

The job to be done: replace a spreadsheet that has become the company's memory, and answer
"are we going to fulfil this contract, and will it still be profitable" without a person
having to reconcile anything by hand.

## Product Purpose

An operations platform for a contract-driven recycling business. Client contracts pull
material through procurement, inventory, expenses and delivery to invoice and payment,
and every transaction stays attributable to the commitment that caused it. Success is a
manager trusting the screen enough to stop maintaining the workbook.

## Brand Personality

Precise, industrial, unshowy. The voice of a well-kept ledger: exact, plain, never
decorative. Three words: **exact, calm, durable.**

The interface should feel like an instrument. Confidence comes from restraint and from
numbers that always line up, never from ornament.

## Anti-references

- **Eco / environmental marketing.** No green gradients, no leaf motifs, no recycling
  arrows, no "sustainability" imagery. This is heavy industry and money, not a campaign.
- **Generic SaaS dashboard.** No hero-metric template, no identical card grids, no
  KPI tiles with gradient accents.
- **The previous build in this repository.** Lavender canvas, violet accent, floating
  panels, folder-shaped cards, soft toy-like shadows. Rejected on colour, typography,
  layout and finish.
- **Decorative depth.** Glassmorphism, oversized rounding, coloured shadows.

## Design Principles

1. **Restraint is the luxury signal.** Near-monochrome. Colour appears only where it
   carries meaning, and its scarcity is what makes it legible.
2. **Absence of colour is the healthy state.** A contract that is fine gets no colour at
   all. Amber and red are earned by exception, so a coloured row always means act.
3. **Density serves the operator.** More rows on screen beats more air around them. The
   people who live in this tool want to see the whole book at once.
4. **Every number opens.** No figure is a dead end; each one leads to the transactions
   behind it. A number a manager cannot trace is a number they will not trust.
5. **Structure over containers.** Hairlines, alignment and space define regions. Cards
   only where elevation carries real meaning.

## Accessibility & Inclusion

WCAG AA minimum, AAA for body text where achievable. The office is bright and screens are
not always good, so contrast is a functional requirement, not a compliance box. Status is
never carried by colour alone: every state ships with a word. Full keyboard operation,
visible focus, and `prefers-reduced-motion` honoured. UI language is English; numbers use
the French convention of the source ledger. Arabic or French UI is an open question
(docs/wiki/22, #27), so no layout may hard-depend on English string lengths.
