# Authentication Enhancement Brainstorming

## Problem

The existing auth module only covered register and login. The product now needs email verification, password recovery, and a basic profile page for signed-in users.

## Initial Decisions

- Keep the shared `user_account` table.
- Add `email_verified` and OTP metadata to the user row.
- Send a 6-digit verification code after registration.
- Require verification before login returns JWT.
- Support password reset through a separate OTP flow.
- Expose profile read/update through the authenticated current-user endpoint.
- Add logout that blacklists the current JWT until it expires.

## Open Questions

- Whether to add more profile fields later, such as avatar or phone number.
- Whether OTP delivery should later move from simple mail to an async queue.
- Whether token revocation should later move from in-memory storage to Redis.

# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[API-User]]
- [[Table-User]]
- [[ADR-Authentication-JWT]]