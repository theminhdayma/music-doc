---
type: srs
project: ecommerce-music
module: cart
created: 2026-05-27
---

# Problem

There is no buyer-side cart functionality to let users collect music items before checkout. The frontend needs a small, role-protected API to add/remove/list selected music.

# Goal

Provide a minimal Cart module that allows authenticated buyers to add music to a cart, remove items, and retrieve the cart with totals. The cart must enforce business rules (no self-add, only published music) and avoid exposing music file URLs.

# Scope

In scope:
- Buyer-only cart APIs (add, remove, list)
- Service-layer business rules and DB migration
- DTO responses and thin controllers

Out of scope:
- Checkout/payment integration (converting cart → order)
- Quantity support (one cart item per music in MVP)
- Cart sharing or cross-device sync beyond JWT-authenticated user

# User Flow

1. Buyer authenticates (existing JWT flow).
2. Buyer calls `POST /api/v1/buyer/cart/{musicId}` to add a music item.
3. Service validates buyer, music existence, music status, and ownership; prevents duplicates.
4. Buyer may call `GET /api/v1/buyer/cart` to list items and totals.
5. Buyer may call `DELETE /api/v1/buyer/cart/{musicId}` to remove an item.

# Technical Design

- Controller: `CartController` under `/api/v1/buyer` with `@PreAuthorize("hasRole('BUYER')")`.
- Service: `CartService` / `CartServiceImpl` holds business logic and throws domain exceptions (`NotFoundException`, `ConflictException`, `BadRequestException`).
- Repository: `CartItemRepository` extends `JpaRepository` with helper queries `existsByBuyerIdAndMusicId`, `findByBuyerIdOrderByCreatedAtDesc`.
- Security: Reuses existing JWT and `AuthenticatedUser` principal to determine buyer id and role.
- DB: Flyway migration `V4__create_cart_item.sql` creates `cart_item` table with `UNIQUE(buyer_id,music_id)`.

# API

- `POST /api/v1/buyer/cart/{musicId}` — add to cart
- `DELETE /api/v1/buyer/cart/{musicId}` — remove from cart
- `GET /api/v1/buyer/cart` — list cart (returns `CartResponse` with `items[]`, `totalItems`, `totalPrice`)

# Database

- Table: `cart_item(id, buyer_id, music_id, created_at)`
- Foreign keys: `buyer_id -> user_account(id)`, `music_id -> music(id)`
- Constraint: `UNIQUE(buyer_id, music_id)` to prevent duplicates

# Edge Cases

- Adding a music that does not exist → `404 Not Found`.
- Adding a non-published or soft-deleted music → `400 Bad Request`.
- Buyer tries to add their own music → `400 Bad Request`.
- Duplicate add → `409 Conflict` (service-level check + DB unique constraint).
- Unauthorized access by non-buyers → `401/403` depending on auth state.

# Acceptance Criteria

- Buyer can add a published music to their cart: returns `200 OK` and persisted row.
- Duplicate add returns `409 Conflict`.
- Removing an existing cart item succeeds with `200 OK`.
- Listing returns `CartResponse` with correct `totalItems` and `totalPrice`.

# Related

- [[Cart-module]]
- [[API-Cart]]
- [[Table-Cart]]
- [[ADR_Cart-module]]

