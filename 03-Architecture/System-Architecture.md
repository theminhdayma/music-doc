# System Architecture

## Overview

The system uses a Spring Boot backend, a Next.js frontend, MySQL persistence, Spring Security with JWT, and Flyway for schema versioning.

## Authentication Flow

- Public registration hits `/api/v1/auth/register` and sends an email verification OTP.
- Email verification hits `/api/v1/auth/verify-email` and returns a JWT access token.
- Login hits `/api/v1/auth/login` only after the account is verified.
- Password reset uses `/api/v1/auth/forgot-password` and `/api/v1/auth/reset-password`.
- Profile read/update uses `/api/v1/users/me` for the authenticated user.
- Logout uses `/api/v1/auth/logout` and blacklists the current access token until expiry.
- JWT filter populates the security context for protected endpoints.
- User roles are enforced at the HTTP layer and with method security.

## Genre Module Flow

- Public clients call the public genre endpoint.
- JWT-authenticated users with BUYER or SELLER roles can also read active genres.
- ADMIN users access protected management endpoints under `/api/v1/admin/genres`.
- Soft-deleted genres remain in the database but are excluded from public API responses.

## Music Module Flow

- Sellers submit multipart music uploads through seller routes.
- Cloudinary stores the thumbnail and audio assets.
- Public endpoints expose only published music.
- Seller routes are restricted by role and ownership checks.
- Admin routes can hide or delete any track.

## Library Module Flow

- Buyers access their purchased library through authenticated buyer endpoints.
- Ownership is derived from `purchased_library`, not from public music visibility.
- Signed download URLs are generated only after ownership is verified.
- The buyer library also feeds the frontend player ownership cache, so full playback can be unlocked immediately after a purchase.

## Admin Dashboard Flow

- Admin dashboard aggregates counts for users, musics, orders, and revenue.
- Only successful paid orders are counted in revenue totals.
- Admin dashboard data is read-only and powers the `/admin` overview page.

## Cross-Cutting Rules

- Controllers stay thin and delegate to services.
- Services contain business logic and validation orchestration.
- DTOs are used for request and response boundaries.
- MapStruct handles entity to DTO mapping.
- External integration points such as email delivery are isolated behind services for testability.
- Token revocation is handled with a lightweight in-memory blacklist for the MVP.

# Related

- [[Backend-Architecture]]
- [[Frontend-Architecture]]
- [[Database-Design]]
