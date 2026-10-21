# Documentation

Everything written about this product lives here. Everything executable lives in
`frontend/`. The two files kept at the repository root are `README.md` and
`claude.md`, the latter because Claude Code loads it from the root automatically.

## Where to look

| I want to | Read |
|---|---|
| Understand the product and the business | [idea.md](idea.md) |
| Know who this is for and what it must not look like | [PRODUCT.md](PRODUCT.md) |
| Know the visual system that ships | [DESIGN.md](DESIGN.md) |
| Understand the domain | [wiki/00-index.md](wiki/00-index.md) |
| Know why something is built the way it is | [decisions/](decisions/) |
| Know what is unresolved with the client | [wiki/22-open-questions.md](wiki/22-open-questions.md) |
| See what has actually been built | the build reports below |
| Work on the code as an agent | `../claude.md` and [agents.md](agents.md) |

## Contents

- `idea.md` — product vision, business model, strategy, MVP, UX direction.
- `PRODUCT.md` — who the product is for, its personality, its anti-references
  and the strategic design principles. Read before any interface work.
- `DESIGN.md` — the visual system that ships: colour, typography, layout,
  components, motion. If it disagrees with the stylesheet, the stylesheet wins.
- `agents.md` — working contract for AI agents in this repository.
- `wiki/` — the domain source of truth, 26 files from business model through
  build order and review gates.
- `decisions/` — numbered architectural and product decisions with their reasons.
- `product/` — feature matrix, user stories, acceptance criteria, task template.
- `architecture/` — decision principles and API contract guidance.
- `design/` — premium marketing site plan.
- `ops/` — daily, weekly and monthly reporting model.
- `ai/` — automation roadmap, deliberately sequenced after the transactional core.
- `qa/` — testing strategy.
- `source_materials/` — the supplied Excel workbook and receipt reference that
  the domain analysis was drawn from.
- `references/` — visual references supplied for the product interface.
- `screenshots/` — screenshots of the built application, used by the reports.

## Where the project stands

Read **[STATUS.md](STATUS.md)** first. It is the present tense: what works, what
does not, what the data looks like, and what is next. It is rewritten every
session, unlike everything below it.

## Build reports

One report per delivered slice: what was built, the decisions taken inside it,
what was verified and how, what is deliberately unfinished, and which open
questions the work touched. These are session records. Durable rules belong in
`wiki/` and `decisions/`, not here.

| Report | Slice | Date |
|---|---|---|
| [19 — The invoice as a file](19-the-invoice-as-a-file.md) | A real PDF download, printed from the same page a person looks at | 1 Sep 2026 |
| [18 — Paths and a guard](18-paths-and-a-guard.md) | The record button led nowhere; the tests were writing into the real book | 31 Aug 2026 |
| [17 — The month, day by day](17-the-month-calendar.md) | A calendar on the ledger: which days were recorded, and the rows behind one | 31 Aug 2026 |
| [16 — Deployment](16-deployment.md) | The image, the stack, TLS, backups, and the guards against losing data | 31 Aug 2026 |
| [15 — Importing the real workbook](15-importing-the-real-workbook.md) | July and August imported, with a report of what the sheet gets wrong | 31 Aug 2026 |
| [14 — The invoice document](14-the-invoice-document.md) | The invoice on the company letterhead, in French, one A4 page | 31 Aug 2026 |
| [13 — Invoices and payments](13-invoices-and-payments.md) | Bill a shipment, take money against it, see what each client owes | 31 Aug 2026 |
| [12 — Corrections](12-corrections.md) | Amend and void a recorded row, with the reason kept | 30 Aug 2026 |
| [11 — Any period, and a lighter hand on entry](11-any-period.md) | Export over any date range; entry that carries the day and the price | 30 Aug 2026 |
| [10 — Preview and export the month](10-export.md) | The month as a spreadsheet, previewed and downloaded | 30 Aug 2026 |
| [09 — The daily ledger](09-daily-ledger.md) | The first write path: one list, one row, one transaction | 30 Aug 2026 |
| [08 — What the real workbook and letterhead changed](08-source-reconciliation.md) | Sources read in full; one-operator scope, calendar months, verified company details | 30 Aug 2026 |
| [07 — Backend task 4: permissions](07-permissions.md) | Role matrix enforced on the server, refusal screen, wiki reconciled | 30 Aug 2026 |
| [06 — Backend task 3: authentication](06-authentication.md) | Sessions, sign-in, the guard, org scoping; 129 tests plus browser checks | 30 Aug 2026 |
| [05 — Backend task 2: the read layer](05-read-layer.md) | Screens read Postgres; 105 tests | 30 Aug 2026 |
| [04 — Backend task 1: the database](04-database.md) | Postgres schema, constraints, migrations, seed | 30 Aug 2026 |
| [03 — The bento dashboard](03-bento-dashboard.md) | Dashboard and shell rebuilt on the client's supplied reference system | 30 Aug 2026 |
| [02 — Next.js rebuild](02-nextjs-rebuild.md) | The Next.js foundation and architecture. Its visual language is superseded by 03. | 30 Aug 2026 |
| [01 — Frontend foundation](01-frontend-foundation.md) | The React build that 02 replaced. History. | 30 Aug 2026 |
