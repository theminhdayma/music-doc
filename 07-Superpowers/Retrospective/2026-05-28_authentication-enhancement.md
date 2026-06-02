---
type: retrospective
project: ecommerce-music
module: authentication
created: 2026-05-28
---

# Retrospective - Authentication Enhancement

## What Worked

- Keeping OTP metadata on the existing user row kept the implementation simple.
- Returning JWT only after verification matched the security requirement cleanly.
- Splitting profile logic into a separate service kept the auth controller focused.
- A small in-memory blacklist was enough for logout without introducing DB complexity.

## What To Improve

- Move mail delivery to an async worker if OTP volume increases.
- Add dedicated integration tests for the full verify-email and reset-password HTTP flows.
- Consider adding richer profile fields later only if the product needs them.
- Consider replacing the in-memory blacklist with Redis if logout needs to survive restarts.

## Lessons Learned

- Third-party configuration should always have explicit test defaults.
- Flyway migrations need to stay database-portable and avoid hardcoded database switches.
- Auth features become easier to evolve when account state, recovery state, and profile state are clearly separated in the model.
- Stateless auth still needs a revocation strategy when logout is required.

# Related

- [[2026-05-28_authentication-enhancement]]