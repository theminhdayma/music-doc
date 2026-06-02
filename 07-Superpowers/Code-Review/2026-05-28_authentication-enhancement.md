# Code Review - Authentication Enhancement

## Summary

The authentication module now supports email OTP verification, password recovery, and current-user profile management on top of JWT access tokens.

## Checklist

- [x] No business logic in controller
- [x] Register does not return JWT before verification
- [x] Verification and reset OTPs are time-limited
- [x] Password is BCrypt-hashed
- [x] Profile endpoints use authenticated user identity
- [x] DTOs do not expose password or OTP internals
- [x] Service-layer tests pass
- [x] Logout revokes the current JWT until expiry

## Notes

- Shared `user_account` remains the single source of truth for account and recovery state.
- Test profile includes explicit JWT, mail, and Cloudinary placeholders so the Spring context starts cleanly.

# Related

- [[2026-05-28_authentication-enhancement]]