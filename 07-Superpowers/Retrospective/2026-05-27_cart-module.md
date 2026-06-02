---
type: retrospective
project: ecommerce-music
module: cart
created: 2026-05-27
---

# Cart Module — Retrospective

What went well:
- Followed project's thin-controller pattern; business logic in service.
- Added Flyway migration with unique constraint to protect duplicates.
- Wrote controller security tests and service unit tests.

What could be improved:
- We added service implementation before creating RED tests in one pass; strict TDD would create failing tests first.
- More integration tests covering DB and security would increase confidence.

Action items:
- Add integration tests (Testcontainers) for cart flows.
- Add API docs for checkout flows referencing cart.
- Define rate-limiting or anti-abuse for cart add/remove endpoints.

# Related

- [[2026-05-27_cart-module]]
- [[SRS-Cart]]
- [[API-Cart]]
- [[Table-Cart]]
- [[2026-05-27_ADR_cart-module]]
