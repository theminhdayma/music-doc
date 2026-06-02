---
title: Payment API (Sandbox)
module: payment
version: 1
---

# Payment API

Currently, the payment module operates internally within the checkout workflow and **does not expose a dedicated payment endpoint**.

## Trigger Point

* The payment sandbox is triggered by:

  * `POST /api/v1/buyer/orders/checkout`

## Behavior

1. After creating an order with `PENDING` status, the service invokes the sandbox payment process.
2. The current sandbox implementation always returns `SUCCESS`.
3. If the payment result is `FAILED`, the order is updated with `payment_status = FAILED` and the checkout process is terminated with an error.
4. If the payment result is `SUCCESS`, the order is updated with `payment_status = SUCCESS` and `order_status = PAID`.

## Notes

* The MVP does not include a payment provider transaction ID.
* When integrating a real payment gateway in the future, the checkout API contract should remain unchanged, while the implementation inside `PaymentService` can be extended.

# Related

* [[SRS-Payment]]
* [[Table-Payment]]
* [[SRS-Order]]
