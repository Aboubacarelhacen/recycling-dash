# Permissions & Audit

## Roles

### Owner
Full organizational access.

### Manager
Operational and financial management, excluding certain owner-only administration actions.

### Accountant
Financial records, invoices, payments, reports.

### Field Worker
Purchases, collectors, weights, eligible expenses.

### Driver
Assigned trips/deliveries.

### Viewer
Read-only.

## Permission dimensions

Permissions may evolve from simple role-level to:

- organization
- location
- module
- field
- action

**As built:** role plus action, in `frontend/src/server/auth/permissions.ts`.
Location and field scoping are not implemented and are not needed yet.

## The matrix as built

The three examples below are encoded literally, because a generic
"finance role gets finance, field role gets operations" model gets them wrong.

- field workers may create purchases but **not** void or reweigh a posted one
- accountants may issue invoices but **not** alter a purchase weight
- managers may approve expenses, but **not** administer the team or settings

| Permission | Owner | Manager | Accountant | Field worker | Driver | Viewer |
|---|:--:|:--:|:--:|:--:|:--:|:--:|
| contracts.view | y | y | y | y | . | y |
| contracts.manage | y | y | . | . | . | . |
| purchases.view | y | y | y | y | . | y |
| purchases.create / post | y | y | . | y | . | . |
| purchases.void | y | y | . | . | . | . |
| purchases.edit_weight | y | y | . | . | . | . |
| expenses.view | y | y | y | y | . | y |
| expenses.create | y | y | y | y | . | . |
| expenses.approve | y | y | y | . | . | . |
| inventory.view | y | y | y | y | y | y |
| inventory.adjust | y | y | . | . | . | . |
| deliveries.view | y | y | y | y | y | y |
| deliveries.manage | y | y | . | . | y | . |
| collectors.view | y | y | y | y | . | y |
| collectors.manage | y | y | . | y | . | . |
| invoices.view | y | y | y | . | . | y |
| invoices.issue | y | y | y | . | . | . |
| invoices.void | y | . | y | . | . | . |
| payments.view | y | y | y | . | . | y |
| payments.record | y | y | y | . | . | . |
| reports / profitability.view | y | y | y | . | . | y |
| team.view / settings.view | y | y | . (settings only) | . | . | . |
| team.manage / settings.manage | y | . | . | . | . | . |
| audit.view | y | y | y | . | . | . |

The table is asserted role by role in `tests/permissions.test.ts`. If the code
and this table ever disagree, the test fails.

## Where a rule is enforced

Three places, in this order of authority:

1. **The screen or action** calls `sessionWith(permission)` and refuses on its
   own account. This is the rule.
2. **Navigation** hides what a person cannot reach. This is courtesy, not
   security: typing the URL still meets the refusal.
3. **Search** indexes only what the person may open.

A refusal names the person, their role and the missing right, and offers a
screen they can actually open. It never bounces them somewhere silently.

## Audit events

**As built:** sign-in, failed sign-in, account lockout and sign-out; purchase
recorded, corrected and voided; expense recorded and voided. Each is written to
`audit_logs` against the person's name, and a correction carries the reason plus
the before and after values.

Record important actions such as:

- purchase created
- purchase voided
- weight changed
- contract edited
- expense approved
- inventory adjusted
- invoice issued
- payment recorded
- user permission changed

## Human-readable audit entry

Example:

```text
30 Aug 2026 10:42
Ahmed created Purchase P-0182
420 kg aluminum
14,700 MRU
Contract ALU-014
```

If edited:

```text
30 Aug 2026 11:04
Manager changed Purchase P-0182
Weight: 420 kg → 430 kg
Reason: corrected scale reading
```
