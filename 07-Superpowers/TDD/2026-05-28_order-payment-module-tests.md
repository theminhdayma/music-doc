---
type: tdd
project: ecommerce-music
module: order-payment
created: 2026-05-28
---

# Order + Payment Module — TDD Notes

Status: implemented and verified (targeted + full backend tests passed)

## RED -> GREEN coverage

1. Checkout success
- create order
- set `payment_status = SUCCESS`
- set `order_status = PAID`
- create order snapshots
- grant ownership in `purchased_library`
- clear cart

2. Checkout failures
- cart empty -> fail
- duplicate cart items -> fail
- hidden/deleted music -> fail
- already owned music -> fail
- payment failed -> fail and mark failed

3. Access control
- buyer endpoints require BUYER role
- admin order endpoints require ADMIN role

4. Library behavior
- list purchased library by buyer
- reject download when buyer does not own music
- return signed URL when ownership valid

## Implemented test classes

- `OrderServiceTest`
- `LibraryServiceTest`
- `OrderControllerSecurityTest`
- `OrderRepositoryTest`

# Related

- [[2026-05-28_order-payment-module]]
- [[SRS-Order]]
- [[API-Order]]
- [[Table-Order]]
