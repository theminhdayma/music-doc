---
type: implementation-plan
status: ready
agent: copilot
module: authentication
---

# Objective

Implement email verification, password reset, and current-user profile endpoints on top of the existing JWT auth module.

# Approved Spec

[[2026-05-28_authentication-enhancement]]

# Tasks

## Task 1: Extend user schema
- File: `src/main/resources/db/migration/V6__add_user_account_security_columns.sql`
- File: `src/main/java/com/tien/ecommerce_music_be/entity/User.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/response/UserResponse.java`

## Task 2: Add OTP and profile DTOs
- File: `src/main/java/com/tien/ecommerce_music_be/dto/request/ForgotPasswordRequest.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/request/ResetPasswordRequest.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/request/ResendVerificationRequest.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/request/VerifyEmailRequest.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/request/UpdateProfileRequest.java`
- File: `src/main/java/com/tien/ecommerce_music_be/dto/response/VerificationResponse.java`

## Task 3: Add mail service
- File: `src/main/java/com/tien/ecommerce_music_be/service/MailService.java`
- File: `src/main/java/com/tien/ecommerce_music_be/service/MailServiceImpl.java`

## Task 4: Extend auth service and controller
- File: `src/main/java/com/tien/ecommerce_music_be/service/AuthService.java`
- File: `src/main/java/com/tien/ecommerce_music_be/controller/AuthController.java`
- File: `src/main/java/com/tien/ecommerce_music_be/config/SecurityConfig.java`
- File: `src/main/java/com/tien/ecommerce_music_be/security/JwtAuthenticationFilter.java`
- File: `src/main/java/com/tien/ecommerce_music_be/security/TokenBlacklistService.java`
- File: `src/main/java/com/tien/ecommerce_music_be/security/InMemoryTokenBlacklistService.java`

## Task 5: Add profile module
- File: `src/main/java/com/tien/ecommerce_music_be/service/UserProfileService.java`
- File: `src/main/java/com/tien/ecommerce_music_be/controller/UserProfileController.java`

## Task 6: Update docs and tests
- File: `src/test/java/com/tien/ecommerce_music_be/service/AuthServiceTest.java`
- File: `src/test/resources/application.properties`
- File: `docs/obsidian-vault/05-API/API-Auth.md`
- File: `docs/obsidian-vault/05-API/API-User.md`
- File: `docs/obsidian-vault/08-Testing/authentication-test.md`

# Checkpoints

- Run focused auth tests.
- Run full backend test suite.
- Confirm Flyway migration is portable and context loads successfully.

# Related

- [[2026-05-28_authentication-enhancement]]