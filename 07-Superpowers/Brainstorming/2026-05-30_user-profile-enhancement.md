# User Profile Enhancement Brainstorming

## Problem

Current `updateProfile` only updates `fullName`, which makes the profile feature feel too limited for real user account management.

## Initial Decisions

- Scope this as an authentication/profile enhancement for authenticated users.
- Extend the shared `user_account` entity with optional profile fields.
- On registration, keep the new profile fields `null` by default.
- Allow values to be set only through the profile update flow.
- Keep the current update profile endpoint and expand its request/response DTOs.
- Use the authenticated user from JWT; do not accept a user id from the client.
- `avatarUrl` stores the final Cloudinary URL only; the profile service does not handle upload logic.

## Proposed Fields

- `avatarUrl`
- `phoneNumber`
- `address`
- `bio`
- `dateOfBirth`

## Database Impact

- Add nullable columns to `user_account` with no default values.
- Create a backward-compatible migration for the new columns.
- Preserve existing rows by leaving the new columns `NULL`.

## API Impact

- `GET /api/v1/users/me` should return the new safe profile fields.
- `PUT /api/v1/users/me` should allow updating the new fields.
- Registration response should still omit sensitive fields and leave new profile fields empty.

## Edge Cases

- Invalid date format for `dateOfBirth` should be rejected by validation.
- `bio` should be limited to 1000 characters.
- Empty strings should be handled consistently, either by rejecting them or normalizing them to `null`.
- Existing users without values should still be able to use the profile endpoint normally.
- Password, OTP, and other security-sensitive fields must never be exposed.

## Open Questions

- Whether `phoneNumber` needs format validation or only max length validation.
- Whether `address` should be a single free-text field or later split into structured address parts.
- Whether the frontend should show these fields immediately in profile pages or wait for a separate UI task.

## Related

- [API User Profile](../../05-API/API-User.md)
- [Authentication Flow](../../01-Business/User-Flows/flow-authentication.md)
- [Table: user_account](../../06-Database/Table-User.md)
- [SRS Authentication](../../02-SRS/SRS-Authentication.md)
- [TDD](../TDD/2026-05-30_user-profile-enhancement.md)
- [Code Review](../Code-Review/2026-05-30_user-profile-enhancement.md)
- [Retrospective](../Retrospective/2026-05-30_user-profile-enhancement.md)
