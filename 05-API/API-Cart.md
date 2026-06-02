---
title: Cart API
module: cart
version: 1
---

# Cart API

Base path: `/api/v1/buyer/cart`

Endpoints:

- `POST /api/v1/buyer/cart/{musicId}`
  - Description: Add a music item to the authenticated buyer's cart.
  - Roles: BUYER
  - Success: `200 OK` with `ApiResponse<Void>` message "Added to cart".
  - Errors: `400 Bad Request` (invalid operation), `404 Not Found` (music or buyer not found), `409 Conflict` (already in cart), `401/403` (auth).

- `DELETE /api/v1/buyer/cart/{musicId}`
  - Description: Remove a music item from the authenticated buyer's cart.
  - Roles: BUYER
  - Success: `200 OK` with `ApiResponse<Void>` message "Removed from cart".
  - Errors: `404 Not Found` (cart item not found), `401/403` (auth).

- `GET /api/v1/buyer/cart`
  - Description: Retrieve authenticated buyer cart with totals.
  - Roles: BUYER
  - Success: `200 OK` with `ApiResponse<CartResponse>` where `CartResponse` contains:
    - `items`: array of `CartItemResponse`
    - `totalItems`: integer
    - `totalPrice`: decimal

DTOs (summary):

- `CartItemResponse`:
  - `cartItemId`: number
  - `musicId`: number
  - `title`: string
  - `slug`: string
  - `artistName`: string
  - `thumbnailUrl`: string
  - `price`: number
  - `sellerId`: number
  - `sellerName`: string

- `CartResponse`:
  - `items`: `CartItemResponse[]`
  - `totalItems`: number
  - `totalPrice`: number

Notes:
- This API follows existing project patterns: thin controllers, service-layer business logic, DTO responses, and soft-delete checks for users/music.
- Do not expose raw music file URLs; use signed URLs during checkout/delivery flows.

# Related

- [[SRS-Cart]]
- [[Table-Cart]]
- [[2026-05-27_cart-module]]
