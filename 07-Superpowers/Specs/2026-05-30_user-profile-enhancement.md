---
type: spec
project: ecommerce-music
module: authentication
status: draft
created: 2026-05-30
---

# Problem

The current profile update flow is too limited because it only allows updating `fullName`. Users need a richer profile so the account page feels complete and the update experience is more useful.

# Goal

Extend the shared user profile model so authenticated users can view and update additional optional fields without changing the existing authentication flow or backend API contract style.

# Scope

In scope:
- Extend the `user_account` entity with optional profile fields.
- Return the new safe profile fields from `GET /api/v1/users/me`.
- Allow updating the new fields through `PUT /api/v1/users/me`.
- Keep registration behavior backward compatible by setting the new fields to `null`.
- Add a backward-compatible database migration for the new columns.

Out of scope:
- Uploading avatar files in the profile service.
- Changing the login, register, OTP, or logout flows.
- Adding public user profile pages.
- Adding image processing or Cloudinary upload orchestration in the backend profile service.

# User Flow

1. User registers normally.
2. System creates the `user_account` row with the new profile columns left `NULL`.
3. User opens the profile page after login.
4. System reads the current user profile from the authenticated JWT identity.
5. User edits optional profile fields such as avatar URL, phone number, address, bio, and date of birth.
6. System validates the input, persists the changes, and returns the updated profile.

# Technical Design

Use the existing auth/profile module as the boundary and extend the shared `user_account` table rather than introducing a separate profile table.

The profile update request should continue to use the authenticated user from JWT, not a client-provided id. The new fields are optional and should remain `NULL` unless the user explicitly updates them.

`avatarUrl` stores the final Cloudinary URL only. The backend profile service does not handle upload or signed URL generation for this feature.

# API

## GET /api/v1/users/me

Authenticated endpoint that returns the current user's safe profile data.

Returned profile data should now include:
- `fullName`
- `avatarUrl`
- `phoneNumber`
- `address`
- `bio`
- `dateOfBirth`

## PUT /api/v1/users/me

Authenticated endpoint that updates the current user's profile.

Request payload should allow:
- `fullName`
- `avatarUrl`
- `phoneNumber`
- `address`
- `bio`
- `dateOfBirth`

Rules:
- The endpoint must use the JWT-authenticated user.
- The request must not accept a user id.
- Only allowed profile fields may be updated.
- Sensitive fields like password, OTP values, and status must never be exposed.

# Database

Update `user_account` with the following nullable columns and no default values:
- `avatar_url` VARCHAR
- `phone_number` VARCHAR
- `address` VARCHAR
- `bio` VARCHAR(1000)
- `date_of_birth` DATE

Migration rules:
- Must be backward compatible.
- Existing rows must remain valid.
- New columns must be nullable.
- No default values should be introduced.

# Edge Cases

- `dateOfBirth` must reject invalid date formats.
- `bio` must be limited to 1000 characters.
- Empty values should be handled consistently during update requests.
- Existing users with all new profile columns as `NULL` must still be able to load and update their profile.
- `avatarUrl` must only store the final URL string, not upload metadata.
- Password, OTP, and other sensitive account fields must never appear in profile responses.

# Acceptance Criteria

- [ ] Registration creates users with the new profile columns set to `NULL`.
- [ ] `GET /api/v1/users/me` returns the new safe profile fields.
- [ ] `PUT /api/v1/users/me` updates the new fields for the authenticated user.
- [ ] `avatarUrl` stores only the final Cloudinary URL.
- [ ] Database migration is backward compatible and adds only nullable columns with no defaults.
- [ ] Existing users continue to work without manual data migration.
- [ ] Sensitive fields remain excluded from profile responses.

# Related

- [[2026-05-30_user-profile-enhancement]]
- [API User Profile](../../05-API/API-User.md)
- [Authentication Flow](../../01-Business/User-Flows/flow-authentication.md)
- [Table: user_account](../../06-Database/Table-User.md)
- [SRS Authentication](../../02-SRS/SRS-Authentication.md)
- [TDD](../TDD/2026-05-30_user-profile-enhancement.md)
- [Code Review](../Code-Review/2026-05-30_user-profile-enhancement.md)
- [Retrospective](../Retrospective/2026-05-30_user-profile-enhancement.md)
