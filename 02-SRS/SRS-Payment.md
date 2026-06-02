# SRS — Payment Module (Sandbox)

## Purpose

Defines the payment workflow within the MVP scope using a sandbox simulation (without integration with a real payment gateway).

## Scope

* **In Scope:** Simulated payment processing in the backend and storing payment status in orders.
* **Out of Scope:** External payment gateways, webhook callbacks, refunds, and partial payments.

## Functional Requirements

1. The system invokes the payment sandbox during the checkout process.
2. In the current sandbox implementation, payment results default to `SUCCESS`.
3. If the payment fails, the system must update the order with `payment_status = FAILED` and the checkout process must fail.
4. If the payment succeeds, the system must update the order with `payment_status = SUCCESS` and `order_status = PAID`.
5. The payment method is fixed as `SANDBOX` for the MVP.

## Data Requirements

* `payment_method` uses the enum: `SANDBOX`
* `payment_status` uses the enum: `PENDING`, `SUCCESS`, `FAILED`
* Detailed payment metadata (gateway transaction ID, raw payload, etc.) is not included in the MVP.

## Security & Compliance

* Sensitive payment information must not be logged.
* Internal payment objects must not be exposed directly to clients.

# Related

* [[API-Payment]]
* [[Table-Payment]]
* [[SRS-Order]]
