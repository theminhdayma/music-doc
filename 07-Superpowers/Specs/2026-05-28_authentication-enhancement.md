---
type: spec
project: ecommerce-music
module: authentication
status: approved
created: 2026-05-28
---

# Problem

The authentication module needs a secure user onboarding and account recovery flow beyond simple register/login.

# Goal

Provide email OTP verification, JWT login only for verified accounts, password reset by OTP, logout with token revocation, and current-user profile view/update.

# Scope

In scope: public register with OTP email, resend verification, verify email, forgot password, reset password, logout, current-user profile read/update.

Out of scope: refresh tokens, social login, public admin registration, multi-factor authentication beyond email OTP.

# User Flow

1. User registers as BUYER or SELLER.
2. System creates an unverified account and sends an OTP.
3. User verifies the email and receives a JWT access token.
4. User logs in with email and password.
5. Verified users can request password reset and update their own profile.
6. Signed-in users can logout and invalidate the current access token.

# Technical Design

Use the shared `user_account` table, store verification/reset OTP state on the same row, keep services as the business boundary, and use DTOs for all auth and profile responses.

# API

- `POST /api/v1/auth/register`
- `POST /api/v1/auth/verify-email`
- `POST /api/v1/auth/resend-verification`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/forgot-password`
- `POST /api/v1/auth/reset-password`
- `GET /api/v1/users/me`
- `PUT /api/v1/users/me`
- `POST /api/v1/auth/logout`

# Database

- `user_account(id, email, password_hash, role, status, email_verified, email_verification_otp, email_verification_otp_expires_at, password_reset_otp, password_reset_otp_expires_at, created_at, updated_at, deleted_at)`

# Edge Cases

- Duplicate email registration.
- Login before verification.
- Invalid or expired OTP.
- Password reset requested for an unverified account.
- Profile update without authentication.
- Reusing a token after logout.

# Acceptance Criteria

- [x] Register sends OTP and returns verification response.
- [x] Email verification returns JWT.
- [x] Login rejects unverified accounts.
- [x] Password reset works through OTP.
- [x] Profile read/update works for authenticated users.
- [x] Logout invalidates the current JWT.

# Related

- [[2026-05-28_authentication-enhancement]]