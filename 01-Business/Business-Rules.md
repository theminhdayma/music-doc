# Business Rules

## Authentication Rules

- Public users can register only as BUYER or SELLER.
- Public admin registration is rejected.
- Email must be unique.
- New accounts require email OTP verification before login.
- Password must be stored as BCrypt hash only.
- Login returns a JWT access token only after the email is verified.
- Password data must never be exposed in responses.
- Users can request a password reset OTP and update their password after verification.
- Users can view and update their own profile from the authenticated user endpoint.

## Music Rules

- Only SELLER can upload music.
- SELLER can only manage their own music.
- BUYER can view published music and seller profiles.
- ADMIN can hide or delete any music.
- Music supports soft delete.
- Music thumbnail and audio files must be uploaded to Cloudinary.
- Public music lists return only published tracks.

## Genre Rules

- Genres are used to categorize music tracks for browsing and filtering.
- Genre name must be unique.
- Genre slug must be unique.
- Genre slug must be URL friendly.
- Genre supports active and inactive states.
- Genre supports soft delete.
- Deleted genres must not appear in public APIs.
- Public users, BUYER, and SELLER can view only active and non-deleted genres.
- ADMIN can create, update, deactivate, and delete genres.

## Marketplace Rules

- The platform sells music licenses, not streaming access.
- Track file URLs must not be exposed directly.
- Public responses must never expose password hashes or internal entity objects.
- Authenticated users can update their own profile, but cannot update other users' accounts.

## Cart Rules

1. Cart is a buyer-side temporary collection of selected music items before checkout.
2. Only users with role `BUYER` can use cart APIs. Sellers and Admins cannot add/remove items to buyer cart.
3. One cart item represents one music asset; quantity is not supported in MVP.
4. A buyer cannot add their own music to their cart.
5. Only `PUBLISHED` and non-deleted music are allowed to be added to cart.
6. Duplicate cart items are prevented at service level and enforced with a DB unique constraint `(buyer_id, music_id)`.
7. Deleting a buyer (soft-delete) should not physically delete cart rows; system should ignore carts for soft-deleted users.
8. The cart may be cleared on successful checkout; implementation deferred to Checkout module.
9. No direct music file URLs are returned in cart API — signed URLs must be generated during checkout/delivery only.
10. Controllers must be thin; business rules live in `CartService`.

## Order + Payment Rules

1. Only users with role `BUYER` can perform checkout and access purchased library endpoints.
2. Checkout source of truth is buyer cart; empty cart checkout is rejected.
3. Cart items must be validated before checkout:
	- no duplicate music items in cart
	- music exists and is not soft-deleted
	- music is not `HIDDEN`
	- buyer has not already owned the music in `purchased_library`
4. Checkout must be atomic and transactional: create order, process payment, create snapshot order items, grant ownership, then clear cart.
5. Payment method for MVP is `SANDBOX` only.
6. Payment status lifecycle: `PENDING` -> `SUCCESS` or `FAILED`.
7. Order status lifecycle for MVP: `PENDING` -> `PAID`.
8. Ownership is determined by `purchased_library` (not by joining orders on each request).
9. Download URL must be signed and time-limited; never expose permanent raw file access.
10. API responses must return DTOs via `ApiResponse` / `PageResponse`, not entities.
# Related

- [[Ecommerce-Overview]]
- [[User-Roles]]
- [[System-Architecture]]
- [[SRS-Cart]]
- [[API-Cart]]
- [[Table-Cart]]
- [[SRS-Order]]
- [[SRS-Payment]]
- [[API-Order]]
- [[API-Payment]]
- [[Table-Order]]
- [[Table-Payment]]
