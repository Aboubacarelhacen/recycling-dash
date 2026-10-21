# Data Dictionary

**This page now describes the schema that exists.** The authority is
`frontend/src/server/db/schema.ts` and the migrations in `frontend/drizzle/`;
if the two disagree, the schema is right and this page is stale.

Conventions as built:

- money is `numeric(14,2)`, weight is `numeric(12,3)`, both returned as strings
  so nothing passes through a float
- every business table carries `organization_id`
- statuses are Postgres enums matching `docs/wiki/06`
- invariants are database constraints, not only service checks
  (`docs/04-database.md` lists them)

## organizations

- `id`
- `name`
- `default_currency`
- `timezone`
- `created_at`
- `updated_at`

## users

- `id`
- `organization_id`
- `name`
- `email`, `phone`
- `role` (owner, manager, accountant, field_worker, driver, viewer)
- `status`
- `password_hash` (argon2id, nullable until a password is set)
- `last_login_at`
- `failed_login_count`, `locked_until` (five failures, fifteen minutes)
- timestamps

## sessions

Added in task 3. The cookie carries a random token; this table stores only its
SHA-256 hash, so a database leak cannot be replayed as a login.

- `id`
- `user_id`, `organization_id`
- `token_hash` (unique)
- `expires_at` (twelve hours), `last_used_at`, `revoked_at`
- `user_agent`, `ip_address`
- `created_at`

## clients

- `id`
- `organization_id`
- `name`
- `contact_name`
- `phone`
- `email`
- `address`
- `notes`

## collectors

- `id`
- `organization_id`
- `name`
- `phone`
- `location`
- `notes`
- `status`
- `since`

## collector_aliases

Added because the source workbook spells the same supplier several ways
(`docs/wiki/09`). An import resolves an alias to a canonical collector rather
than inventing a person.

- `id`
- `organization_id`
- `collector_id`
- `alias` (unique per organisation)

## materials

- `id`
- `organization_id`
- `name`
- `category`
- `base_unit`
- `active`

Base quantity should normally be kg.

## contracts

- `id`
- `organization_id`
- `client_id`
- `contract_number` (unique per organisation)
- `title`, `status`
- `material_id` (header material for the single-material case; several materials
  use `contract_items`)
- `target_quantity_kg` (must be greater than zero)
- `selling_price_per_kg`, `contract_value` (at least one is required)
- `currency`
- `start_date`, `deadline` (the deadline may not precede the start)
- `destination`
- `notes`
- `created_by`, timestamps

## contract_items

- `id`
- `contract_id`
- `material_id`
- `target_quantity_kg`
- `unit_price`
- `currency`
- `notes`

## purchases

- `id`
- `organization_id`
- `reference` (unique per organisation)
- `collector_id`
- `contract_id` nullable
- `warehouse_id`
- `vehicle_id` nullable
- `status` (DRAFT, POSTED, VOIDED)
- `purchase_date`
- `currency`
- `transport_cost`, `fuel_cost`, `other_cost` (never negative)
- `client_transaction_id` (unique per organisation, the idempotency key)
- `posted_at`, `voided_at`, `void_reason`
- `notes`
- `created_by`, timestamps

`trip_id` is not implemented. Trips arrive with the fleet slice.

## purchase_items

- `id`
- `purchase_id`
- `material_id`
- `quantity_kg`
- `unit_price`
- `subtotal`

## expense_categories

- `id`
- `organization_id`
- `code` (unique per organisation), `name`, `active`

## expenses

- `id`
- `organization_id`
- `reference` (unique per organisation)
- `category_id`
- `amount`
- `currency`
- `expense_date`
- `contract_id` nullable
- `vehicle_id` nullable
- `trip_id` nullable
- `status`
- `notes`

## inventory_movements

- `id`
- `organization_id`
- `material_id`
- `location_id`
- `quantity_kg_delta`
- `movement_type`
- `source_type`
- `source_id`
- `created_at`

## deliveries

- `id`
- `organization_id`
- `contract_id`
- `client_id`
- `status`
- `delivery_date`
- `vehicle_id`
- `driver_id`
- `destination`
- `notes`

## delivery_items

- `id`
- `delivery_id`
- `material_id`
- `quantity_kg`

## invoices

- `id`
- `organization_id`
- `invoice_number` (unique per organisation)
- `client_id`
- `contract_id` nullable
- `status`
- `issue_date`, `due_date`, `issued_at`
- `currency`
- `subtotal`, `total` (derived while a draft, frozen at issue)
- `voided_at`, `void_reason`
- `notes`, `created_by`, timestamps

## invoice_lines

Added because an invoice raised from a delivered quantity needs a priced line,
and a header-level quantity breaks the moment one shipment mixes two materials.

- `id`
- `invoice_id`
- `material_id`, `delivery_id` nullable
- `description`
- `quantity_kg` (greater than zero), `unit_price`, `line_total`

## payments

- `id`
- `organization_id`
- `invoice_id` nullable
- `direction`
- `amount`
- `currency`
- `payment_date`
- `method`
- `reference`

## attachments

- `id`
- `organization_id`
- `entity_type`
- `entity_id`
- `storage_key`
- `mime_type`
- `created_by`
- `created_at`

## audit_logs

- `id`
- `organization_id`
- `actor_id`
- `entity_type`, `entity_id`, `entity_label`
- `action`, `detail`
- `reason` nullable, required by the write path for corrections
- `before`, `after` (jsonb, changed fields only)
- `created_at`

## import_batches and import_rows

Added so legacy rows keep their provenance: workbook, sheet, row number,
original values, normalised values and the messages a row produced
(`docs/wiki/02`, `docs/wiki/18`). Nothing questionable is silently corrected.
