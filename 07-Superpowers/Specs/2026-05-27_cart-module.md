---
type: spec
project: ecommerce-music
module: cart
status: approved
created: 2026-05-27
---

# Problem
Buyers currently have no dedicated cart flow to collect music before checkout, causing friction in purchase preparation and no place to validate purchase-eligible items.

# Goal
Provide a secure buyer cart module that supports add/remove/list operations, enforces purchase rules, and returns clean DTO responses without exposing internal entities.

# Scope
- Buyer adds/removes music from personal cart.
- Buyer lists cart items with total item count and total price.
- Service enforces role, ownership, status, and duplicate rules.
- Do not expose entities directly.

# User Flow
- Buyer logs in and receives JWT.
- Buyer adds a published music item via `POST /api/v1/buyer/cart/{musicId}`.
- System validates role, ownership, item state, and duplication.
- Buyer opens cart via `GET /api/v1/buyer/cart` and sees totals.
- Buyer removes an item via `DELETE /api/v1/buyer/cart/{musicId}`.

# Technical Design
- Persist cart entries in MySQL using Flyway migration `V4__create_cart_item.sql`.
- Use `CartItem` entity with `buyer` and `music` relations.
- Use `CartService` interface and `CartServiceImpl` for business logic.
- Use `CartController` under `/api/v1/buyer` with role guard `BUYER`.
- Use DTOs `CartItemResponse` and `CartResponse` in API responses.

# API
- `POST /api/v1/buyer/cart/{musicId}`
- `DELETE /api/v1/buyer/cart/{musicId}`
- `GET /api/v1/buyer/cart`

# Database
- Add `cart_item` table with foreign keys to `user_account` and `music`.
- Use `UNIQUE(buyer_id, music_id)` as duplicate prevention at DB level.

# Edge Cases
- Buyer cannot add own music to cart.
- Only `PUBLISHED` and non-deleted music can be added.
- Duplicate cart item add request returns conflict.
- Unauthorized or wrong-role users cannot access buyer cart APIs.
- Removing non-existing cart item returns not found.

# Acceptance Criteria
- [x] Buyer can add published music to cart.
- [x] Buyer can remove cart item.
- [x] Buyer can view cart with `totalItems` and `totalPrice`.
- [x] Duplicate add is rejected.
- [x] Buyer cannot add own music.

# Related
- [[SRS-Cart]]
- [[API-Cart]]
- [[Table-Cart]]
- [[2026-05-27_ADR_cart-module]]
- [[2026-05-27_cart-module]]
