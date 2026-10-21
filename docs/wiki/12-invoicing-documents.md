# Invoicing & Documents

## Objective

Generate professional documents from the same structured transactional data used by the application.

## Document types

- Client invoice
- Payment receipt
- Purchase receipt
- Delivery note
- Contract/offer
- Statement/report

## Template system

Templates should be configurable per organization.

Suggested variables:

```text
{{company.name}}
{{company.address}}
{{company.phone}}
{{company.email}}
{{client.name}}
{{contract.number}}
{{material.name}}
{{quantity_kg}}
{{unit_price}}
{{total_amount}}
{{invoice.number}}
{{invoice.date}}
```

## PDF generation

The final system should generate stable, printable PDFs. Do not make the PDF a separate manually maintained data source.

**Built as a page, not a generated file** (decision 0015). The invoice is a real
route with a print stylesheet: sizes in millimetres, `@page { size: A4; margin: 0 }`,
and the letterhead `position: fixed` so it repeats on every sheet of a long
invoice. There is one layout, so the preview and the paper cannot disagree.
The browser's own print pipeline produces the PDF. If a downloadable file is
wanted later, headless Chrome prints this same route; the layout does not change.

## The letterhead

`docs/source_materials/receipt.png` is a blank letterhead. It is **not** used as
a background image. The wave boundaries were traced out of it and emitted as SVG
paths, and its four colours sampled: `#013c20` and `#018024` for the header,
`#4a9b00` and `#1f3b12` for the footer. The wordmark and the continent watermark
were cut from the same file with their backgrounds keyed out and live in
`frontend/public/`. The artwork is theirs; it is vector where it can be, so it
prints crisp rather than as a photograph of their stationery.

The company's phones, emails, address and postal box are columns on
`organizations`, not constants in code, so correcting them is a settings change.

## Language

The document is French; the application interface stays English. The workbook is
French, the letterhead is French, the reader is Mauritanian.

It carries the amount in letters — "Arrêtée la présente facture à la somme de
…" — which is standard on invoices in the region and is what a client reads to
check the figures were not altered. `src/lib/amount-in-words.ts` writes it in the
traditional spelling rather than the 1990 reform's hyphenate-everything rule.

## What is built

The invoice is modelled as a **tranche**: the shape the `AHMEDOU` block in the
workbook already has (`docs/wiki/02`), expressed in the `invoices`,
`invoice_lines` and `payments` tables rather than a new one. See decision 0014.

A tranche is billed either by weight and price, or as one agreed amount. Both
write an `invoice_lines` row, so the invariant "an invoice total is the sum of
its own lines" holds for every invoice in the table and the printable document
always has a line to show.

Two rules are enforced in `src/server/invoices/record.ts`:

- The invoice total is written once, at issue, and never recomputed. This is
  deliberately unlike every other figure in the system (decision 0004): a
  correction made next week must not change a number a client already holds.
- What is owed is never stored. `outstanding` is the total less the payments
  received, computed on read. There is no balance column and no code that
  increments one.

The status follows the money: a payment leaving a balance moves the invoice to
`PARTIALLY_PAID`, a payment closing it moves it to `PAID`. Nobody sets it by
hand, so it cannot disagree with the payments table. A payment larger than the
balance is refused, with the amount still owed named in the message.

`/invoices` shows one block per client — their tranches in the order agreed,
with amount, paid and remaining in the workbook's own columns — and takes a
payment in place of the row so the balance stays on screen while the amount is
typed.

## Invoice numbering

Use a server-generated sequence or configurable numbering scheme. Avoid deriving invoice numbers from timestamps alone.

Built as `FA-<year>-<0000>`, allocated inside the issuing transaction under an
advisory lock on `<org>:invoice`, continuing the series already in the data. A
unique index on `(organization_id, invoice_number)` is the backstop.

## Corrections

Issued documents should not be silently edited. Use:

- credit/debit adjustment
- cancellation/void
- replacement document

according to business/accounting policy.

## Company branding

The supplied reference suggests an Africa Aluminium green/white identity. The software should preserve that brand while allowing future template configuration.

## What is deliberately absent

No tax identifier, no registration line, no VAT. None appears on the supplied
letterhead and `docs/wiki/22` #22, #23 and #30 are unanswered. `organizations`
carries `tax_identifier` and `registration_line`, both null; the document prints
them when filled and prints nothing when not, and a test asserts it invents
neither. The stamp question is handled meanwhile by a signature rule labelled
"Cachet et signature", which is right either way.

## Document audit

Store:

- template version
- generated timestamp
- generated by
- source transaction IDs
- document number
- file hash or immutable reference where useful
