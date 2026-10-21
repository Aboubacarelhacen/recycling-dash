# 03 — The bento dashboard

**Date:** 30 August 2026
**Slice:** dashboard and shell rebuilt on a supplied reference system
**Supersedes:** the visual language in [02](02-nextjs-rebuild.md); its
architecture stands
**Status:** demonstrable, read-only against the deterministic dataset

---

## 1. What changed and why

The instrument interface from report 02 was structurally right and the client
did not want it. They supplied a health-records dashboard and asked for that
layout, system and colours, carrying this product's content. This report covers
that rebuild.

Nothing below the interface moved. Next.js, server components, the selectors and
`lib/calc.ts` are untouched, which is the point of having kept them separate.

## 2. The system

One white app panel floating on a grey page. A circular icon rail is the primary
navigation, the top bar is a row of pill clusters, and content is a 12-column
bento grid of soft-shadowed cards on a light canvas. Full values in
[DESIGN.md](DESIGN.md); the decision is [0008](decisions/0008-bento-dashboard.md).

**Lime means good, and only good.** Active navigation, a positive delta, the peak
bar, the operating day. A contract behind its pace gets a neutral chip instead,
so the colour keeps meaning something. Amber and red stay reserved for contract
health and always carry a word.

![The dashboard](screenshots/dashboard.png)

## 3. How the reference maps onto this business

| Reference card | Here |
|---|---|
| Wellness, with KCAL total | Collection: kilograms bought, average price, purchases posted |
| Analytics, with the bar chart | Daily collection over 30 days, peak and operating day in lime |
| Progress, with the checkbox list | The active contract book, each row with its fulfilment |
| "Get more exposure" feature card | The contract that needs a decision, with its pace and a way in |
| "Take a breath now" | The draft purchase waiting on its second weighing |
| Body-scan card with weekday bars | Today's collection beside the last seven days |
| Workout card | Inventory: available against on hand |
| Oxygen and bpm tiles | Fulfilment of the active book, and cost per kilogram |

Two slots in the reference hold lifestyle photography. Inventing images here
would be filler, so those became lime blocks carrying the figure that matters.

![The contract workspace](screenshots/contract-workspace.png)

## 4. Verification

Typecheck, ESLint and the production build are clean. Every screen was rendered
and read back at 1440 px and at a true 390 px phone viewport.

Defects found by looking, and fixed:

| Found | Fix |
|---|---|
| Bars rendered as ellipses: a stretched SVG viewBox distorts rounded rects | drawn as HTML capsules, which also made them crisper |
| One movement figure reused on four unrelated badges | each badge computes its own baseline: month over month, cadence, week over week, margin share |
| A stock ratio wore a trend badge, implying movement it did not have | plain chip reading "55 % free" |
| Lime badges on contracts that were behind pace, so the accent stopped meaning good | behind-pace contracts take a neutral chip |
| List screens still referenced the previous token names, so their borders and surfaces silently vanished | retokenised, and tables now render inside cards |
| List and workspace screens ran edge to edge against the panel | page padding matched to the dashboard grid |
| The average price printed with a decimal point while every other figure used the French comma | one convention everywhere |

## 5. Deliberately not finished

- Read-only. No store, no mutation, no server actions. The purchase form is the
  next slice.
- Light only.
- Eight routes render an honest placeholder naming what they wait on.
- `lib/calc.ts` still needs porting to a server when one exists.

## 6. Next

The fast purchase entry form: collector, material, weight, unit price, posted in
seconds, with the correction trail behind it.
