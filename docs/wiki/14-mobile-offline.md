# Mobile & Offline-First Field Operations

## Why mobile matters

Field staff are the primary creators of purchase and expense records. The mobile workflow must be faster than the current paper/Excel process.

## Primary mobile actions

- New Purchase
- New Expense
- New Collector
- Record Weight
- View assigned contracts
- Delivery
- Sync status

## Target interaction

A routine purchase should require only the fields that are genuinely necessary and should complete in roughly 10–20 seconds for trained users.

## Offline behavior

When offline:

- allow creation of permitted transactions
- attach local IDs
- queue sync operations
- show clear `Pending sync` state
- prevent duplicate creation on retry

When online:

- sync queued events
- reconcile server IDs
- surface conflicts/errors explicitly

## Sync model

Prefer an event/request queue with idempotency over “blindly upload all local rows.”

## Conflict principle

The server remains the authority for canonical totals, permissions and state transitions.

## Photo evidence

Mobile should be able to attach a photo to:

- purchase
- expense
- delivery
- document

Compression can be applied for network efficiency while preserving enough quality for audit purposes.
