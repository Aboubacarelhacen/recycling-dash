# Contracts

## Contract record

Minimum recommended fields:

- id
- organization_id
- client_id
- contract_number
- title/description
- material_id
- target_quantity_kg
- committed_unit_price or contract_value
- currency
- start_date
- deadline
- destination
- status
- terms/notes
- source_document_id
- created_by / timestamps

If a contract can contain multiple materials, use a header + contract items model rather than adding material fields directly to the contract forever.

## Contract workspace

The workspace is the central management screen.

### Header

- Client
- Contract number
- status
- deadline
- contract value

### KPI strip

- target kg
- fulfilled kg
- remaining kg
- fulfillment %
- days remaining

### Economics

- material purchase cost
- operational expenses
- total cost
- cost/kg
- projected margin
- actual margin (when completed)

### Activity

- purchases
- expenses
- inventory movements
- deliveries
- invoices
- payments

### Risk

Explain why the contract is `ON_TRACK`, `ATTENTION`, or `AT_RISK`.

## Contract integrity

A purchase linked to a contract should increase contract fulfillment only when that purchase is in the canonical posted state and the quantity is valid.

A delivery should not reduce contract fulfillment below zero. Delivered quantity and sourced/fulfilled quantity may be separate metrics; do not assume they are identical without a confirmed business definition.

## Future enhancements

- contract templates
- approval workflow
- uploaded contract parsing
- price versioning
- client-specific terms
- automatic document extraction
- SLA/deadline alerts
