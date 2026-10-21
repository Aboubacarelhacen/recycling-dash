# AGENTS.md — Repository Operating Contract for AI Agents

## Mission

Build and maintain the Africa Aluminium recycling operations platform as a production-quality business system.

The agent must optimize for **correct business behavior, traceability, maintainability, and usable UX** — in that order.

## 1. Read before coding

Before implementing anything, read:

- `README.md`
- `idea.md`
- `wiki/00-index.md`
- Relevant domain document(s) for the feature.
- Relevant decision records under `decisions/`.

Do not start a large implementation from memory alone.

## 2. Source-of-truth hierarchy

When deciding behavior, use this order:

1. Explicit requirements from the owner/client.
2. This wiki and confirmed decisions.
3. Existing database/schema and established code behavior.
4. Industry conventions.
5. Agent inference.

If a rule is uncertain and materially affects money, inventory, or contract fulfillment, **stop and surface the uncertainty** instead of silently inventing a rule.

## 3. Never fake domain certainty

Examples of questions that require confirmation if not documented:

- Is transport allocated per purchase or per trip?
- Is fuel assigned to a vehicle, a purchase, a trip, or a contract?
- Is food an operational expense at company level or contract-level cost?
- Does a client's promised price apply per kg, per ton, per container, or per contract?
- Are overweight deliveries allowed?
- What is the official definition of “paid” versus “remaining”?
- Can purchases be edited after payment?

Use `wiki/22-open-questions.md` to track these.

## 4. Architecture rules

- Keep domain logic out of UI components.
- Validate at the API/service boundary and again at persistence boundaries where appropriate.
- Use database constraints for invariants that must never be false.
- Prefer explicit domain services over clever generic abstractions.
- Use transactions for multi-record financial/inventory changes.
- Preserve historical records; do not rewrite history to make current balances look right.
- Prefer append-only inventory/financial movement records with controlled correction mechanisms.
- Make external integrations idempotent.

## 5. Money rules

- Never use binary floating point for persisted money amounts if the stack supports exact decimal/numeric types.
- Store currency explicitly.
- Avoid hidden currency conversion.
- Do not mix currencies in one arithmetic expression without an explicit conversion rule.
- Round only according to a documented policy.
- Monetary corrections should create auditable adjustment records rather than destructive edits.

## 6. Weight rules

- Store quantity with sufficient precision for the business.
- Normalize units internally (recommended base unit: kg) while allowing UI display in kg/ton.
- Preserve original entered quantity/unit where auditability requires it.
- Never infer a weight from a count unless a documented conversion factor exists.
- Contract fulfilment must be quantity-based, not number-of-records based.

## 7. Contract rules

Every operational transaction that fulfills a client commitment should be linkable to a contract when applicable.

A contract should expose:

- target quantity
- fulfilled quantity
- remaining quantity
- financial terms
- deadline
- status
- associated costs
- delivery history

No screen should calculate fulfillment differently from another screen. Use one domain calculation service/query.

## 8. Inventory rules

Inventory is a consequence of movements, not a manually typed number.

Examples of movement types:

- `PURCHASE_RECEIPT`
- `TRANSFER_IN`
- `TRANSFER_OUT`
- `ADJUSTMENT_IN`
- `ADJUSTMENT_OUT`
- `DELIVERY_OUT`
- `RETURN_IN`

Never silently mutate inventory balances without recording why.

## 9. Audit rules

For sensitive mutations record at minimum:

- actor
- timestamp
- entity
- action
- before/after summary when useful
- reason where required
- correlation/request ID where available

Deleting financial, inventory, or contract history should normally mean “void/cancel/adjust,” not physical deletion.

## 10. UX rules

- Field users need speed.
- Managers need situational awareness.
- Accountants need exactness and traceability.
- Never make a worker navigate through management-only complexity.
- Reduce duplicate data entry.
- Use defaults intelligently, but make defaults visible and reversible.
- Never hide a meaningful financial or quantity change behind a vague “save” action.

## 11. UI style rules

The product should feel premium, calm and industrial.

Avoid:

- noisy gradients everywhere
- decorative “green” clichés
- oversized rounded cards with no information hierarchy
- dashboard clutter
- generic environmental stock imagery

Prefer:

- strong typography
- restrained borders
- generous spacing
- clear density where operational data benefits from it
- subtle motion
- a dark graphite/green brand direction where appropriate

## 12. Implementation protocol

For each task:

1. State the intended behavior in plain language.
2. Identify domain entities and invariants.
3. Identify affected data/schema.
4. Implement backend/domain logic.
5. Add tests for money/weight/status edge cases.
6. Implement UI.
7. Verify empty/loading/error/permission states.
8. Run tests/lint/type checks.
9. Update documentation if the behavior changes.
10. Summarize what changed and any open assumptions.

## 13. Definition of done

A feature is not done because the UI renders.

It is done when:

- domain behavior is correct
- persistence is correct
- edge cases are handled
- unauthorized actions are blocked
- audit behavior is defined
- tests cover critical calculations
- UI has happy/error/empty states
- data is consistent across screens
- documentation is updated

## 14. Agent communication format

When reporting progress use:

### Completed
Exact behaviors now working.

### Changed
Files/modules/schema touched.

### Verified
Tests/checks executed.

### Assumptions
Any unconfirmed business decisions.

### Next
Smallest logical next step.

Do not claim something is implemented if only the UI mock exists.
