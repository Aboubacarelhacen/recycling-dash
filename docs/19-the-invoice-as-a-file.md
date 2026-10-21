# 19 — The invoice as a file

Date: 1 September 2026
Slice: a download button that produces a real PDF of the invoice.

## 1. The gap the client found

He noticed it himself, looking at the two screens side by side: the ledger has
an **Export month** button that downloads a spreadsheet, and the invoice has a
button that opens the browser's print dialog. An invoice leaves this office on
WhatsApp. That needs a file.

## 2. Paying a bill written eight days earlier

[Decision 0015](decisions/0015-the-document-is-the-page.md) made the invoice a
route with a print stylesheet rather than a generated file, and named the price
of the file in advance:

> If a downloadable file is wanted later, the way to get it is to print this
> same route from headless Chrome.

That is what was built. `GET /api/invoices/[id]/pdf` drives a headless Chromium
over `/invoices/[id]`, the page a person actually looks at, carrying that
person's own session cookie, and returns what it prints.

The alternative was a PDF library: lay the invoice out a second time in code and
embed the letterhead by hand. Cheaper to run, no browser in the image, and the
thing 0015 already rejected. Two layouts drift the first time either one
changes, and the drift shows up on a document a client is holding. Written up as
[decision 0018](decisions/0018-the-pdf-is-the-page-printed.md).

**It costs about 180 MB of Chromium in the image**, by a wide margin the largest
thing in it, and roughly a second per invoice. That is the honest price of the
file and the screen never being able to disagree.

## 3. The obvious way to get this wrong

An endpoint that renders a URL is a fetcher for whatever URL it is handed. This
one accepts none. The path is built in the route from an id checked against a
UUID pattern, and the origin is this server over the loopback:

```ts
path: `/invoices/${invoiceId}`,
origin: process.env.INTERNAL_ORIGIN ?? `http://127.0.0.1:${process.env.PORT ?? 3000}`,
```

Permission is checked and the invoice loaded scoped to the reader's organisation
**before** any browser is launched, so someone else's invoice is a 404 that
costs no Chromium. Loopback rather than the public hostname on purpose: printing
must not depend on a certificate, a proxy hop, or DNS resolving inside a
container.

## 4. Degrading rather than breaking

With no Chromium the endpoint returns **503 and an instruction**: *"This server
has no Chromium installed, so it cannot build the file. Use Print on the invoice
instead."* The page still prints from the reader's own browser, which is what it
did before this slice. The document page now offers both buttons, because they
are different jobs: Download is for sending, Print is for paper.

## 5. What was verified

A real invoice was downloaded and then checked with tools that know nothing
about this code:

- **849 KB**, header `%PDF-1.4`, terminator `%%EOF`
- **one page**, MediaBox 595 × 841.9 pt, which is **210 × 297 mm exactly**
- text extracted by `pdftotext`: *"N° de facture, FA-2026-0001, Date
  d'émission, 30 août 2026, Facture, FACTURÉ À"*. Real selectable text with
  correct accents, not a picture of a page
- rendered back to an image and looked at: letterhead, watermark, the client
  block, the amount in words

**29 tests** across two files: 11 new in `tests/invoice-pdf.test.ts` on the
route's guards, including that a client name is stripped of anything a filename
header cannot carry, and 4 added to `tests/deployment.test.ts` asserting the
image installs Chromium, installs a fallback font, is given more than the 64 MB
default shared memory, and never pulls in `puppeteer` beside `puppeteer-core`.

Six checks added to the document browser suite: the file downloads, begins
`%PDF-`, ends `%%EOF`, arrives as an attachment named for the invoice and the
client, is large enough to be carrying the letterhead, and the page offers both
actions.

## 6. Two things that went wrong around it

**The sign-in helper in the browser harness waited twelve seconds** for the form
and then evaluated against whatever was on screen. A cold Next dev server can
spend longer than that compiling the route, and the failure surfaced as an
opaque "Uncaught" from inside the snippet. It now waits ninety seconds and, if
the form never arrives, says which URL it was on and what the page contained.

**The machine ran out of disk.** Each browser run gets its own Chrome profile
directory so runs cannot interfere, and cleanup is best-effort, so every crashed
run left one behind: fifteen directories, 686 MB. The volume was already at 99
per cent, and that was the last straw. The directories were removed, along with
`.next` and the npm cache, and the harness teardown was already tolerant of
Chrome still holding files. The underlying problem is the client's disk, not
this project: Docker alone holds 8 GB and it has never successfully started.

## 7. Next

Verifying `docker build`, which is now the only step between this and the
manager using it, and which needs Docker Desktop to start.
