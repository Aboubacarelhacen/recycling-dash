# Domain Model

## Core entities

### Organization
The business using the platform.

### User
A person with credentials and role assignments.

### Client
A buyer/customer who purchases material from the organization.

### Collector / Supplier
A person or business selling recyclable material to the organization.

### Material
A recyclable commodity such as aluminum cans, plastic, copper, cardboard, etc.

### Contract
The commercial commitment with a client, including quantity, price, dates and terms.

### Purchase
A buy-side transaction in which the company acquires material from a collector/supplier.

### Purchase Item
A material line within a purchase when a transaction can contain multiple lines.

### Expense
Operational spend such as fuel, transport, food, worker costs, maintenance or other.

### Vehicle
A company/partner vehicle used in procurement or delivery operations.

### Trip
A movement activity that may contain one or more stops/purchases/deliveries.

### Warehouse / Location
A stock-holding or operational location.

### Inventory Movement
An immutable-ish event that changes stock.

### Delivery
A shipment of material to a client.

### Invoice
A client-facing billing document.

### Payment
Money received from a client or paid to a supplier, depending on the payment domain.

### Attachment
A photo, receipt scan, contract file, proof of delivery or other evidence.

### Audit Log
A record of sensitive changes.

## Relationship model

```text
Organization
 ├── Users
 ├── Clients
 ├── Collectors/Suppliers
 ├── Materials
 ├── Vehicles
 ├── Warehouses
 └── Contracts
       ├── Purchases
       │     └── Purchase Items
       ├── Expenses
       ├── Deliveries
       │     └── Delivery Items
       ├── Invoices
       └── Payments

Purchases / Deliveries / Adjustments
            ↓
      Inventory Movements
            ↓
         Inventory
```

## Contract-centered traceability

A user should be able to open a contract and see:

- every related purchase
- every related expense
- every delivery
- every invoice
- every payment
- current inventory reserved/available
- actual/projected margin

## Derived vs stored values

Prefer deriving:

- fulfillment percentage
- remaining quantity
- total purchase amount
- current inventory balance
- projected margin

from canonical transactional records, unless a documented snapshot is required for reporting/performance.

Store original transaction facts and controlled adjustments.
