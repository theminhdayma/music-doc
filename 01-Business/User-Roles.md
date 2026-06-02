# User Roles

## Public

- Can register as BUYER or SELLER.
- Can verify email with OTP, request password reset, and log in with email and password.
- Cannot access admin-only endpoints.
- Can browse published music.

## Buyer

- Can view active genres.
- Can view published music.
- Can purchase licenses.
- Cannot manage genres.
- Cannot upload, edit, or delete music.

## Seller

- Can view active genres.
- Can upload music.
- Can update own music.
- Can delete own music.
- Can view own music list.
- Cannot manage genres.
- Cannot manage other sellers' music.

## Admin

- Can view all genres.
- Can create, update, deactivate, and soft delete genres.
- Can manage platform-wide content and moderation.
- Can hide any music.
- Can delete any music.

# Related

- [[Business-Rules]]
- [[SRS-Authentication]]
- [[SRS-Cart]]
