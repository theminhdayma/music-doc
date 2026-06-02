---
type: brainstorming
project: ecommerce-music
module: cart
status: draft
created: 2026-05-27
---

# Cart Module Brainstorming

- Goal: lightweight cart for buyers to hold selected music before checkout.
- MVP: add/remove/list, no quantity, prevent duplicates, enforce roles.
- Integrate with existing JWT auth and Music entity/status rules.
- Keep controllers thin; service layer owns rules.
- Use DB uniqueness constraint to protect duplicates as second line of defense.

Next: write spec and plan, then tests (RED) and implementation (GREEN).

# Related

- [[2026-05-27_cart-module]]
- [[SRS-Cart]]
- [[API-Cart]]
- [[Table-Cart]]
- [[2026-05-27_ADR_cart-module]]
