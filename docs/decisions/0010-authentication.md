# 0010 - Sessions in the database, Argon2id for passwords

## Status

Accepted, 30 August 2026.

## Context

`docs/wiki/15` requires roles, a per-person audit trail and server-side
authority. `docs/architecture/decision-principles.md` asks for minimal
dependencies. The 2026 field is split between adopting an auth framework and
owning the session path.

## Decision

**Own it.** The requirement is one organisation, email and password, six roles
that already exist in the schema, and a session that can be revoked. A framework
would want to own the `users` table this product designed around the audit
trail, and would add a dependency to solve a problem this narrow. Revisit if
single sign-on, social login or multi-factor becomes a requirement.

**Argon2id, at the OWASP baseline.** `m=19456 KiB, t=2, p=1`, roughly 100 ms per
verification. Argon2id is the first algorithm in the current OWASP password
storage guidance; the baseline parameters are the ones that guidance publishes,
not a number picked for feel.

**Server-side sessions, not JWTs.** The cookie carries a 32-byte random token;
the database stores only its SHA-256 hash, so a database leak cannot be replayed
as a login. Sessions are rows because an operations tool has to revoke access the
moment somebody leaves, which a stateless token cannot do. Twelve-hour lifetime:
a working day plus a night.

**The cookie is `httpOnly`, `sameSite=lax`, and `secure` in production.** Next
compares Origin against Host on server actions, which handles the common CSRF
case; `sameSite` is the belt to that brace.

**Every failure returns one sentence.** Unknown address, wrong password,
disabled account and locked account all answer "That email and password do not
match an account." An unknown address also spends the same Argon2 work a real
verification would, so response time cannot be used to enumerate staff.

**Five failures lock the account for fifteen minutes,** and both the failures and
the lock are written to the audit trail against the person's name.

**Every page and every action calls `requireSession()` for itself.** A server
action is a public POST endpoint; it cannot assume a layout already checked.

## Consequences

- Sign-in costs roughly 100 ms of CPU by design. That is the point.
- Sessions live in Postgres, so revocation is a single update and the sign-in
  path needs the database available. Acceptable: nothing in this product works
  without it.
- `OPERATING_TODAY` is still a constant, so the demo stays reproducible. The
  organisation's timezone now exists on the record for when that changes.
