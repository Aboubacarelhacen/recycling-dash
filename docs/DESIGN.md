# Design

Values here are what ships in `frontend/src/app/globals.css`. If the two ever
disagree, the stylesheet is right and this file is stale.

## Visual Theme

A bento dashboard: a white app panel floating on a soft grey page, cards on a
light canvas, near-black type, and one electric lime carrying every highlight,
badge and active state. Chrome is circular and pill-shaped; content is card-shaped.

Light only. The scene: an operations manager at a desk in daylight, scanning the
contract book between phone calls.

## Color

**Strategy: Restrained neutrals, one loud accent.** Everything is white, near-black
or a green-tinted grey, and lime is the only saturated colour on the page.

| Token | Value | Use |
|---|---|---|
| `--page` | `oklch(0.938 0.006 135)` | the surface the app panel floats on |
| `--panel` | `oklch(1 0 0)` | the app panel and top bar |
| `--canvas` | `oklch(0.971 0.004 135)` | the surface cards sit on |
| `--card` | `oklch(1 0 0)` | cards |
| `--card-sunken` | `oklch(0.965 0.004 135)` | chips, inactive rail tiles, nested blocks |
| `--line` | `oklch(0.922 0.005 135)` | hairlines |
| `--ink` | `oklch(0.185 0.012 145)` | text, bars, the solid pill |
| `--ink-2` | `oklch(0.43 0.011 145)` | secondary text |
| `--muted` | `oklch(0.63 0.009 145)` | supporting copy |
| `--faint` | `oklch(0.755 0.007 145)` | labels and axis text |
| `--lime` | `oklch(0.925 0.185 122)` | the accent |
| `--lime-deep` | `oklch(0.875 0.2 124)` | dots and pressed states |

**Lime means good, and only good.** The active rail tile, a positive delta, the
peak bar, the operating day, a contract at or above pace. A figure that is behind
gets a neutral chip, never a lime one, so the eye can trust the colour.

Amber `oklch(0.68 0.145 70)` and red `oklch(0.58 0.185 27)` remain reserved for
contract health, always with the word beside them.

## Typography

**Geist Sans** throughout; figures use the `.fig` class for tabular, slashed-zero
numerals with `-0.03em` tracking. Numbers are the loudest thing on the page.

| Step | Size / line | Weight | Use |
|---|---|---|---|
| Page title | 38 / 40 | 600 | one per screen, over a grey overline |
| Card figure | 30 / 36 | 600 | the headline number in a card |
| Figure | 22-26 | 600 | secondary figures |
| Section | 19 / 24 | 600 | section heads |
| Card head | 12.5 | 600 | beside the lime tile |
| Body | 13 / 19 | 400 | default |
| Chip | 11-12 | 500 | pills and badges |
| Label | 10 | 600, `0.07em`, uppercase | axis text, captions |

## Layout

- The app is one white panel with a 28 px radius, floating on the grey page with
  a wide soft shadow. It fills the viewport height and scrolls inside.
- **A circular icon rail**, 84 px, is the primary navigation. The current section
  is a filled lime tile; sections with something waiting carry a small dot.
- The top bar is a row of pill clusters: tools, identity, greeting, date.
- Content is a **12-column bento grid** with 12 px gutters. Cards use a 22 px
  radius and a soft shadow, never a border.
- Nested blocks inside a card use a 16 px radius on the sunken grey.

## Components

Circular icon buttons at 32 and 40 px. Pills for everything else: the solid black
pill is the only primary action on a screen, chips are grey, and the lime delta
badge carries an arrow and a percentage.

Tables live inside a card with hairline row rules and no outer border. Loading
takes the shape of the content. Empty states say what is empty, why it matters,
and what to do next. Screens that are not built say so.

## Motion

150 to 200 ms on `cubic-bezier(0.22, 1, 0.36, 1)`. Motion carries state change
only: hover tint, pill press, bar fill. Nothing loops, nothing choreographs a
page load, and all of it collapses under `prefers-reduced-motion`.
