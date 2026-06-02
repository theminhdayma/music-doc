---
type: adr
project: ecommerce-music
module: cart
created: 2026-05-27
---

## Context
We need a simple, reliable cart for buyers to collect music before checkout. Requirements: buyer-only, no quantities in MVP, avoid exposing file URLs.

## Decision
1. Create a dedicated `cart_item` table with columns `id`, `buyer_id`, `music_id`, `created_at` and a unique constraint on `(buyer_id, music_id)`.
2. Enforce uniqueness both in application service-level checks and at DB constraint level.
3. Do not support quantity in MVP — one row per music.
4. Use soft-delete for users and music; service queries must filter deleted_at IS NULL.
5. Keep controllers thin; put rules in `CartService` and use domain-specific exceptions.

## Consequences
- Simplicity for frontend and backend; easy to extend later to support quantity.
- Relying on DB constraint prevents race-condition duplicates as a last line of defense.
- Soft-delete awareness increases query complexity but aligns with project rules.

# Related

- [[SRS-Cart]]
- [[API-Cart]]
- [[Table-Cart]]
