---
type: implementation-plan
status: ready
agent: copilot
module: authentication
---

# Objective

Extend the shared user profile so authenticated users can manage richer account details beyond `fullName`, while keeping registration and auth flows backward compatible.

# Approved Spec

[[2026-05-30_user-profile-enhancement]]

# Tasks

## Task 1: Extend user entity and migration
- File: `e-commerce_music_be/src/main/resources/db/migration/V7__add_user_account_profile_columns.sql`
- File: `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/entity/User.java`
- File: `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/UserResponse.java`

## Task 2: Expand profile request validation
- File: `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/request/UpdateProfileRequest.java`
- File: `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/UserProfileService.java`

## Task 3: Update profile service and mapper
- File: `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/UserProfileService.java`
- File: `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/mapper/UserMapper.java`
- File: `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/controller/UserProfileController.java`

## Task 4: Verify registration backward compatibility
- File: `e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/service/AuthServiceTest.java`

## Task 5: Add backend tests for profile behavior
- File: `e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/service/UserProfileServiceTest.java`
- File: `e-commerce_music_be/src/test/java/com/tien/ecommerce_music_be/controller/UserProfileControllerSecurityTest.java`

## Task 6: Update docs and acceptance notes
- File: `docs/obsidian-vault/05-API/API-User.md`
- File: `docs/obsidian-vault/06-Database/Table-User.md`
- File: `docs/obsidian-vault/08-Testing/authentication-test.md`

# Expected Tests

- Registration creates users with new profile columns left `NULL`.
- `GET /api/v1/users/me` returns the new safe profile fields.
- `PUT /api/v1/users/me` updates `avatarUrl`, `phoneNumber`, `address`, `bio`, and `dateOfBirth` for the authenticated user.
- Validation rules are enforced for `fullName`, `avatarUrl`, `phoneNumber`, `address`, `bio`, and `dateOfBirth`.
- Blank or empty strings for `avatarUrl`, `phoneNumber`, `address`, and `bio` are normalized to `null`.
- Sensitive fields remain excluded from profile responses.
- Profile updates cannot modify `role`, `status`, `emailVerified`, or `password`.
- Security rules require authentication for profile endpoints.

# Commands

- `./gradlew test --tests "com.tien.ecommerce_music_be.service.UserProfileServiceTest"`
- `./gradlew test --tests "com.tien.ecommerce_music_be.controller.UserProfileControllerSecurityTest"`
- `./gradlew test`
- `./gradlew build`

# Checkpoints

- Confirm Flyway migration is backward compatible and only adds nullable columns with explicit lengths: `avatar_url VARCHAR(2048)`, `phone_number VARCHAR(20)`, `address VARCHAR(500)`, `bio VARCHAR(1000)`, `date_of_birth DATE`.
- Confirm profile DTOs and mapper expose only safe fields.
- Confirm registration still works for existing auth flows and newly created users keep the added profile fields empty.
- Confirm full backend test suite and build pass after the change.

# Related

- [[2026-05-30_user-profile-enhancement]]
- [Brainstorming](../Brainstorming/2026-05-30_user-profile-enhancement.md)
- [Spec](../Specs/2026-05-30_user-profile-enhancement.md)
- [TDD](../TDD/2026-05-30_user-profile-enhancement.md)
- [Code Review](../Code-Review/2026-05-30_user-profile-enhancement.md)
- [Retrospective](../Retrospective/2026-05-30_user-profile-enhancement.md)
