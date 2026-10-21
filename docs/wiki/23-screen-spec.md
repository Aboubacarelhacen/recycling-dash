# Screen-by-Screen Product Specification

## 1. Dashboard

### Purpose
Management cockpit.

### Above the fold
- active contracts
- total contract value
- collected quantity
- current spend
- available inventory
- projected margin

### Main content
- active contract health table
- today activity
- recent purchases
- expense breakdown
- collection trend

### Primary action
`Create Purchase` for operational users; `Create Contract` for managers.

## 2. Contracts list

Columns:

- contract number
- client
- material
- target
- fulfilled
- remaining
- deadline
- health
- projected margin
- status

Filters:

- status
- client
- material
- deadline
- health

## 3. Contract workspace

Sections:

- header/terms
- fulfillment KPI strip
- quantity progress
- financial summary
- risk explanation
- purchases
- expenses
- deliveries
- invoices
- audit/activity

## 4. Purchase form

Required/minimal fields:

- collector
- material
- quantity
- unit price
- currency
- contract when applicable

Optional based on workflow:

- transport
- fuel
- other cost
- vehicle
- trip
- notes
- photo

Automatic:

- subtotal
- total
- timestamp
- created_by
- inventory effect
- contract effect

## 5. Collectors

List should prioritize recent activity and total volume.

Collector profile:

- identity
- contact
- total quantity
- total paid
- average price
- recent purchases
- notes

## 6. Expenses

List:

- date
- category
- amount
- currency
- contract
- vehicle
- status

Create expense should require only the fields needed by the business policy.

## 7. Inventory

Top KPIs:

- total stock
- reserved
- available
- inventory value if costing is enabled

Main table:

- material
- location
- quantity
- reserved
- available
- cost/kg

## 8. Deliveries

List:

- delivery number
- client
- contract
- quantity
- vehicle
- driver
- date
- status

## 9. Invoices

List:

- invoice number
- client
- contract
- amount
- currency
- issue date
- status

Actions:

- preview
- download
- send
- void/correct where authorized

## 10. Reports

Filters should be consistent across reports:

- date range
- client
- contract
- material
- collector
- vehicle
- location

Exports:

- PDF
- CSV/XLSX where appropriate

## 11. Settings

- organization profile
- users/roles
- materials
- expense categories
- invoice numbering
- document templates
- currencies
- locations
- vehicles

## UX rule

Each screen must have one obvious primary action and no accidental competition between actions.
