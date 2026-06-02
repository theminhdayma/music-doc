---
type: code-review
project: ecommerce-music
module: order-payment
created: 2026-05-28
---

# Order + Payment Module — Code Review Notes

Files reviewed:
- `OrderServiceImpl`
- `LibraryServiceImpl`
- `PaymentServiceImpl`
- `OrderController`, `LibraryController`
- Mapper + repository classes for order/payment/library

Checklist:
- Business logic in service layer, not controller: pass.
- Response contract uses DTO + `ApiResponse` / `PageResponse`: pass.
- Transaction boundary for checkout is explicit (`@Transactional`): pass.
- Ownership check relies on `purchased_library`: pass.
- Snapshot fields persisted in `order_items`: pass.
- Role access restrictions buyer/admin are present: pass.
- Full backend tests after refactor: pass.

Risks / follow-up:
- Signed URL implementation currently hashes parameters over existing URL; suitable for sandbox but should be replaced with provider-native private delivery in production.
- Add cancel/revoke flow tests when order cancellation feature is implemented.

# Related

- [[2026-05-28_order-payment-module]]
- [[SRS-Order]]
- [[API-Order]]
- [[Table-Order]]
- [[ADR-Order-Payment-Ownership]]
