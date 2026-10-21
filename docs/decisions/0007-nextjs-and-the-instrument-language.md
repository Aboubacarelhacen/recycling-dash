# 0007 - Next.js, and the instrument language

## Status

Accepted, 30 August 2026. The visual language below is **superseded by
[0008](0008-bento-dashboard.md)**; the architecture decisions stand. Supersedes [0005](0005-frontend-stack-and-ui-system.md)
(stack) and [0006](0006-ui-matched-to-client-reference.md) (visual direction)
in full.

## Context

Two earlier interfaces were rejected by the client. The first borrowed a
supplied reference's structure but substituted its own palette. The second
matched that reference closely. Neither was judged premium, and the client's
verdict on the second was that colour, typography, layout and finish all fell
short. The client then asked for Next.js, and for the design to be led by the
design skills rather than by the UI guidance held in this repository.

A structured interview established the direction: **Linear and Raycast
precision, light theme, dense**, with the theme decided by a scene rather than a
category, an operations manager at a desk in daylight.

## Decision

**Framework: Next.js with the App Router.** Pages are server components reading
the domain layer directly, so the dataset stays out of the client bundle and the
only client islands are the command palette and the mobile navigation sheet.
Filters and tabs are URL search params, which makes every view shareable and
server-rendered.

**The domain layer survived the rewrite.** `lib/calc.ts`, `lib/format.ts` and
`domain/*` were carried across unchanged. They are the canonical business logic,
not UI, and rebuilding them because a visual direction failed would have been
throwing away the correctness work with the paint.

**Visual language: a precision instrument in a bright room.** Warm paper, cold
ink, hairlines. Restrained colour, one accent under 10 % of the surface, and no
cards anywhere. Full values in [DESIGN.md](../DESIGN.md).

**Green is not the accent.** "Recycling company" makes every model reach for
green, and that reflex is a large part of why the earlier work read as generic.
The accent is an archival blue, used only for selection, focus and progress.

**Healthy carries no colour.** On track is uncoloured; amber and red are earned
by exception. Status always ships with a word, never colour alone, which also
settles the colour-blindness case.

**The dashboard is a book, not a tile wall.** The hero-metric row of KPI cards
is the category cliché. The period figures are set as a statement divided by
hairlines, and the contract table itself is the dashboard.

## Consequences

- Server rendering means the interactive surface is small and fast, but any
  future mutation work needs server actions or an API, not client state.
- Light only. A dark theme would be a token swap, but it is not built, and the
  scene that chose light did not ask for one.
- The two earlier decision records remain in the log as history. They describe
  interfaces that no longer exist.
