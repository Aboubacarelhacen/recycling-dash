# API Contract Guidance

## General

- Use consistent JSON representations.
- Include server-calculated totals and derived values where useful.
- Use explicit status codes.
- Return validation errors in machine-readable + human-readable form.

## Example create purchase request

```json
{
  "collectorId": "...",
  "materialId": "...",
  "contractId": "...",
  "quantityKg": "420.000",
  "unitPrice": "35.00",
  "currency": "MRU",
  "transportCost": "0.00",
  "fuelCost": "500.00",
  "otherCost": "100.00",
  "clientTransactionId": "mobile-uuid"
}
```

## Example server response

```json
{
  "id": "...",
  "status": "POSTED",
  "quantityKg": "420.000",
  "purchaseSubtotal": "14700.00",
  "totalCost": "15300.00",
  "contract": {
    "fulfilledQuantityKg": "22420.000",
    "remainingQuantityKg": "7580.000",
    "fulfillmentRatio": 0.7473
  }
}
```

Use strings for money in JSON if the stack's number handling risks precision loss.
