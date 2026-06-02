---
type: code-review
project: ecommerce-music
module: cart
created: 2026-05-27
---

# Cart Module — Code Review Notes

- Files to review:
  - `CartServiceImpl` (business rules)
  - `CartController` (route/design)
  - `CartItem` entity and repository
  - Unit tests: `CartServiceTest`

Checklist:
- **Business logic**: Ensure no business rules in controller — all implemented in service.
- **Validation**: buyer-only checks, music status, ownership checks present.
- **Transactions**: `addToCart` and `removeFromCart` annotated with `@Transactional` — ok.
- **DTOs**: Controller returns DTOs via `ApiResponse` wrapper → consistent.
- **Soft delete**: Service uses `findByIdAndDeletedAtIsNull` when loading user/music.
- **Exceptions**: Use domain-specific exceptions (`NotFoundException`, `ConflictException`, `BadRequestException`).
- **Tests**: Unit tests cover positive and negative flows — consider adding more edge cases (concurrent adds).

Suggested improvements:
- Add integration tests for controller with full security flow (JWT), optionally using Testcontainers for DB.
- Consider adding metrics/events for cart changes for analytics.
- Add repository-level query to delete all cart items for a buyer when user is soft-deleted.

# Related

- [[2026-05-27_cart-module]]
- [[SRS-Cart]]
- [[API-Cart]]
- [[Table-Cart]]
- [[2026-05-27_ADR_cart-module]]
