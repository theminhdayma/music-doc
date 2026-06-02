# Authentication Module Brainstorming

## Problem

The platform needs a minimal authentication module for public Buyer and Seller registration, login, and JWT-based access control.

## Initial Decisions

- Use one shared `user_account` table for Buyer, Seller, and Admin.
- Public registration only allows Buyer and Seller.
- Store passwords as BCrypt hashes.
- Return JWT access tokens only; no refresh token for MVP.
- Keep the response DTO free of password data.

## Open Questions

- Whether to add profile fields like display name now or later.
- Whether admin accounts will be seeded manually or via a separate internal flow.
# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[Table-User]]
- [[ADR-Authentication-JWT]]
