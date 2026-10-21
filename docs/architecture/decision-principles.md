# Architecture Decision Principles

1. One transactional source of truth.
2. Relational core for consistency.
3. Immutable-ish event history for inventory/financial changes.
4. Domain services for business calculations.
5. Server-side authority for saved totals.
6. Offline-capable edge, authoritative server core.
7. Asynchronous jobs for non-critical heavy work.
8. Explicit audit trail.
9. Minimal dependencies and boring infrastructure for V1.
10. Design boundaries that allow later mobile, AI and multi-tenant growth.
