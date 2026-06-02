---
type: tdd
project: ecommerce-music
module: cart
created: 2026-05-27
---

# Cart Module — TDD Test Cases

Status: implemented and verified (unit tests passing)

Planned unit tests (service level) — RED → GREEN:

1. Add to cart — success
   - Given authenticated buyer and published music not owned by buyer
   - When `addToCart(musicId)`
   - Then repository `save(...)` called and no exception thrown

2. Add to cart — duplicate
   - Given music already exists in buyer's cart
   - When `addToCart(musicId)`
   - Then `ConflictException` thrown

3. Add to cart — seller adding own music
   - Given buyer is also the seller of the music
   - When `addToCart(musicId)`
   - Then `BadRequestException` thrown

4. Remove from cart — success
   - Given cart item exists
   - When `removeFromCart(musicId)`
   - Then repository `delete(...)` called

5. Get cart — totals
   - Given buyer has several cart items
   - When `getCart()`
   - Then returns `CartResponse` with correct `totalItems` and `totalPrice`

Test implementation:
- Tests are implemented under `src/test/java/.../service/CartServiceTest.java` using Mockito and JUnit5.
- Tests run locally as part of `./gradlew test` and passed in this run.

# Related

- [[2026-05-27_cart-module]]
- [[SRS-Cart]]
- [[API-Cart]]
- [[Table-Cart]]
- [[ADR_cart-module]]
