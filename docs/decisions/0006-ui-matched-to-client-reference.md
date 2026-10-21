# 0006 - Interface matched to the client's supplied reference

## Status

Superseded in full by [0007](0007-nextjs-and-the-instrument-language.md). The interface it describes no longer exists. Kept as history.

Accepted, 30 August 2026. Supersedes the visual direction in
[0005](0005-frontend-stack-and-ui-system.md); the architecture in 0005 stands
unchanged.

## Context

The client supplied two screenshots of a dashboard and asked for that design,
adapted to this product's content. The first implementation borrowed the
reference's structure but substituted a graphite and green palette derived from
the Africa Aluminium brand. That was a misreading of the brief: the client asked
for the reference design itself, not an interpretation of it.

## Decision

Match the reference's visual system, and map this product's content onto its
composition rather than inventing new layouts.

**Palette.** Lavender-grey page, white sheets, one violet accent, and a
near-black violet navigation panel. Status colours (green, amber, rose) are
reserved for contract health and appear nowhere else.

**Shell, as three stacked layers.** This is the part that carries the
reference's depth, and it is a stack, not three columns side by side:

1. The icon rail sits directly on the lavender canvas. Tiles are white and
   raised; the active section's tile is filled near-black, with a violet
   indicator on the rail's outer edge.
2. The navigation panel floats above the canvas: near-black, 300px, fully
   rounded, carrying a wide soft shadow.
3. The content sheet sits on top of the panel, lapping over its right edge by
   12px, rounded on its left corners, with a directional shadow that falls onto
   the panel. It runs to the top, right and bottom edges of the window.

Inside the panel: a header with a collapse control and a circular action, a
search row separated by a rule rather than a filled field, a violet primary item
whose icon sits in a white tile, children hanging off a stem with rounded
elbows, a divider carrying a faint violet glow, a labelled secondary group, a
dashed call to action, and a card at the foot.

**Composition.** Each element of the reference maps to something this business
already tracks:

| Reference | Here |
|---|---|
| Storage meter | Monthly container goal against collected weight |
| Recently edited files | Contracts in flight, each preview showing real fulfilment |
| Shared folders | Stock by material, first card filled to anchor the row |
| Tabs over the file table | Purchase ledger: posted, drafts, voided |
| File table with checkboxes | Purchase ledger with bulk selection |
| Selected-row action bar | Export, print, attach, open, void |

**Typography.** Plus Jakarta Sans, matching the reference's geometric sans.
Numbers use tabular figures rather than a separate monospace family, since the
reference does not use one.

**Only one violet button per screen.** It lives in the top bar. Page-level
actions are white, which follows the reference and removes the competing
primaries the earlier build had.

## What deliberately differs

- **Avatars are initials, not photographs.** These stand for real collectors and
  staff; inventing faces for them would be worse than an honest monogram, and it
  avoids a runtime dependency on an external avatar service.
- **A collection trend chart was kept.** The reference has no chart, but
  wiki/13 requires the dashboard to answer how collection is trending. It is
  styled as one of the reference's soft cards so it reads as native.
- **The panel's foot card is a live attention carousel** rather than the
  reference's illustration, cycling the contracts that need a decision.

## Consequences

- The product reads as the client expects on first sight, which matters for a
  demo to a client who chose the reference.
- Dark mode remains supported as a token swap, but the reference is a light
  design and light is the canonical presentation.
