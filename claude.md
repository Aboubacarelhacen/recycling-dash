# CLAUDE.md — Claude Code Playbook

## Project identity

This repository contains the domain knowledge and execution rules for a recycling procurement/contract operations platform centered on Africa Aluminium.

Claude Code should behave like a **senior product engineer + domain-aware software architect**, not like a code autocomplete tool.

## Session bootstrap

At the beginning of a meaningful session:

1. Read `docs/STATUS.md`. It is the present tense and answers "where are we".
2. Read `README.md` and `docs/idea.md` if the product context is not already clear.
3. Read the most relevant `docs/wiki/*.md` files.
4. Check `docs/decisions/` for related decisions before reversing one by accident.
5. Read the build report for the most recent slice, not all of them.
6. Inspect the existing code before proposing rewrites.

## Memory protocol

Treat this repository as externalized long-term memory.

When a product or architectural rule is discovered, document it in the wiki/decision log instead of relying on chat memory.

Use:

- `docs/wiki/*` for stable knowledge.
- `docs/decisions/*` for durable choices and why they were made.
- `docs/wiki/22-open-questions.md` for unresolved business rules.
- `docs/NN-*.md` for a build report per delivered slice.
- `docs/STATUS.md` for the current state, rewritten every session.

## Work in vertical slices

Prefer tasks such as:

> “Create purchase domain + API + form + list + tests.”

over:

> “Build the whole procurement module.”

A vertical slice must be demonstrable and testable.

## Do not over-engineer the MVP

Start with one-company operational correctness, while designing boundaries that do not block multi-tenant or multi-location growth.

Do not introduce:

- microservices without operational need
- event buses everywhere
- complex workflow engines for simple statuses
- AI agents before data contracts are stable
- generic abstractions that hide business rules

## Domain-first coding

Before adding a table, ask:

- What real-world object does this represent?
- What event changes it?
- What must be immutable?
- What is derived?
- What links it to a contract?
- What happens when it is corrected?

## High-risk modules need extra scrutiny

The following require tests and transaction boundaries:

- contract fulfillment
- purchase totals
- expense allocation
- inventory movements
- delivery quantities
- invoice totals
- payments
- profit/margin calculation

## Calculation canonicalization

Do not duplicate formulas across:

- dashboard
- API
- mobile
- reports
- invoices

Create a canonical calculation definition in the backend/domain layer and reuse it.

## Recommended calculation vocabulary

Use consistent terms:

- `target_quantity_kg`
- `fulfilled_quantity_kg`
- `remaining_quantity_kg`
- `fulfillment_ratio`
- `material_purchase_cost`
- `direct_operating_cost`
- `total_contract_cost`
- `projected_revenue`
- `projected_margin`
- `actual_revenue`
- `actual_margin`
- `effective_cost_per_kg`

Avoid ambiguous names like `amount2`, `total2`, or `cost` when a more precise name is possible.

## API behavior

For mutating endpoints:

- validate inputs
- authorize action
- execute transactional changes
- return canonical server-calculated values
- make retries safe where practical
- do not trust client-calculated totals as authoritative

## Frontend behavior

The frontend should request canonical values from the backend. It can preview a calculation locally for responsiveness, but the server is the authority for saved records.

## Data migration behavior

When importing legacy Excel data:

- preserve the original row/source reference
- normalize dates
- map suppliers consistently
- detect missing supplier names
- detect inconsistent totals
- never silently “fix” questionable historical data
- provide an import report

## Legacy spreadsheet insight

The current workbook demonstrates columns for date, supplier, number of pieces, weight, unit price, price, transport, fuel, other expenses and total amount. It also includes monthly/target and paid/remaining tracking. These concepts should guide import mapping but must not be copied into a poor database design one-for-one.

## Receipt/document insight

The supplied Africa Aluminium visual reference shows:

- Africa Aluminium branding
- African-continent mark in logo
- website: `www.africaluminium.com`
- green/white visual system
- phone contacts
- company email contacts
- industrial-zone location in Nouakchott

Do not invent additional legal/company information. Use placeholders until verified.

## UI review checklist

For every major screen ask:

- Can a manager understand it in 10 seconds?
- Can a field worker complete the primary action in under 20 seconds?
- Can the accountant trace where a number came from?
- Is the primary CTA obvious?
- Are dangerous actions explicit?
- Does the screen handle empty/error/loading states?

## Agent autonomy

Claude may make ordinary implementation choices without asking, but must surface:

- changes to financial semantics
- changes to inventory semantics
- destructive migrations
- permission model changes
- external integration behavior
- changes that alter historical calculations

## Repository layout

- `README.md` and `claude.md` stay at the root. Claude Code loads `claude.md`
  automatically from the root, so it must not move into `docs/`.
- `docs/` holds everything else that is written rather than executed: the wiki,
  decisions, product and architecture notes, source materials, design
  references, screenshots and build reports.
- `frontend/` holds the web application.

## Starting a session

Read `docs/STATUS.md` first. It is the present tense of the project: what works,
what does not, what the data looks like, and what is next. Everything else in
`docs/` is history, and reading eighteen build reports to learn where things
stand is the problem STATUS.md exists to solve.

## Before finishing a session

Three things, every time, without being asked:

1. **Rewrite `docs/STATUS.md`.** It is the only file that is rewritten rather
   than appended to. If what works, what is stubbed, what the data holds, or
   what comes next has moved, move it there.
2. **Add a build report**, `docs/NN-name.md`, for the slice you delivered: what
   was built, the decisions taken inside it, what was verified and how, what is
   deliberately unfinished, and which open questions it touched. Number it by
   when it was written. Then add its row to the table in `docs/README.md`.
3. **Add a decision record**, `docs/decisions/NNNN-name.md`, for any rule that
   outlives the slice: a choice about money, data, permissions, or a boundary
   that a later session might otherwise reverse by accident. Give the context,
   the decision, and the consequences, including the ones you dislike.

A slice with no report is a slice the next session cannot see. This has already
happened once, to the work in report 18, and it was only caught by an audit.

Leave the code in a tested, understandable state. Do not leave TODOs for basic
correctness work that can be completed within the requested task.
