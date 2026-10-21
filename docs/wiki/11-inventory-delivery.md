# Inventory & Delivery

## Inventory principle

Inventory is the accumulated result of material movements.

The source of truth should be movement events, not a manually edited “current stock” number.

## Inventory dimensions

At minimum:

- material
- location
- quantity kg

Future dimensions:

- grade/quality
- batch/lot
- reserved quantity
- processed/unprocessed state

## Movement examples

### Inbound
Purchase receipt → `+quantity`

### Outbound
Delivery → `-quantity`

### Internal
Warehouse transfer → `-quantity` at source, `+quantity` at destination.

### Adjustment
Controlled correction with reason and audit.

## Contract vs inventory

A contract can require 30,000 kg, while inventory contains material for multiple contracts.

Therefore the software should distinguish:

- total physical stock
- reserved stock
- available stock
- quantity allocated to a contract

## Delivery

A delivery record should contain:

- client
- contract
- material
- quantity
- vehicle
- driver
- destination
- date/time
- delivery evidence
- status

## Proof of delivery

Potential evidence:

- signature
- photo
- document upload
- received weight
- receiving party

## Weight discrepancy

Do not silently overwrite source/destination weight differences. Record both where the business needs it and expose discrepancy rules later.
