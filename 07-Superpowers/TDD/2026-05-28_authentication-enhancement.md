# Authentication Enhancement TDD

## RED

- Register should fail to return a JWT before email verification.
- Verify-email should fail with invalid or expired OTP.
- Login should fail for unverified users.
- Forgot-password should send a reset OTP only for verified users.
- Reset-password should fail with invalid OTP.
- Logout should revoke the current JWT.
- Profile update should require authentication.

## GREEN

- Added service tests for register, verify-email, login, and invalid login.
- Added service test for logout token revocation.
- Added profile service/controller coverage through the full backend suite.

## Verify

- `./gradlew test --tests com.tien.ecommerce_music_be.service.AuthServiceTest`
- `./gradlew test`

# Related

- [[2026-05-28_authentication-enhancement]]