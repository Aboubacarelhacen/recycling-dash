# Business Model

## Operating model

The company behaves like a **procurement-and-fulfilment operator** in the recycling supply chain.

It has two economic sides:

### Sell-side

A client offers money for a quantity/material requirement.

### Buy-side

The company spends money sourcing the material from collectors/suppliers and operating the collection process.

### Margin

The business succeeds when the sell-side value remains greater than the fully attributable buy-side and operating cost.

## Key business objects

- Client
- Contract / accepted offer
- Material
- Collector / supplier
- Purchase transaction
- Weight
- Expense
- Vehicle / trip
- Warehouse / stock
- Delivery
- Invoice
- Payment

## Management questions

The software must make these easy:

1. Which commitments are active?
2. How much quantity remains on each commitment?
3. What is our effective cost per kg?
4. How much cash has been deployed?
5. Which contracts are profitable?
6. Which are at risk?
7. How much stock is available?
8. What has been delivered and invoiced?

## Important distinction: operational vs financial truth

Operational truth answers:

> How much material moved?

Financial truth answers:

> How much did it cost and how much did we earn?

The system must keep both, while allowing them to be joined by contract, material, supplier, date, vehicle and location.
