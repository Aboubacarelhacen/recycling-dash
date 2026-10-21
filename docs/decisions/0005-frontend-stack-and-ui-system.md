# 0005 - Frontend stack and UI system

## Status

Superseded in full by [0007](0007-nextjs-and-the-instrument-language.md): the stack is now Next.js and the visual direction has changed. Kept as history.

Accepted, 30 August 2026. The visual direction below (two-tone shell, single
green accent) is **superseded by [0006](0006-ui-matched-to-client-reference.md)**,
which matches the client's supplied reference. Everything else here stands.

## Context

The platform needed an operational UI before any backend existed, and the client
supplied dashboard references (dark command rail plus light content canvas,
rounded panels, dense tables) as the visual direction.

## Decision

**Stack.** React 19 + TypeScript + Vite + Tailwind v4, React Router, Motion for
the small amount of state-change animation, Phosphor for icons, Geist and Geist
Mono self-hosted. No component library: the product needs dense operational
tables more than it needs a generic component kit, and owning the primitives
keeps the visual language consistent.

**Canonical calculations live in the frontend too.** `src/lib/calc.ts` implements
wiki/07 exactly, with the vocabulary from CLAUDE.md. When the backend is built,
the same functions are ported rather than reinvented, and the frontend switches
from computing them to reading them. No component performs arithmetic.

**Two-tone shell.** Navigation surfaces are graphite in both themes; the content
canvas follows the user's theme. Command chrome stays constant while the working
surface adapts, which is what makes the dense tables readable in either mode.

**One accent.** A single controlled green derived from the Africa Aluminium
brand carries identity and primary actions. Amber and rust are reserved for
contract health and are never used decoratively, so a colour on screen always
means a state.

**Health is derived and inspectable.** `contractHealth` returns a status plus
written reasons, and the workspace prints them. Thresholds sit in one exported
object marked provisional, pending client validation (wiki/22).

## Consequences

- The frontend can be demonstrated and reviewed before the API exists.
- Porting `calc.ts` to the server is a required task, not an optional one; if the
  two ever diverge the product loses its single definition of margin.
- Screens not yet built state their build gate rather than faking content.
