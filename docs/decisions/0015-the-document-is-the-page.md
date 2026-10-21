# 0015 — The document is a page, not a generated file

Date: 31 August 2026
Status: accepted

## Context

The invoice has to leave the building: printed, or sent to a client as a PDF.
`docs/wiki/12` asks for "stable, printable PDFs" and, in the same breath, that
the PDF must not become "a separate manually maintained data source."

Two ways to do that.

**Render a PDF on the server.** A route that runs headless Chrome, or a PDF
library that lays the page out in code. It gives a file to download, symmetric
with the existing `.xlsx` export.

**Render the document as a real page with a print stylesheet.** The invoice is
a route; the browser's own print pipeline turns it into paper or a PDF.

## Decision

The page.

Headless Chrome is a heavy dependency to add and to keep alive in production
for one document, and a PDF library means laying the invoice out twice: once in
code for the file, once in HTML for the preview anybody wants before sending.
Two layouts is exactly the "separate maintained source" the wiki warns about.

A print stylesheet has one layout. What the manager sees on screen is what
comes out of the printer, because it is the same page. The sizes are written in
millimetres rather than pixels, `@page` asks for A4 with no browser margin, and
the letterhead is `position: fixed`, which is what makes the browser repeat the
stationery on every sheet when an invoice runs long.

If a downloadable file is wanted later, the way to get it is to print this same
route from headless Chrome. The layout does not change, so that stays a
deployment decision rather than a rewrite.

## The letterhead

The supplied `receipt.png` is a blank letterhead: a raster image with two green
waves, the wordmark, and a large continent watermark.

It is not used as a background image. The wave boundaries were traced out of
that file and emitted as SVG paths normalised to a 0-100 box, and the four
colours were sampled from it (`#013c20`, `#018024`, `#4a9b00`, `#1f3b12`). The
wordmark and the continent mark were cut from the same file with their
backgrounds keyed out, and live in `public/`.

So the artwork is theirs, not a lookalike, but it is vector where it can be and
prints crisp at any size rather than as a 1414px photograph of their stationery.
The company's phones, emails, address and postal box moved out of a TypeScript
constant and onto the `organizations` row, so changing them is a settings
change rather than a deployment.

## What is deliberately absent

No tax identifier, no registration line, no VAT. None appears on the supplied
letterhead, and `docs/wiki/22` #22, #23 and #30 are still unanswered: whether
this is a tax document, what registration line Mauritania requires, whether a
stamp is needed.

`organizations` carries `tax_identifier` and `registration_line`, both null. The
document prints them when they are filled and prints nothing when they are not.
A test asserts the document invents neither. The stamp question is answered the
cheap way in the meantime: the page leaves a signature rule labelled "Cachet et
signature", which is correct whether or not a stamp turns out to be required.

## Language

The document is in French. The workbook is French, the letterhead is French,
the client is Mauritanian. The application interface stays in English; the
artefact that leaves the building is in the language its reader uses.

It carries the amount in letters — "Arrêtée la présente facture à la somme de
…" — which is standard on invoices in the region and is the line a client reads
to check the figures were not altered. That is `src/lib/amount-in-words.ts`,
written to the traditional spelling rather than the 1990 reform's
hyphenate-everything rule, because that is what commercial documents there use
and because it keeps a seven-figure total readable.
