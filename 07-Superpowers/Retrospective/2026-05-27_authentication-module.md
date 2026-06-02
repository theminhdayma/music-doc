---
type: retrospective
project: ecommerce-music
module: authentication
created: 2026-05-28
---

# Retrospective - Authentication Module

## What Worked

- Shared `user_account` table kept the MVP simple.
- JWT access token flow integrated cleanly with Spring Security.
- DTOs and MapStruct prevented password exposure in API responses.
- Service-layer validation kept controllers thin.

## What To Improve

- Add a dedicated integration test for the full register/login/security path.
- Consider seeding internal admin accounts instead of relying on manual creation.
- Revisit refresh token support only if the product scope grows.

## Lessons Learned

- Soft delete and DTO boundaries are important even for auth data.
- The HTTP security layer should explicitly whitelist public auth routes.
- Keeping auth logic in the service layer makes policy changes easier.
# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[Table-User]]
- [[ADR-Authentication-JWT]]
