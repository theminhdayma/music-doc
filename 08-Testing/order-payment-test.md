# Order + Payment Test Checklist

## Service and controller coverage

- [x] Checkout success flow: order created, payment success, purchased library granted, cart cleared.
- [x] Checkout fail when cart empty.
- [x] Checkout fail when cart has duplicate music.
- [x] Checkout fail when music is hidden or deleted.
- [x] Checkout fail when buyer already owns music.
- [x] Security test: buyer routes blocked for non-BUYER.
- [x] Security test: admin routes blocked for non-ADMIN.
- [x] Library download rejected when ownership missing.
- [x] Library download returns signed URL when ownership exists.
- [x] Seller revenue stats: no sales returns zero average.
- [x] Seller revenue stats: successful purchases aggregate revenue from `price_at_purchase`.
- [x] Seller revenue stats: pending and failed payments are excluded.
- [x] Seller revenue stats: soft-deleted musics and other sellers are excluded.

## Executed test suites

- [x] Targeted tests:
  - `OrderServiceTest`
  - `OrderControllerSecurityTest`
  - `OrderItemRepositoryTest`
  - `MusicServiceTest`
  - `MusicControllerSecurityTest`
- [x] Full backend suite: `./gradlew test`
- [x] Build verification: `./gradlew build`

## Notes

- Current project has no `lint` task configured in Gradle.
- Production-grade signed delivery should be validated in a dedicated integration test when cloud private delivery is finalized.

# Related

- [[SRS-Order]]
- [[SRS-Payment]]
- [[API-Order]]
- [[API-Payment]]
- [[Table-Order]]
- [[Table-Payment]]
- [[ADR-Order-Payment-Ownership]]
