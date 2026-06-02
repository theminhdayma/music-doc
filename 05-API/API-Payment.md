---
title: Payment API (Stripe)
module: payment
version: 1
---

# Payment API

The payment module integrates with Stripe Checkout and exposes checkout/confirmation endpoints.

## Trigger Points

* **Checkout Endpoint:**
  * `POST /api/v1/buyer/orders/checkout`
  * Initiates the Stripe Checkout Session and returns `checkoutUrl`.

* **Confirmation Endpoint:**
  * `POST /api/v1/buyer/orders/checkout/confirm?sessionId={sessionId}`
  * Verifies session payment status on Stripe and finalizes the order.

## Behavior

1. **Checkout:**
   * Validates cart, creates a `PENDING` order with payment method `STRIPE`.
   * Maps cart items to Stripe Checkout line items.
   * Invokes Stripe API to create a session and returns `checkoutUrl` to client.

2. **Confirmation:**
   * Client sends Stripe `sessionId` query parameter after redirection.
   * Service retrieves session details from Stripe.
   * Updates order status to `PAID` and payment status to `SUCCESS`.
   * Saves purchased tracks to `purchased_library`.
   * Clears the items from the user's cart.

## Notes

* Environment variable `STRIPE_API_KEY` must be configured on the backend environment.
* Frontend success page `/checkout/success?session_id=...` handles calling the confirmation API.

# Related

* [[SRS-Payment]]
* [[Table-Payment]]
* [[SRS-Order]]
