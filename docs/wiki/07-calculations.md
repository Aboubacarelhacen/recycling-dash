# Canonical Calculations

## 1. Purchase subtotal

For a single material line:

`purchase_subtotal = quantity_kg × unit_price`

If the business uses a different pricing unit, normalize to the contract/material's price unit explicitly.

## 2. Purchase total

`purchase_total = purchase_subtotal + transport_cost + fuel_cost + other_cost`

This is an operational total. Whether every cost should be included in inventory valuation or contract costing is a separate policy decision.

## 3. Fulfillment

`fulfillment_ratio = fulfilled_quantity_kg / target_quantity_kg`

Cap display at 100% if the UI uses a progress bar, but retain actual over-fulfillment in the underlying number.

## 4. Remaining quantity

`remaining_quantity_kg = max(target_quantity_kg - fulfilled_quantity_kg, 0)`

Do not erase over-fulfillment; store it as actual quantity.

## 5. Effective cost per kg

`effective_cost_per_kg = attributable_total_cost / attributable_quantity_kg`

The denominator must be non-zero. The allocation rule must be documented.

## 6. Projected margin

`projected_margin = projected_revenue - projected_total_cost`

## 7. Margin percentage

`margin_pct = margin / revenue`

Revenue zero must be handled explicitly.

## 8. Procurement velocity

A useful derived metric:

`daily_collection_rate_kg = fulfilled_quantity_kg / elapsed_collection_days`

For a forward-looking estimate:

`estimated_days_to_target = remaining_quantity_kg / expected_daily_rate_kg`

Never show a forecast without exposing enough context for the manager to understand it.

## 9. Deadline risk signal

The first version can use deterministic signals:

- remaining quantity
- days remaining
- recent daily collection rate
- current effective cost/kg

A later ML/AI layer may improve prediction, but the deterministic calculation remains explainable baseline logic.

## 10. Profitability layers

Do not collapse all cost concepts into one field.

Recommended layers:

- Material purchase cost
- Direct logistics/transport cost
- Fuel cost
- Labor/worker cost
- Food/per diem cost
- Other direct cost
- Allocated overhead (future)

This lets management see what is actually driving margin.

## 11. Rounding

- Store money with exact decimal/numeric precision.
- Store weight with appropriate precision.
- Round only for presentation or at explicit accounting boundaries.
- Never repeatedly round intermediate values in a chain of calculations.
