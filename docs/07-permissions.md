# 07 — Backend task 4: permissions

**Date:** 30 August 2026
**Slice:** the role matrix, enforced on the server, with a refusal that teaches
**Status:** 151 tests and 10 browser checks passing

---

## 1. What was built

`docs/wiki/15` describes roles in prose. This task turned that prose into a
matrix the server enforces, in `server/auth/permissions.ts`: 29 permissions
across six roles, and one function, `can(role, permission)`, that is the only
place a role is ever compared to an action.

Three rules from the wiki are encoded literally, because they are exactly what a
generic "finance role gets finance, field role gets operations" model gets wrong:

- a **field worker** may create and post a purchase but may not void or reweigh
  a posted one
- an **accountant** may issue and void invoices but may not alter a purchase
  weight
- a **manager** runs the operation but does not administer the team or settings

## 2. Where a rule is enforced

Three places, in this order of authority:

1. **The screen** calls `sessionWith(permission)` and refuses on its own
   account. This is the rule.
2. **Navigation** hides what a person cannot reach. Courtesy, not security:
   typing the URL still meets the refusal.
3. **Search** indexes only what the person may open, so the command palette
   cannot leak a contract number to someone who may not see contracts.

`requirePermission()` exists for the write path, where a refusal is an error
rather than a screen. Task 5 uses it.

## 3. The refusal

A driver who opens `/invoices` is not bounced somewhere silent. The screen names
them, names their role, says what the screen needed in plain words, and offers a
screen they can actually open:

> **Cheikhna, your role cannot see invoices.**
> Access is set per person by the owner. If this screen is part of your work,
> ask them to change your role rather than sharing another sign-in: every
> posting and correction is recorded against the name that made it.

A driver also lands on `/deliveries` after signing in rather than on the contract
book, because the contract book would be a refusal.

## 4. Tests

151 unit and integration tests, up from 129. The 22 new ones assert the matrix
role by role, in full, rather than spot-checking it: a permission accidentally
added to a role is the kind of change that passes review and fails an audit.

The viewer test is the sharpest: it enumerates every non-view permission in the
catalogue and asserts the viewer holds none of them, so a new write permission
added later cannot silently land in the read-only role.

Ten browser checks now cover roles as well as sign-in:

| Check | Result |
|---|---|
| A driver lands on deliveries, not the contract book | pass |
| A driver asking for `/invoices` is refused by name | pass |
| A driver's rail hides contracts, purchases and invoices | pass |
| A driver typing `/contracts` directly is still refused | pass |

## 5. Two defects the browser found

**The refusal offered a door that was also locked.** The button read "Back to
the contract book" for a driver who cannot open the contract book. It now offers
whichever screen that person can actually reach.

**The rail still showed the quick-add button** to roles without
`purchases.create`.

Both were invisible to the unit tests and obvious in a screenshot.

## 6. Not in this task

Permissions gate screens and navigation. They do not yet gate writes, because
there are no writes beyond signing in. `requirePermission()` is in place for
task 5, and the field-worker and accountant restrictions above are the first two
rules it will carry.

## 7. Knowledge reconciled

This task also brought the wiki back in line with what exists:

| Page | Change |
|---|---|
| `wiki/15` | The matrix as built, where each rule is enforced, and which audit events exist today |
| `wiki/16` | The stale navigation tree and visual direction replaced by what ships, pointing at `DESIGN.md` |
| `wiki/17` | No separate API service: the Next.js app is the server. Multi-tenancy and the security baseline as built |
| `wiki/22` | Ten open questions now carry a provisional answer, each with where it lives |
| `wiki/24` | Describes the real schema: sessions, invoice lines, collector aliases, import provenance, and the login columns |
| `wiki/25` | Gate status, and an honest note that the real build order put the interface first |

## 8. Next

Task 5, the purchase write path: a transactional posting that creates the
purchase, its lines, the inventory movement and the audit entry together or not
at all, with idempotency for field retries and the permission rules above.
