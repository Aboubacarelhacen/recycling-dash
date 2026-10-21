# Expenses & Fleet

## Expense categories

Initial categories:

- Fuel
- Transport
- Food
- Worker/Labor
- Vehicle maintenance
- Warehouse
- Communication
- Other

Categories should be configurable.

## Allocation

An expense may be linked to one or more dimensions:

- contract
- vehicle
- trip
- location
- date
- department

MVP can support one direct link and evolve later.

## Fuel

If fuel is associated with a vehicle, the system should be able to answer:

- fuel spend by vehicle
- fuel spend by date/month
- fuel spend by contract when allocation is available

Do not infer liters or kilometers unless those fields are actually collected.

## Vehicle profile

- identifier / plate
- vehicle type
- driver assignment
- active/inactive
- fuel spending
- maintenance spending
- collection/delivery activity

## Trip concept

A trip becomes valuable when one vehicle/driver visits multiple collectors or destinations. It is a future optimization, not necessarily a V1 requirement.

## Cost attribution principle

Do not pretend a cost is contract-specific when the input does not justify that conclusion. Allow a company-level or unallocated bucket.

Example:

`Fuel expense → vehicle=Truck 02 → contract=NULL`

is more honest than inventing a contract allocation.
