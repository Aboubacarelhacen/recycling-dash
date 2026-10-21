# High-Value Acceptance Criteria

## Contract fulfillment

- When a posted purchase of 500 kg is linked to an active contract, the contract's fulfilled quantity increases by exactly 500 kg.
- Replaying the same mobile request does not add the 500 kg twice.
- Fulfillment is consistent across dashboard, contract page and reports.

## Purchase calculation

- Purchase subtotal is calculated server-side from quantity × unit price.
- Total includes only explicitly selected/entered cost components according to the configured rule.
- Client-supplied totals cannot override server totals.

## Inventory

- Posting a valid purchase creates an inbound movement.
- Voiding a purchase creates the correct reversal/adjustment according to the chosen accounting workflow.
- Inventory movement is traceable to its source transaction.

## Invoice

- Issued invoice number is unique.
- Invoice total is generated from canonical source records.
- Editing a posted purchase does not silently rewrite an already-issued invoice.

## Permissions

- A field worker cannot void another user's posted purchase unless policy explicitly permits it.
- Viewer users cannot mutate transactions.

## Audit

- Sensitive changes create audit events containing actor, timestamp and target.
