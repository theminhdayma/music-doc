# User Flow — Order + Payment Module

## Buyer Checkout Flow

1. The buyer opens the cart and proceeds to checkout.
2. The system retrieves all cart items of the current buyer.
3. The system validates that the cart is not empty.
4. The system checks for duplicate items in the cart.
5. The system validates each song:

   * Exists
   * Not soft-deleted
   * Not in `HIDDEN` status
   * Not already owned by the buyer
6. The system creates an order (`PENDING`, `payment_status = PENDING`, `payment_method = SANDBOX`).
7. The system initiates the sandbox payment process.
8. If the payment is successful, the order is updated to `PAID` and `payment_status = SUCCESS`.
9. The system creates order item snapshots.
10. The system creates purchased library records.
11. The system clears the cart.
12. The system returns `ApiResponse<OrderResponse>`.

## Buyer Library Flow

1. The buyer requests the library list.
2. The system returns a paginated list of purchased songs from `purchased_library`.
3. The buyer requests a download using `musicId`.
4. The system verifies ownership through `purchased_library`.
5. The system returns a signed download URL with an expiration time.

## Admin Order Flow

1. The admin requests a paginated list of orders.
2. The admin requests order details by ID.
3. The system returns DTOs wrapped in `ApiResponse`.

# Related

- [[SRS-Order]]
- [[SRS-Payment]]
- [[API-Order]]
- [[API-Payment]]
- [[Table-Order]]
