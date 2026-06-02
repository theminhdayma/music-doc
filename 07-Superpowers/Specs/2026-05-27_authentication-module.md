---
type: spec
project: ecommerce-music
module: authentication
status: approved
created: 2026-05-27
---

# Problem

The marketplace currently has no authentication layer. Users need a secure way to register and login before accessing role-protected features.

# Goal

Provide Buyer and Seller registration, email/password login, JWT access token issuance, and safe account responses without exposing passwords.

# Scope

In scope: public Buyer/Seller registration, login, unique email validation, BCrypt password hashing, JWT access token auth, default ACTIVE status.

Out of scope: refresh tokens, password reset, social login, public admin registration.

# User Flow

1. User registers with email, password, and role BUYER or SELLER.
2. System validates unique email and role.
3. System stores a BCrypt password hash and ACTIVE status.
4. User logs in with email and password.
5. System returns a JWT access token and safe user profile.

# Technical Design

Use a shared `user_account` table, Spring Security, JWT, a `UserDetailsService` implementation, a JWT filter, DTOs, MapStruct, and a service layer for business rules.

# API

`POST /api/v1/auth/register`
`POST /api/v1/auth/login`

# Database

- `user_account(id, email, password_hash, role, status, created_at, updated_at, deleted_at)`

# Edge Cases

- Duplicate email.
- Admin registration attempt.
- Invalid credentials.
- Missing or invalid role.

# Acceptance Criteria

- [x] Buyer and Seller can register.
- [x] Login returns JWT access token.
- [x] Password is BCrypt-hashed.
- [x] Password is not exposed in response.
- [x] Admin registration is rejected.

# Related

- [[2026-05-27_authentication-module]]