# AI Layer

## Guiding principle

AI is a **copilot over trustworthy operational data**.

Do not let an LLM become the accounting engine or inventory authority.

## AI opportunities

### 1. Business Q&A

Examples:

- Which contracts are at risk?
- How much aluminum did we buy this month?
- What did fuel cost last week?
- Which collector supplied the most material?
- Which contract has the highest projected margin?

### 2. Contract intake

Read an uploaded client offer and draft:

- client
- material
- quantity
- price
- dates
- destination
- terms

Human must confirm before posting.

### 3. Document generation

Create draft invoices/receipts/delivery notes from canonical records.

### 4. Exception detection

Detect:

- unusual purchase price
- duplicate purchase
- unusual fuel expense
- sudden margin decline
- delivery quantity mismatch

### 5. Daily briefing

Generate a manager summary using structured metrics.

Example:

> Today you collected 2.34 tons. Three active contracts are on track; one is at risk because its recent collection rate is below the required daily rate. Material spend was 8% above the weekly average.

## AI safety rules

- AI cannot directly change money, inventory or contract values without explicit authorized actions.
- AI answers must cite internal records or report query context.
- AI must distinguish facts from estimates.
- AI should show the underlying period/contract/filter used.
- AI should never invent supplier, invoice, contract or payment data.
- High-impact actions require confirmation.

## AI architecture

```text
User question
    ↓
Intent / permission check
    ↓
Read-only business query tools
    ↓
Structured result
    ↓
LLM explanation
    ↓
Citations / source references
```

For write actions:

```text
LLM proposes action
   ↓
Validation
   ↓
Human confirmation (where required)
   ↓
Canonical API mutation
   ↓
Audit log
```

## Future pricing intelligence

A later model could recommend a maximum safe acquisition price based on:

- contract selling economics
- current collected quantity
- cost/kg
- deadline
- recent procurement rate
- expected operating expenses

But recommendations should be presented as advisory, with visible assumptions.
