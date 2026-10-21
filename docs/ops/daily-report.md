# Daily / Weekly / Monthly Reporting

## Daily report

```text
DAILY OPERATIONS — {date}

Material collected: {kg}
Purchase spend: {amount}
Fuel: {amount}
Transport: {amount}
Food: {amount}
Other: {amount}
Total operational spend: {amount}

Active contracts: {count}
On track: {count}
At risk: {count}
Completed: {count}

Top supplier by quantity: {supplier}
```

## Weekly report

Add:

- trend vs prior week
- cost/kg trend
- contract risk changes
- largest expense movements
- supplier activity

## Monthly report

Add:

- revenue
- total purchased kg
- total cost
- margin
- margin by contract
- margin by material
- expenses by category
- ending inventory
- invoice/payment status

## Report integrity

Reports must reference canonical transactional data. Do not maintain separate spreadsheet-like reporting logic that can drift from the dashboard.
