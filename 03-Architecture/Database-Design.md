# Database Design

## User Account Table

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| email | VARCHAR(255) | NOT NULL, UNIQUE | Login identifier |
| full_name | VARCHAR(255) | NOT NULL | Display name |
| avatar_url | VARCHAR(2048) | NULL | Optional Cloudinary profile URL |
| phone_number | VARCHAR(20) | NULL | Optional contact number |
| address | VARCHAR(500) | NULL | Optional profile address |
| bio | VARCHAR(1000) | NULL | Optional profile bio |
| date_of_birth | DATE | NULL | Optional profile birthday |
| password_hash | VARCHAR(255) | NOT NULL | BCrypt hash only |
| role | ENUM | NOT NULL | BUYER, SELLER, ADMIN |
| status | ENUM | NOT NULL, default ACTIVE | Account lifecycle |
| email_verified | BOOLEAN | NOT NULL, default false | Email verification flag |
| email_verification_otp | VARCHAR(255) | NULL | BCrypt-hashed verification OTP |
| email_verification_otp_expires_at | DATETIME | NULL | Verification OTP expiry |
| password_reset_otp | VARCHAR(255) | NULL | BCrypt-hashed password reset OTP |
| password_reset_otp_expires_at | DATETIME | NULL | Password reset OTP expiry |
| created_at | DATETIME | NOT NULL | Set on insert |
| updated_at | DATETIME | NOT NULL | Set on update |
| deleted_at | DATETIME | NULL | Soft delete marker |

## Notes

- Buyer and Seller share the same user table.
- Admin accounts are not publicly registerable.
- Password hashes are never exposed in API responses.
- Email verification and password reset state live on the same user row so the auth service stays simple.
- OTP values are short-lived and should be cleared after successful verification or reset.
- Profile fields are nullable and stay `NULL` for newly registered users until updated.

## Music Table

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| seller_id | BIGINT | NOT NULL, FK | Owner account |
| genre_id | BIGINT | NOT NULL, FK | Genre reference |
| title | VARCHAR(255) | NOT NULL | Track title |
| slug | VARCHAR(255) | NOT NULL, UNIQUE | Public lookup key |
| artist_name | VARCHAR(255) | NOT NULL | Display artist name |
| description | TEXT | NULL | Optional description |
| thumbnail_url | VARCHAR(500) | NULL | Cloudinary secure URL |
| audio_url | VARCHAR(500) | NULL | Cloudinary secure URL |
| duration_seconds | INT | NULL | Track length |
| price | DECIMAL(10,2) | NOT NULL, default 0 | Track price |
| status | VARCHAR(20) | NOT NULL, default DRAFT | DRAFT, PUBLISHED, HIDDEN |
| created_at | DATETIME | NOT NULL | Set on insert |
| updated_at | DATETIME | NOT NULL | Set on update |
| deleted_at | DATETIME | NULL | Soft delete marker |

## Notes

- Music queries must filter `deleted_at is null`.
- Public music queries must also filter `status = PUBLISHED`.
- Cloudinary `secure_url` values are stored instead of local file paths.

## Genre Table

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| name | VARCHAR(100) | NOT NULL, UNIQUE | Display name |
| slug | VARCHAR(120) | NOT NULL, UNIQUE | URL-friendly identifier |
| description | VARCHAR(1000) | NULL | Optional long text |
| is_active | BIT(1) | NOT NULL, default true | Active/inactive state |
| created_at | DATETIME | NOT NULL | Set on insert |
| updated_at | DATETIME | NOT NULL | Set on update |
| deleted_at | DATETIME | NULL | Soft delete marker |

## Notes

- Deleted genres remain in the table for auditability.
- Public queries must filter on `deleted_at is null` and `is_active = true`.
- Unique constraints protect name and slug at the database level.

## Order Table

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| buyer_id | BIGINT | NOT NULL, FK -> user_account(id) | Buyer who checks out |
| order_code | VARCHAR(50) | NOT NULL, UNIQUE | Human-readable order code |
| total_amount | DECIMAL(10,2) | NOT NULL | Snapshot order total |
| order_status | VARCHAR(20) | NOT NULL, default PENDING | PENDING, PAID |
| payment_method | VARCHAR(20) | NOT NULL, default SANDBOX | SANDBOX only in MVP |
| payment_status | VARCHAR(20) | NOT NULL, default PENDING | PENDING, SUCCESS, FAILED |
| created_at | DATETIME | NOT NULL | Set on insert |
| updated_at | DATETIME | NOT NULL | Set on update |

Notes:
- Index: `idx_orders_buyer_created_at (buyer_id, created_at)`.
- Order state and payment state are separate for clearer transitions.

## Order Items Table

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| order_id | BIGINT | NOT NULL, FK -> orders(id) | Parent order |
| music_id | BIGINT | NOT NULL, FK -> music(id) | Purchased music |
| music_title_at_purchase | VARCHAR(255) | NOT NULL | Snapshot title |
| artist_name_at_purchase | VARCHAR(255) | NOT NULL | Snapshot artist |
| thumbnail_url_at_purchase | VARCHAR(500) | NULL | Snapshot thumbnail |
| price_at_purchase | DECIMAL(10,2) | NOT NULL | Snapshot price |
| created_at | DATETIME | NOT NULL | Set on insert |

Notes:
- Snapshot fields preserve historical data if music metadata changes later.

## Purchased Library Table

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| buyer_id | BIGINT | NOT NULL, FK -> user_account(id) | Owner |
| music_id | BIGINT | NOT NULL, FK -> music(id) | Owned music |
| purchased_at | DATETIME | NOT NULL | Purchase timestamp |

Notes:
- Unique constraint: `(buyer_id, music_id)` prevents duplicate ownership records.
- Index: `idx_library_buyer_music (buyer_id, music_id)` supports ownership checks.
- Ownership lookup for download/authorization should read from this table directly.

# Related

- [[System-Architecture]]
- [[Table-User]]
- [[Table-Product]]
- [[Table-Order]]
- [[Table-Payment]]
