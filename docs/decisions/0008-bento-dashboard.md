# 0008 - The bento dashboard

## Status

Accepted, 30 August 2026. Supersedes the visual half of
[0007](0007-nextjs-and-the-instrument-language.md); its architecture decisions
(Next.js, server components, the canonical calculation layer) stand unchanged.

## Context

The near-monochrome instrument interface from 0007 was correct in structure but
the client did not want it. They supplied a health-records dashboard as the
reference and asked for that layout, system and colour, carrying this product's
content.

## Decision

Adopt the reference's system wholesale, and map our content onto it.

**One white app panel** floating on a grey page, with a circular icon rail as the
primary navigation and a top bar of pill clusters. Content is a 12-column bento
grid of soft-shadowed cards on a light canvas.

**Lime is the accent, and it means good.** Active navigation, positive deltas,
the peak bar and the operating day. A contract behind its pace gets a neutral
chip rather than a lime one, so the colour keeps its meaning. Amber and red stay
reserved for contract health and always ship with a word.

**Numbers are the loudest thing on the page.** Card figures at 30 px in tabular
numerals, each with a delta badge that carries a real comparison.

**Every delta is computed against its own baseline.** Collection compares the
last 30 days to the 30 before it, cadence compares active buying days, the week
strip compares week over week. Reusing one movement across unrelated figures
would have been decoration dressed as data.

## What differs from the reference

- **No photography.** The reference uses lifestyle images; inventing them here
  would be filler. Those slots carry lime blocks with the figure that matters.
- **The bar chart is HTML, not SVG.** A stretched viewBox turns rounded rects
  into ellipses, and the capsule bar is the whole character of the chart.
- **Health colour survives.** Amber and red are load-bearing in this domain, so
  they remain alongside the lime, unlike the reference which is lime-only.

## Consequences

- The visual system is now maximal where 0007 was minimal. Density on list
  screens dropped slightly to match: tables sit in cards with more row padding.
- Every screen shares the vocabulary, so a new screen is card plus chips plus
  figures rather than a fresh layout decision.
