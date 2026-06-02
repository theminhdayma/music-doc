# ADR - Authentication with Shared User Table and JWT

## Status

Accepted

## Context

The marketplace needs a secure auth system for Buyer and Seller registration, email verification, login, password reset, and profile access while keeping Admin accounts internal. The implementation must stay small and secure for the MVP.

## Decision

- Use a single `user_account` table for Buyer, Seller, and Admin.
- Allow public registration only for BUYER and SELLER.
- Reject public ADMIN registration.
- Store passwords as BCrypt hashes.
- Require email verification before login.
- Store verification and password-reset OTP metadata on the same user row.
- Support logout by blacklisting the current JWT until it expires.
- Issue JWT access tokens for authenticated sessions.
- Keep refresh tokens out of scope for the MVP.

## Consequences

- User persistence stays simple and centralized.
- Security rules remain consistent across the application.
- The JWT filter can authenticate requests without session state.
- Public responses must use DTOs so password hashes never leak.
- Email verification and password reset can be implemented without introducing a separate auth token table.
- Logout can remain stateless by recording revoked JWTs in memory for the token lifetime.
# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[2026-05-27_authentication-module]]
