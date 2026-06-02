# Code Review - Authentication Module

## Summary

The authentication module now supports Buyer and Seller registration, email/password login, BCrypt hashing, JWT access tokens, and safe DTO responses.

## Checklist

- [x] No business logic in Controller
- [x] DTOs do not expose password
- [x] Request DTOs use validation annotations
- [x] `ApiResponse<T>` wrapper is used
- [x] Global exception handler covers auth and validation errors
- [x] JWT claims include userId and role
- [x] Spring Security protects non-public endpoints
- [x] Unit tests added for `AuthService`

## Notes

- Admin registration is blocked in the service layer.
- Malformed JWTs are treated as unauthenticated requests.
- Schema migration file is present for the new shared account table.
# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[Table-User]]
- [[ADR-Authentication-JWT]]
