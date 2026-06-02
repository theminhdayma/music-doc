# SRS — Payment Module (Stripe)

## Purpose

Defines the payment workflow integrated with Stripe Checkout gateway.

## Scope

* **In Scope:** Stripe Checkout Session creation, redirecting to Stripe payment page, verifying payment status via Session ID confirmation.
* **Out of Scope:** Webhook callbacks, refunds, and partial payments.

## Functional Requirements

1. The system creates a Stripe Checkout Session during the checkout process and returns the session URL.
2. The user is redirected to Stripe to fill payment details.
3. The frontend success page verifies the payment using the session ID.
4. If the payment fails, the system updates the order with `payment_status = FAILED`.
5. If the payment succeeds, the system updates the order with `payment_status = SUCCESS` and `order_status = PAID`, registers music to purchased library, and clears cart items.
6. The payment method is configured as `STRIPE`.

## Data Requirements

* `payment_method` uses the enum: `SANDBOX`, `STRIPE`
* `payment_status` uses the enum: `PENDING`, `SUCCESS`, `FAILED`
* Detailed payment metadata includes `session_id` passed via redirect query param.

## Security & Compliance

* Sensitive payment information must not be logged.
* Internal payment objects must not be exposed directly to clients.

# Related

* [[API-Payment]]
* [[Table-Payment]]
* [[SRS-Order]]
