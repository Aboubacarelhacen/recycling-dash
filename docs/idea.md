# IDEA.md — Africa Aluminium Recycling Operations Platform

## 1. One-sentence idea

Build a premium, operationally simple **recycling procurement and contract management platform** that connects client contracts to field purchasing, material quantities, expenses, inventory, delivery, invoicing, and true contract profitability.

## 2. The business problem

The company receives offers/orders from clients for recyclable material such as aluminum cans. A client may commit money against a target quantity, for example a requirement for 20–30 tons. The company then has to execute that commitment by sourcing material from collectors/suppliers, paying for material, transporting it, using vehicles, paying fuel and worker-related expenses, storing/handling material, and finally delivering the required quantity to the client.

The current process is spreadsheet/document driven. The uploaded workbook demonstrates a real operational ledger with columns for date, supplier, item/quantity count, weight, unit purchase price, purchase amount, transport, fuel, other costs, and total amount. It also contains monthly goal/target and payment/remaining tracking.

The uploaded receipt reference shows Africa Aluminium branding and contact information and provides a concrete visual direction for generated documents: green industrial branding, company mark, website, phone/email contacts, and Nouakchott industrial-area location.

### The actual operational pain

- Re-entering the same information in multiple places.
- Manual totals and formulas are easy to break.
- Contract fulfilment requires manual reconciliation.
- Managers cannot instantly see true margin per contract.
- Supplier/collector history is fragmented.
- Fuel/transport/food costs can be disconnected from the contract that created them.
- Invoices and receipts are recreated manually.
- Management reporting is delayed.
- Field collection may occur with unreliable connectivity.

## 3. Product thesis

### We are not building an Excel replacement.

Excel records transactions. The platform should **connect transactions and explain the state of the business**.

The system should answer, in seconds:

- What did we promise the client?
- How much material have we collected?
- How much remains?
- What did that material actually cost?
- How much extra did fuel/transport/food/labor add?
- Are we on schedule?
- Are we still profitable?
- Which collectors are supplying the most material?
- Which vehicles are expensive to operate?
- Which contracts are at risk?
- What document should be sent to the client now?

## 4. Product positioning

Recommended category:

> **Recycling Operations Management Platform**

Alternative positioning:

> **The operating system for recycling procurement.**

The product can initially be built for Africa Aluminium but should be modeled as a multi-tenant SaaS-ready system from day one unless the client explicitly wants a single-company deployment.

## 5. Core lifecycle

```text
CLIENT
  ↓
CONTRACT / OFFER ACCEPTED
  ↓
PROCUREMENT PLAN
  ↓
COLLECTOR/SUPPLIER PURCHASES
  ↓
WEIGHT / RECEIVING
  ↓
INVENTORY
  ↓
OPERATING EXPENSES + LOGISTICS
  ↓
DELIVERY
  ↓
INVOICE / RECEIPT
  ↓
PAYMENT
  ↓
REALIZED / PROJECTED PROFIT
```

## 6. Primary users

### Owner / Executive
Needs a fast business view: revenue, spend, tonnes, margins, contract health, cash exposure, and exceptions.

### Operations Manager
Owns contracts, procurement, inventory, collectors, deliveries, and daily execution.

### Accountant / Finance
Owns expenses, invoices, payments, document correctness, reconciliations, and financial reporting.

### Field Worker / Buyer
Needs a fast mobile workflow for purchases, weights, supplier selection, notes, photos, and field expenses.

### Driver
Needs assignments, pickup/delivery information, trip details, and proof of delivery.

### Viewer / Auditor
Read-only access to approved information.

## 7. MVP

### Build first

- Authentication and organization setup.
- Dashboard.
- Clients.
- Materials.
- Collectors/suppliers.
- Contracts.
- Purchases.
- Expenses.
- Inventory movements.
- Deliveries.
- Invoices/receipts.
- Basic reporting.
- Role-based permissions.
- Audit log.

### Build next

- Vehicles and fleet cost visibility.
- Payment tracking.
- Advanced profitability analysis.
- Alerts and notifications.
- Mobile field application.
- Offline synchronization.
- Supplier/collector portal.

### Build later

- AI business assistant.
- Contract risk prediction.
- Pricing recommendations.
- Automatic PO/invoice extraction.
- WhatsApp intake.
- Client portal.
- Supplier portal.

## 8. Product principles

1. **Contract-centered.** Transactions should be attributable to a business commitment.
2. **Weight-first.** Quantity of recyclable material must be treated as a first-class business measure.
3. **Money-safe.** Currency amounts require explicit currency, decimal-safe arithmetic, and auditable adjustments.
4. **Fast field entry.** The field worker should be able to record a purchase within seconds.
5. **Offline-aware.** Field data must tolerate intermittent connectivity.
6. **Audit-ready.** Important changes must be explainable.
7. **Document automation.** The same structured data should power invoice/receipt generation.
8. **Management by exception.** Dashboard highlights contracts/issues that need attention.
9. **AI is downstream of truth.** AI can interpret and automate; it does not define accounting truth.
10. **Premium but restrained UI.** Visual polish should reinforce trust and operational seriousness.

## 9. Core differentiation

The most strategically valuable feature is the **Contract Workspace**.

Every contract should have a living operational and financial view:

```text
Target quantity
Collected quantity
Remaining quantity
Fulfilment %
Days elapsed / remaining
Material purchase cost
Operational expenses
Total committed cost
Projected margin
Cost per kg
Required collection rate
Risk status
```

## 10. Contract health model

Use a simple initial status model:

- `ON_TRACK` — fulfilment and economics are healthy.
- `ATTENTION` — a metric has deteriorated but recovery is plausible.
- `AT_RISK` — target, deadline, or margin is threatened.
- `COMPLETED` — contract fulfilled and closed.
- `CANCELLED` — contract ended without completion.

The exact thresholds should be configurable later. Do not hardcode arbitrary thresholds without client validation.

## 11. Example economics

Illustrative scenario only — never hardcode these values:

```text
Contract value:        $10,000
Target:                30,000 kg
Material cost:         $4,820
Fuel:                     $430
Transport:                $180
Worker cost:              $600
Food:                     $210
Other:                    $120
--------------------------------
Total cost:            $6,360
Projected margin:      $3,640
```

The platform should distinguish:

- **Purchase cost** — money paid for material itself.
- **Direct operating cost** — fuel/transport/food/labor/etc. allocated to the activity.
- **Total contract cost** — all costs attributable under the chosen accounting rule.
- **Projected margin** — contract revenue less projected/actual cost.
- **Realized margin** — final revenue less final recognized cost.

## 12. Landing page thesis

Position the company/software as industrial technology, not as a generic environmental campaign.

Recommended message:

> **Run every recycling contract with precision.**

Supporting line:

> From client commitments to field procurement, inventory, expenses and final delivery — every kilogram and every cost connected in one operating platform.

The product UI itself should be the hero visual. The landing page should feel more like modern fintech/industrial software than a generic “green” website.

## 13. Long-term vision

The platform can become a vertical operating system for recycling businesses in Africa:

- Procurement.
- Contract execution.
- Inventory.
- Logistics.
- Payments.
- Reporting.
- Supplier relationships.
- Customer portals.
- AI-powered management.

The eventual moat is not the dashboard. It is the **connected operational dataset** and the workflows built around it.
