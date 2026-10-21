# Status

**The one file to read first.** Where the project stands right now, what works,
what does not, and what is next. Updated at the end of every session.

The build reports (`docs/NN-*.md`) are history: what was built in one sitting and
why. The decisions (`docs/decisions/`) are the rules that came out of them. This
file is the present tense, and it is the only file that gets rewritten rather
than appended to.

Last updated: **1 September 2026**, after the invoice PDF (report 19).

---

## In one paragraph

The manager's daily loop works end to end: sign in, record the day, watch the
month, correct a mistake, export the ledger to Excel, bill a client, take a
payment, print the invoice on the company letterhead. His real July and August
are loaded from the workbook. What is missing is not the loop but its edges:
six screens are honest stubs, the invoice has no downloadable file, and nothing
is deployed yet.

## What works, end to end

| | Where | State |
|---|---|---|
| Sign in | `/sign-in` | Argon2id, DB sessions, 5-attempt lockout, roles enforced server-side |
| Record the day | `/purchases` | One form for purchases and running costs: a weight makes it a purchase, costs alone make it an expense |
| See the month | `/purchases` | Calendar of the month, darker the more came in; click a day for its rows |
| Correct a mistake | `/purchases?edit=` | Amend or void, reason required, stock moves by the difference |
| Export | `/api/exports/ledger` | Any period as .xlsx in the workbook's format |
| Bill a client | `/invoices` | Tranches per client with amount, paid, remaining |
| Take a payment | `/invoices?pay=` | Overpayment refused; status follows the money |
| The invoice document | `/invoices/[id]` | On the artwork from their Word letterhead, French, one A4 page |
| Send an invoice | `/invoices/[id]` → Download PDF | A real file, printed from that same page, named for the invoice and client |
| Watch the book | `/` | Dashboard: collection, spend, stock, daily shape |

Also built: `/contracts`, `/contracts/[id]`, `/inventory`, `/collectors`, `/reports`.

## What is not built

**Six stubs.** `deliveries`, `profitability`, `settings`, `team` are genuinely
missing. `expenses` and `payments` are *listing* screens only: both jobs already
work (in the ledger and against an invoice), and each stub now says so and links
there.

**Nothing is deployed.** The stack is written and its guards are tested, but
`docker build` has never been run: Docker Desktop would not start on this
machine. This is now the only step between the project and the manager using
it.

**The AHMEDOU tranche block is not imported.** Eight tranches, 8 988 MRU
outstanding, and no dates. An invoice needs an issue date and none may be
invented.

## The data in the app

Their real book, imported from `ACHAT UBC (2).xlsx`:

- 43 purchases, 9 running costs, 10 448 kg of stock
- July reconciles exactly with the workbook
- **August is 243 MRU below the workbook**, entirely rows 27 and 30, where PRIX
  does not equal weight times price. Reported, never corrected (decision 0016)
- 1 invoice, `FA-2026-0001`, description `....`, believed to be a manual test

`npm run db:real` reloads it; `npm run db:seed` returns the demo dataset.

## Verification

403 tests over 17 files. 75 browser checks over 6 suites. Lint clean, build
compiles. The browser suites **refuse to run against a non-scratch server**
(decision 0017), so they cannot write into the real book again.

## A warning about the machine

The client's data volume sits at 98 per cent full, 5.7 GB free of 228 GB, and it
filled completely mid-session. Docker alone holds 8 GB and has never
successfully started; the npm cache held 3 GB and Chrome's caches 2 GB.

The browser suites each open a Chrome profile directory and clean up
best-effort, so a crashed run leaves one behind. Fifteen had accumulated, 686 MB,
and they were the last straw rather than the cause. If commands start failing
with `ENOSPC`, `rm -rf /tmp/cdp-*` is the first thing to try.

## Waiting on the client

Eight open questions in `docs/wiki/22-open-questions.md`. The three that block
work:

1. **#36 — row 30.** Is the weight right or the price? Until answered the app's
   August total stays 243 MRU below the workbook's.
2. **#37 — tranche dates.** Without them the AHMEDOU block cannot be imported.
3. **#22, #23, #30 — the invoice's legal identity.** Is it a tax document, what
   registration line does Mauritania require, is a stamp needed. The document
   leaves the slot empty and prints nothing rather than inventing it.

## Next, in the order I would do it

1. **Verify `docker build`, then deploy.** Everything else is refinement.
   Needs Docker Desktop to start, which it has not on this machine.
2. **The listing screens** for expenses and payments, which are small because
   the write paths exist.
3. **Deliveries and profitability**, which are new domain work.

`settings` and `team` are last: one operator, and the settings that matter
(monthly target, company details) are one row in the database.
