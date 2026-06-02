# SRS - Authentication

## Goals

- Allow users to register as `BUYER` or `SELLER`.
- Require email OTP verification after registration.
- Allow users to login using email and password after verification.
- Allow users to request a password reset OTP and update their password.
- Allow authenticated users to view and update their own profile.
- Allow authenticated users to logout and invalidate the current JWT.
- Protect endpoints using JWT access token authentication.

## Functional Requirements

- The system must enforce unique email addresses.
- The system must encrypt passwords using BCrypt before storage.
- The system must not expose password data in API responses.
- The system must set default account status to `ACTIVE`.
- The system must reject public admin registration.
- The system must store `email_verified` as `false` when a user is newly registered.
- The system must send a 6-digit verification OTP to the user's email after registration.
- The system must reject login until the email is verified.
- The system must support password reset through a time-limited OTP.
- The system must allow authenticated users to view and update their own profile.
- The system must allow authenticated users to update allowed profile fields: full name, avatar URL, phone number, address, bio, and date of birth.
- The system must keep profile fields nullable for newly registered users until explicitly updated.
- The system must support logout by revoking the current access token until it expires.

## Non-Goals

- Refresh token support.
- Social login.

## Acceptance Criteria

- Register sends a verification OTP and returns a verification response.
- Verifying the email returns a JWT access token.
- Login returns a JWT access token for verified credentials.
- Duplicate email is rejected.
- Admin role registration is rejected.
- Password reset OTP flow works for verified users.
- Authenticated users can update their own profile.
- Authenticated users can view and update the expanded safe profile fields.
- Newly registered users keep profile fields empty until they update them.
- Logout invalidates the current JWT.

# Related

- [[API-Auth]]
- [[API-User]]
- [[ADR-Authentication-JWT]]
- [[User-Roles]]
