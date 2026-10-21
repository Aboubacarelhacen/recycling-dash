# Test Strategy

## Unit tests

Focus on:

- quantity normalization
- purchase totals
- contract fulfillment
- cost/kg
- margin
- health status
- rounding

## Integration tests

Focus on:

- purchase → inventory movement
- purchase → contract fulfillment
- delivery → inventory movement
- invoice → canonical source data
- payment → invoice status
- audit events

## Permission tests

Verify each role's ability to:

- create
- read
- edit
- approve
- void
- export

## Mobile sync tests

Test:

- offline create
- retry
- duplicate request
- partial sync failure
- attachment upload failure
- conflict handling

## Import tests

Use representative legacy Excel rows:

- valid purchase
- missing supplier
- expense-only row
- inconsistent date format
- inconsistent historical total
- duplicate candidate

## UI tests

For critical screens verify:

- loading
- empty
- success
- validation error
- server error
- permission denied
- mobile layout
