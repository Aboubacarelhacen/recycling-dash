# 06 — Backend task 3: authentication and organisations

**Date:** 30 August 2026
**Slice:** sessions, sign-in, sign-out, the guard, and every query scoped to the
signed-in organisation
**Decision:** [0010](decisions/0010-authentication.md)
**Status:** 129 unit and integration tests, plus 6 end-to-end browser checks

---

## 1. What research changed

The 2026 guidance splits between adopting an auth framework and owning the
session path. Two findings shaped this build.

**Server actions are public POST endpoints.** Anything marked `"use server"` can
be called with curl. So identity is established inside each entry point via
`requireSession()`, never inherited from a layout that happened to run first.

**Argon2id is the current first recommendation,** with published baseline
parameters. Those exact parameters are in the code, and a test asserts the hash
string carries them, so a future edit that weakens them fails the suite.

The framework question went the other way from the popular answer: this needs
email, password, six roles that already exist, and revocation. A framework would
want to own the `users` table the audit trail is built around.

## 2. What was built

| Piece | File |
|---|---|
| Argon2id hashing and the timing equaliser | `server/auth/password.ts` |
| Session issue, resolve, revoke, revoke-all | `server/auth/session.ts` |
| Request-scoped session and the guard | `server/auth/context.ts` |
| Sign-in and sign-out actions | `server/auth/actions.ts` |
| Sign-in screen | `app/(auth)/sign-in` |
| `sessions` table, login throttling columns | migration `0001` |

Queries now take their organisation from the session rather than from the first
row in the table, so the tenant boundary is exercised on every request.

## 3. The security decisions worth checking

**The database never sees the session token,** only its SHA-256 hash. A leaked
dump cannot be replayed as a login.

**Every failure answers identically.** Unknown address, wrong password, disabled
account, locked account: one sentence. An unknown address also burns the same
Argon2 work a real check would, so timing cannot enumerate staff either.

**Five failures lock the account for fifteen minutes.** Both the failures and the
lock are recorded against the person's name.

**A deactivated person's live sessions stop working immediately,** because the
session lookup joins the user and requires them to be active.

**The cookie is `httpOnly`, `sameSite=lax`, `secure` in production,** and expires
in twelve hours.

## 4. The sign-in screen

The dashboard's language, on a split. Left: the wordmark, the form, and one line
saying that access is per person and everything is recorded against a name.
Right: a near-black panel with the lime chips, the capsule bars and the product's
one sentence. No live figures appear before sign-in.

Full states: idle, pending (the button reads "Checking"), error (one calm
sentence, never a hint about which field was wrong), and a demo credential line
that is rendered outside production only.

## 5. Tests

129 tests in four files, up from 105.

`tests/auth.test.ts` adds 24, with Next's cookie, header and redirect surfaces
stubbed so the action itself is exercised:

- hashing: argon2id format, the OWASP parameters, salting, verification, and a
  refusal rather than a throw when a user has no password
- sessions: the stored hash is not the token, resolution, absent and malformed
  tokens, expiry, revocation, revoke-all, deactivated users, the twelve-hour life
- signing in: success sets an http-only cookie and lands on the dashboard, the
  audit row names the person, the failure counter resets, a wrong password and an
  unknown address give the same answer, five failures lock the account, the lock
  refuses even the right password, a deactivated person is refused
- signing out: the session is revoked, the cookie cleared, the token dead

## 6. End-to-end

`npm run test:e2e` drives a real browser:

| Check | Result |
|---|---|
| Anonymous visit to `/contracts` redirects to `/sign-in` | pass |
| Wrong password stays on the page with one error | pass |
| Correct password lands on the contract book | pass |
| Session cookie is invisible to JavaScript | pass |
| The signed-in person is named in the chrome | pass |
| A signed-in visitor reaches `/contracts` | pass |

## 7. Signing in

```
ahmed@africaluminium.com   nouakchott2026    manager
sidi@africaluminium.com    nouakchott2026    owner
fatimetou@africaluminium.com  nouakchott2026 accountant
```

Seeded by `npm run db:seed`. The hint renders on the sign-in screen outside
production only.

## 8. Not in this task

Roles are carried on the session but not yet enforced: an accountant can still
open every screen. That is task 4. No password reset, no invitations, no
multi-factor, no session-management screen.

## 9. Next

Task 4, permissions: role rules per action, enforced in the query and action
layer rather than by hiding buttons, with the audit trail naming the actor.
