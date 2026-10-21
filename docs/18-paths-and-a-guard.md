# 18 — The paths through the app, and a guard on the real book

Date: 31 August 2026
Slice: fixing how a person reaches the work, and stopping the tests from
writing into the company's ledger.

**Written late.** This work happened between reports 16 and 17 and I did not
write it up at the time, which is exactly the failure this file exists to
prevent. The numbering follows when it was written, not when it was built.

## 1. What prompted it

The client asked a question rather than reporting a bug: *"how the user will
record all that stuff every day, where will he record them from? I can see
something in this daily ledger, but I feel like it's not user friendly, or maybe
it's actually good."*

That instinct was right, and the cause was not the form.

## 2. The most prominent button in the application was a dead end

The black circular button at the foot of the icon rail, the one obvious "add
something" control on every screen, pointed at `/purchases/new`. That route was
a stub reading **"Not built yet"**, and its own copy was stale: it described the
fast-entry form as "the next slice" and the application as read-only, both
untrue since report 09.

So the natural path to record a purchase ended in a wall, while the form that
does the job sat at the top of the ledger.

Two more paths ended the same way. **Expenses** and **Payments** were stubs, but
neither job is missing: a running cost is a ledger row with no weight, and a
payment is recorded against the invoice it settles. A person who thought "I need
to record an expense" clicked Expenses and found nothing.

Fixed:

- The rail button goes to `/purchases#record`, the entry form, and focuses it.
  `/purchases/new` remains as a redirect so an old bookmark still lands somewhere
  useful. There is no separate "new purchase" screen and there should not be: a
  manager entering a day's rows wants the form and the day's rows on one page.
- `Unbuilt` gained an optional way out, and the two stubs whose work already
  exists now name where it happens and link there.

The form itself was never the problem. The routes to it were.

## 3. Test data in the company's ledger

Looking at `/invoices` to answer the client's question about invoicing, the
screen showed six invoices described `e2e tranche` and `e2e document shipment`.
Mine, from browser test runs.

The browser suites write, and they point at whatever server is running. That was
harmless while the server held demo data. After report 15 it held the real book.

Removed, and the book verified back to 43 purchases, 9 running costs, 10 448 kg,
0 invoices. Then the hole was closed rather than swept: `/api/health` now reports
whether a server is disposable and the suites refuse anything else. Written up
as [decision 0017](decisions/0017-tests-never-touch-the-real-book.md).

Both halves were verified: the suites now refuse port 3000 with an instruction,
and pass against a scratch database while the real book stays at 43 purchases.

This was my mistake, and it should not have taken the client noticing a screen
to surface it.

## 4. Two smaller things

`suppressHydrationWarning` on `<body>`. Grammarly writes
`data-gr-ext-installed` onto the body before React hydrates, and the mismatch is
reported as ours. It suppresses that one element's own attributes, one level
deep, and we set none there, so nothing real can hide behind it. A hydration
warning a person learns to ignore is one they will ignore when it is real.

The harness teardown no longer crashes a passing run when Chrome still holds its
profile directory for a moment after the kill.

## 5. Next

Report 17, the month calendar, which the client asked for immediately after
this.
