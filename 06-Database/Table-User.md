# Table: `user_account`

## Purpose

Store shared accounts for Buyer, Seller, and Admin.

## Columns

| Column | Type | Constraints |
|--------|------|-------------|
| `id` | BIGINT | PK, auto increment |
| `email` | VARCHAR(255) | NOT NULL, UNIQUE |
| `full_name` | VARCHAR(255) | NOT NULL |
| `avatar_url` | VARCHAR(2048) | NULL |
| `phone_number` | VARCHAR(20) | NULL |
| `address` | VARCHAR(500) | NULL |
| `bio` | VARCHAR(1000) | NULL |
| `date_of_birth` | DATE | NULL |
| `password_hash` | VARCHAR(255) | NOT NULL |
| `role` | VARCHAR(20) | NOT NULL |
| `status` | VARCHAR(20) | NOT NULL, default `ACTIVE` |
| `email_verified` | BOOLEAN | NOT NULL, default `false` |
| `email_verification_otp` | VARCHAR(255) | NULL |
| `email_verification_otp_expires_at` | DATETIME | NULL |
| `password_reset_otp` | VARCHAR(255) | NULL |
| `password_reset_otp_expires_at` | DATETIME | NULL |
| `created_at` | DATETIME | NOT NULL |
| `updated_at` | DATETIME | NOT NULL |
| `deleted_at` | DATETIME | NULL |

## Rules

- Email must be unique.
- Password is stored only as BCrypt hash.
- Default status is `ACTIVE`.
- Email verification is required before login.
- OTP data is short-lived and must be cleared after successful use.
- Public registration allows only `BUYER` and `SELLER`.
- Profile columns are nullable and stay `NULL` for newly registered users until updated.
- Migration must remain backward compatible for existing rows.

# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[API-User]]
- [[ADR-Authentication-JWT]]
