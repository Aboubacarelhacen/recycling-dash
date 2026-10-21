# UX & Design System

## Product character

Premium, industrial, trustworthy, efficient.

The product should feel like software a serious operator trusts with money and inventory.

## Visual direction

- dark graphite / off-black surfaces in the marketing experience when appropriate
- white or warm-light operational surfaces for readability
- controlled green accent derived from the Africa Aluminium brand
- high-quality sans serif typography
- fine dividers
- subtle depth
- minimal decorative noise

## Dashboard density

Operational tables should favor information density, while management cards should favor immediate comprehension.

## Navigation

```text
AFRICA ALUMINIUM

Overview

OPERATIONS
  Contracts
  Purchases
  Inventory
  Deliveries
  Collectors
  Vehicles

FINANCE
  Expenses
  Invoices
  Payments

ANALYTICS
  Profitability
  Reports

MANAGEMENT
  Team
  Settings
```

## Main screens

### Dashboard
Cockpit.

### Contract workspace
Operational command center for a single client commitment.

### Purchases
Fast-entry ledger.

### Collectors
Supplier history and relationship context.

### Expenses
Controlled cost capture.

### Inventory
Live stock position.

### Deliveries
Execution + proof.

### Invoices
Professional document generation and receivables.

## Micro-interaction philosophy

Animations should communicate state/change, not decorate. Use smooth transitions for progress updates, row insertion, navigation and document generation feedback.

## Empty states

Each empty state should answer:

- what is empty
- why it matters
- what action should happen next

Example:

> No active contracts yet. Create your first client commitment to start tracking fulfilment and margin.

## Error states

Errors should be actionable:

Bad:

> Something went wrong.

Better:

> Purchase could not be saved because the weight is missing. Check the scale entry and try again.

## Implemented system

The visual system that ships is documented in `docs/DESIGN.md`, which holds the
token values, the type scale and the layout rules. It is the authority; this page
is the intent behind it.

The direction changed twice during the build and settled on the client's
supplied reference. The record is `docs/decisions/0008-bento-dashboard.md`,
superseding 0006 and the visual half of 0007.

**In one paragraph:** a white app panel filling the viewport, a circular icon
rail as the primary navigation, a top bar of pill clusters, and content as a
twelve-column bento grid of soft-shadowed cards on a light canvas. Near-black
type, one electric lime accent, and status colour reserved for contract health.

### Navigation as built

The navigation tree earlier on this page was written before the interface
existed and no longer matches it. What ships:

```text
Rail (primary, always visible)
  Book         the contract book
  Ctr          contracts
  Buy          purchases
  Stk          inventory
  Del          deliveries
  Col          collectors
  Inv          invoices

Menu (secondary, behind the top-right control)
  Expenses  Payments  Profitability  Reports  Team  Settings  Sign out
```

Every entry declares the permission it needs, and a person only sees what they
may open (`docs/wiki/15`).

### Rules that survived every redesign

- Colour means something. Lime means good, amber means attention, red means at
  risk, and a healthy contract carries no colour at all.
- Numbers are tabular and are the loudest thing on a card.
- Empty states say what is empty, why it matters and what to do next.
- A screen a person may not open explains itself rather than redirecting.
