# SRS — Order Module

## Purpose

This module handles the checkout process from cart to order creation, stores order item snapshots, and allows buyers and admins to view orders based on their permissions.

## Actors

* **BUYER**: performs checkout, views their own order list, and views their own order details.
* **ADMIN**: views all orders and order details.

## Functional Requirements

1. Buyers can checkout using their current cart.
2. The system must reject checkout if the cart is empty.
3. The system must reject checkout if the cart contains duplicate song items.
4. The system must reject checkout if a song is soft-deleted or has `HIDDEN` status.
5. The system must reject checkout if the buyer already owns the song in `purchased_library`.
6. The system creates an order with `order_code`, `total_amount`, `order_status`, `payment_status`, and `payment_method`.
7. The system creates an order item snapshot for each purchased item at the time of checkout.
8. The system clears the cart after a successful checkout.
9. Buyers can view their order list with pagination.
10. Buyers can view details of their own orders.
11. Admins can view all orders with pagination.
12. Admins can view details of any order.

## State & Status

* `OrderStatus`: `PENDING`, `PAID`
* `PaymentMethod`: `SANDBOX`
* `PaymentStatus`: `PENDING`, `SUCCESS`, `FAILED`

## Non-Functional Requirements

* Checkout must run within a transaction to ensure atomicity.
* Controllers must not contain business logic; validation and workflow processing must be handled by services.
* All responses must use DTOs and be wrapped in `ApiResponse` or `PageResponse`.

## Error Handling

* `BadRequestException` for empty cart, duplicate cart items, hidden/deleted songs, or payment failures.
* `ConflictException` when the buyer already owns the song.
* `NotFoundException` when the order does not exist or is not accessible.
* `ForbiddenException` for invalid roles or unauthorized access.

# Related

* [[API-Order]]
* [[Table-Order]]
* [[SRS-Payment]]
