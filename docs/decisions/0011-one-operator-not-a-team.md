# 0011 - One operator, not a team

## Status

Accepted, 30 August 2026. Narrows the scope assumed by
[0010](0010-authentication.md) and the permission work in `docs/07-permissions.md`.

## Context

The product was designed against `docs/wiki/15`, which describes six roles:
owner, manager, accountant, field worker, driver and viewer. The client has since
been explicit: **only the manager signs in.** Drivers and field workers will not
use the system. The company is small, in Nouakchott, and the manager is the
person who takes the collection requests, talks to the collectors, and records
what came in each day.

The workbook agrees. It is one person's ledger: rows entered in date order,
suppliers recorded only sometimes, expenses mixed into the same list, months
closed with a TOTAL row.

## Decision

**Design for one operator. Keep the role model.**

Concretely:

- **Only the people who operate the system have a password.** The manager and the
  owner can sign in. The accountant, the field worker and the driver remain as
  person records, because historical rows are attributed to them and a delivery
  names its driver, but they carry no password hash and therefore cannot sign in
  at all. A record without a password fails verification like any other bad
  credential.
- **The permission matrix stays.** It is written, tested and costs nothing to
  keep, and it is what stops a future viewer account from seeing everything. What
  changes is that the product stops being *shaped* around a team: no team
  screens, no assignment flows, no per-role dashboards.
- **The field is a phone call, not a login.** The mobile field-worker app in
  `docs/wiki/14` is not the next thing to build, and possibly not a thing to
  build at all. The manager records the day.

## What this does not change

The audit trail still records who did what, because the owner reads it and
because a correction has to be attributable. Multi-tenancy stays: it costs one
column and it is what allows a second company later.

## Consequences

- Screens are built for the manager's day first. Anything that only makes sense
  with several people on the system waits until there are several people.
- If the company later hires a second recorder, the matrix already exists and the
  change is creating an account, not rebuilding authorisation.
