# ADR 0003 — Offline-First Field Operations

## Decision

Design field workflows so that basic purchase/expense capture can work with intermittent connectivity.

## Why

The field operation is where data is created, and connectivity should not become a blocker.

## Consequence

Mobile requires local pending state, idempotency and synchronization semantics.
