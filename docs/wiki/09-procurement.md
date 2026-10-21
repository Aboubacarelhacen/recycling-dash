# Procurement & Collector Management

## Purchase entry

A field worker should be able to enter:

- date/time
- collector/supplier
- material
- weight
- unit price
- calculated subtotal
- transport/fuel/other cost if applicable
- contract linkage
- vehicle/trip linkage if applicable
- notes
- photo/evidence

The user should not have to manually calculate subtotal or total.

## Collector profile

Show:

- identity/contact
- total quantity purchased
- total amount paid
- transaction count
- average unit price
- recent activity
- payment history
- notes

## Supplier naming

The Excel contains variations/blank names. The software should have canonical supplier records and optional aliases, rather than trusting free text forever.

## Duplicate prevention

The backend should guard against accidental duplicate submissions, especially from mobile retries.

Possible idempotency key:

`client_generated_transaction_id`

## Pricing history

Supplier pricing may vary. Never overwrite historical unit prices when today's price changes.

Use transaction-level price snapshots and optionally maintain current price lists separately.

## Future supplier portal

Potential later features:

- statement
- purchase history
- balances
- pickup request
- payment status
- communication

Do not add this to MVP unless the client asks for it.
