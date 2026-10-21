# Recycling Operations Platform — Africa Aluminium

Knowledge base, product decisions and the application itself.

## Purpose

This repository is the single source of truth for building a premium recycling procurement and contract-operations platform for **Africa Aluminium** and, later, potentially other recycling companies with similar workflows.

The company currently operates through client offers/contracts, material procurement from collectors/suppliers, field expenses, inventory/fulfilment, deliveries, and client-facing invoices/receipts. Their current operating record is centered on Excel and document templates. The product goal is to convert that fragmented workflow into one connected system of record.

## Core product thesis

> **Do not build “an app that replaces Excel.” Build the operating system for a contract-driven recycling operation.**

The key chain is:

`Client Contract → Procurement → Weighing → Inventory → Expenses/Logistics → Delivery → Invoice/Payment → Real Profitability`

Every important event should be traceable back to the relevant contract, material, supplier/collector, vehicle, user, and date.

## How to use this repository

1. Read `docs/idea.md` before making product decisions.
2. Read `docs/agents.md` before touching the codebase.
3. Read `claude.md` when working with Claude Code / coding agents.
4. Treat `docs/wiki/` as the domain source of truth.
5. Treat `docs/decisions/` as the record of architectural/product decisions.
6. Never invent business rules that are not documented. Mark uncertain rules as open questions.
7. For implementation, prefer small vertical slices that move from database → API → UI → tests.

## Repository map

Two things live at the root: this file, and `claude.md`, which Claude Code loads
automatically and therefore cannot move. Everything written lives in `docs/`,
everything executable lives in `frontend/`.

```text
README.md          you are here
claude.md          Claude Code execution playbook and guardrails
docs/              all documentation, knowledge and reference material
frontend/          the web application
```

### docs/

- `docs/README.md` — documentation hub and the index of build reports.
- `docs/idea.md` — product vision, business model, strategy, MVP, UX direction.
- `docs/agents.md` — general AI-agent working contract for the repository.
- `docs/03-bento-dashboard.md` — current build report: the dashboard and shell.
- `docs/02-nextjs-rebuild.md` — the Next.js foundation and architecture.
- `docs/PRODUCT.md` and `docs/DESIGN.md` — strategic and visual context. Read
  both before touching the interface.
- `docs/wiki/` — the domain source of truth, `00-index.md` through
  `25-build-order-and-review-gates.md`.
- `docs/decisions/` — numbered architectural and product decisions.
- `docs/product/` — feature matrix, user stories, acceptance criteria, task template.
- `docs/architecture/` — decision principles and API contract guidance.
- `docs/design/` — premium marketing site plan.
- `docs/ops/` — daily, weekly and monthly reporting model.
- `docs/ai/` — automation roadmap.
- `docs/qa/` — testing strategy.
- `docs/source_materials/` — the supplied Excel workbook and receipt reference.
- `docs/references/` — visual references supplied for the product UI.
- `docs/screenshots/` — screenshots of the built application, used by the reports.

### frontend/

Next.js (App Router) + TypeScript + Tailwind v4. Pages are server components
reading the domain layer directly; the canonical calculations in
`src/lib/calc.ts` are the single definition of every derived number and must be
ported to the server when one exists.

```bash
cd frontend && npm install && npm run dev
```

## Golden rule

When there is a conflict between a pretty interface and correct business behavior, **business correctness wins**. When there is a conflict between a clever implementation and a boring maintainable implementation, **maintainability wins**.
