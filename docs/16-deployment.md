# 16 — Deployment

Date: 31 August 2026
Slice: everything needed to run this as a real product, on one machine.

## 1. There is no separate backend

Worth stating plainly, because it is the first thing anyone asks. Next.js is
the backend here:

| Concern | Where it lives |
|---|---|
| Pages | Server components querying Postgres directly |
| Writes | Server actions — `recordEntry`, `amendPurchase`, `issueInvoice`, `recordPayment` |
| Auth | Argon2id hashing, database-backed sessions, httpOnly cookies |
| Excel export | A route handler at `/api/exports/ledger` |
| Health | A route handler at `/api/health` |
| Schema | Drizzle migrations in `drizzle/` |

The only companion process is **PostgreSQL**. No Redis, no object storage, no
worker: nothing is uploaded, and the spreadsheet is generated in memory and
streamed. Adding a separate API service would only insert a network hop between
the page and its own queries.

## 2. Why one machine

Every screen in this application is server-rendered out of Postgres. A page is
a handful of queries, so the round trip between the server and the database
happens on every click, several times.

That single fact decides the architecture. Put the application in one region
and the database in another and 60 ms of network becomes most of a second on
screen, for a manager on a connection that is not fast to begin with. Colocating
them turns those queries into localhost.

So: **one small VPS in Europe** — Paris or Marseille, the shortest path to
Nouakchott — running the application, Postgres, and a reverse proxy beside each
other. Around €5 a month. `deploy/docker-compose.yml` is that stack.

The alternative worth taking seriously is a managed platform (Railway, Fly.io)
with the application and the database pinned to **the same region**. Slightly
more money, considerably less to look after. The same image runs on both.

Vercel is a poor fit here despite being the obvious choice for Next.js: the
functions and any managed Postgres end up in different places, which is the one
thing this application cannot afford.

## 3. What was built

`Dockerfile` — three stages, so the runtime image carries no toolchain and no
development dependencies. It runs as a non-root user, and its command is
`node scripts/migrate.mjs && node server.js`: **the schema is never behind the
code that expects it**, because the server does not start until the migrations
have applied.

`next.config.ts` — `output: "standalone"` for the image, and
`serverExternalPackages: ["@node-rs/argon2"]` because argon2 is a native binary
and bundling it breaks the `.node` require.

One trap worth naming, because it fails at deploy time rather than at build
time: the password hasher is a **native binary**, and the copy traced on a
laptop is the macOS build. `npm ci` inside the image picks the Linux musl build
instead, but only because `package-lock.json` records every platform variant.
A test asserts those variants are still in the lockfile. It is also why the
instructions below build **on the server** rather than pushing an image from a
Mac: an Apple Silicon laptop produces arm64, and most VPS hosts are amd64.

`scripts/migrate.mjs` — plain JavaScript, because it runs inside the production
image where there is no TypeScript. It uses only `postgres` and `drizzle-orm`,
which the server needs anyway.

`scripts/create-user.mjs` — how a real account is made. The password is read
from stdin, so it never reaches shell history or the process list, and it is
hashed with the same Argon2id parameters the sign-in path verifies against.

`deploy/docker-compose.yml` — Postgres, the application, Caddy, and a backup
loop. **Only Caddy is bound to a public port**; Postgres and the application are
reachable across the compose network and from nowhere else. The application
waits for the database to pass its health check before starting.

`deploy/docker-compose.yml` also defines a `tools` service, built from the
build stage and held behind a profile so `up` never starts it. It is where
one-off jobs that need the development toolchain run, such as the historical
import.

`deploy/Caddyfile` — automatic HTTPS. Caddy obtains and renews the certificate
itself, so there is no cron job and no renewal to forget. HSTS, `nosniff`,
`DENY` framing.

`/api/health` — touches the database, because a server that is up but cannot
reach Postgres is not serving anybody. It reports nothing about the business:
the endpoint is public.

## 4. Two ways to lose the company's data, both closed

**Starting without a database.** `src/server/db/index.ts` fell back to a
localhost connection string when `DATABASE_URL` was unset. In development that
is convenient; in production it means the server starts, connects to nothing in
particular, and fails in a way that looks like an application bug rather than a
missing secret. It now refuses to start.

**Seeding over a real book.** `npm run db:seed` truncates the tables it owns and
gives every account one shared demo password. It now refuses to run when
`NODE_ENV=production`.

The backup service takes a nightly `pg_dump`, keeps a month, and prints
`BACKUP FAILED` loudly rather than failing quietly. The purchase ledger is the
product; losing it is the only unrecoverable failure here.

## 5. Deploying it

```bash
# On the server, with a DNS A record already pointing at it.
git clone <repo> && cd <repo>/deploy
cp .env.example .env
openssl rand -base64 32          # paste into POSTGRES_PASSWORD
$EDITOR .env                     # and set SITE_DOMAIN

docker compose up -d --build     # migrations run before the server starts

# Create the first real account. Not the demo seed.
docker compose exec app node scripts/create-user.mjs \
  "Ahmed Ould Cheikh" ahmed@africaluminium.com manager

# Then load the historical workbook, once. This one runs through the `tools`
# service: the import is TypeScript, and the runtime image deliberately carries
# neither the source nor tsx.
docker compose --profile tools run --rm tools npm run import:workbook -- --apply
```

Updating is `git pull && docker compose up -d --build`. Migrations apply on
start, so there is no separate release step to remember.

## 6. What was verified

**370 tests**, 15 files. 13 are new in `tests/deployment.test.ts`, and they
assert the things that would be worst to get quietly wrong: a production server
refuses to start without a database, the demo seed refuses to touch production,
the image runs migrations before the server and does not run as root, only the
reverse proxy is published, the backup expires old dumps, and the environment
template ships no password.

## 7. Still not done

The six stub routes (deliveries, expenses, payments, profitability, settings,
team), the AHMEDOU tranche block, and the open questions in `docs/wiki/22`.
None of them block putting this in the manager's hands.

There is no CI, no error monitoring, and no staging environment. For a
single-operator product those are reasonable to add later; for a second customer
they are not.
