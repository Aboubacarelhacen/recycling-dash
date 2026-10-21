# 0018 — The PDF is the page, printed

Date: 1 September 2026
Status: accepted

## Context

[Decision 0015](0015-the-document-is-the-page.md) made the invoice a route with
a print stylesheet rather than a generated file, and said what the file would
cost when it was wanted:

> If a downloadable file is wanted later, the way to get it is to print this
> same route from headless Chrome. The layout does not change, so that stays a
> deployment decision rather than a rewrite.

It was wanted. The client noticed the gap himself: the ledger has an **Export
month** button that downloads a spreadsheet, while the invoice has a button that
opens the browser's print dialog. An invoice leaves this office on WhatsApp, and
that needs a file.

## Decision

The endpoint drives a headless Chromium over the **same route a person looks
at**, `/invoices/[id]`, carrying that person's own session cookie, and returns
what it prints.

The alternative was a PDF library: lay the invoice out a second time in code,
embed the letterhead by hand, no browser in the image. It is cheaper to run and
it is the thing 0015 already rejected. Two layouts drift the first time either
one changes, and the drift shows up on a document a client is holding.

So there is one layout. The file cannot disagree with the screen, because it is
the screen.

## What it costs

**About 180 MB of Chromium in the image**, the largest thing in it by a wide
margin, plus roughly a second and a burst of memory per invoice. For one manager
printing a handful of invoices a month that is a fair trade for a document that
can never quietly diverge. A pooled browser would be premature.

Without a browser the endpoint returns **503 with an instruction**, and the page
still prints from the reader's own browser. The feature degrades; it does not
break.

## The obvious way to get this wrong

An endpoint that renders a URL is a fetcher for whatever URL it is given. This
one takes none. The path is built here from an id checked against a UUID
pattern, and the origin is this server over the loopback:

```ts
path: `/invoices/${invoiceId}`,
origin: process.env.INTERNAL_ORIGIN ?? `http://127.0.0.1:${process.env.PORT ?? 3000}`,
```

Permission is checked, and the invoice loaded scoped to the reader's
organisation, **before** the browser is launched. An invoice belonging to
someone else is a 404 that costs no Chromium.

Loopback rather than the public name on purpose: printing must not depend on a
certificate, a proxy hop or DNS resolving inside the container.

## Consequences

`puppeteer-core`, never `puppeteer`: the latter downloads its own browser, which
would put a second 300 MB copy in the image beside the one the package manager
installed. A test asserts the dependency stays that way.

The page and the file are now verified together. The browser suite downloads a
real invoice and checks it begins `%PDF-`, ends `%%EOF`, arrives as an
attachment named for the invoice and the client, and is large enough to be
carrying the letterhead rather than an empty page.
