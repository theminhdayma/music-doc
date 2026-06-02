---
type: tdd
project: ecommerce-music
module: authentication
created: 2026-05-30
---

# TDD - User Profile Enhancement

## RED Cases

- Registration should create users with the new profile columns left `NULL`.
- `GET /api/v1/users/me` should return the expanded safe profile fields.
- `PUT /api/v1/users/me` should update avatar URL, phone number, address, bio, and date of birth.
- Blank profile strings should be normalized to `null`.
- Profile updates should not allow `role`, `status`, `emailVerified`, or `password` changes.

## GREEN Target

- Add the minimal entity, DTO, service, migration, and mapper changes to satisfy the profile tests.

## Verify

- `./gradlew test --tests "com.tien.ecommerce_music_be.service.UserProfileServiceTest"`
- `./gradlew test --tests "com.tien.ecommerce_music_be.controller.UserProfileControllerSecurityTest"`
- `./gradlew test`

# Related

- [[2026-05-30_user-profile-enhancement]]
- [API User Profile](../../05-API/API-User.md)
- [Table: user_account](../../06-Database/Table-User.md)
- [SRS Authentication](../../02-SRS/SRS-Authentication.md)