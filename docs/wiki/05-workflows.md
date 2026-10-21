# Core Workflows

## 1. Client contract workflow

```text
Draft offer
  ↓
Negotiation / review
  ↓
Accepted
  ↓
Active
  ↓
Partially fulfilled
  ↓
Fulfilled
  ↓
Closed
```

## 2. Purchase workflow

```text
Start purchase
  ↓
Select supplier
  ↓
Select material
  ↓
Record weight
  ↓
Record unit price
  ↓
Record associated operational costs (when applicable)
  ↓
Attach evidence (optional/required by policy)
  ↓
Save
  ↓
Inventory increases
  ↓
Contract fulfillment increases (if linked)
  ↓
Financial totals recalculate
```

## 3. Expense workflow

```text
Create expense
  ↓
Category
  ↓
Amount/currency
  ↓
Date
  ↓
Optional contract/vehicle/trip/location link
  ↓
Receipt/evidence
  ↓
Approve (if approval is enabled)
  ↓
Included in reporting/costing
```

## 4. Delivery workflow

```text
Ready for delivery
  ↓
Create delivery
  ↓
Select client/contract
  ↓
Select material/quantity
  ↓
Select vehicle/driver
  ↓
Dispatch
  ↓
Delivered
  ↓
Proof of delivery
  ↓
Inventory decreases
  ↓
Contract delivered quantity updates
  ↓
Invoice/settlement
```

## 5. Invoice workflow

```text
Draft
  ↓
Review
  ↓
Issued
  ↓
Sent
  ↓
Partially paid
  ↓
Paid
```

Voids/cancellations must be auditable.

## 6. End-of-day workflow

The manager should be able to review:

- total weight bought
- total material spend
- fuel spend
- transport spend
- other expenses
- active contracts touched
- stock changes
- deliveries made
- cash paid out
- exceptions

## 7. Monthly workflow

- total tonnes by material
- procurement cost by material
- expense breakdown
- contract performance
- inventory closing position
- client receivables
- supplier/collector payouts
- realized/projected margins
- comparison vs target
