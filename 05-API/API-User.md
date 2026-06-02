# API User Profile

## GET /api/v1/users/me

Authenticated endpoint to read the current user's profile.

### Response

Returns `ApiResponse<UserResponse>` with the current user's safe profile fields:
- `fullName`
- `avatarUrl`
- `phoneNumber`
- `address`
- `bio`
- `dateOfBirth`

## PUT /api/v1/users/me

Authenticated endpoint to update the current user's profile.

### Request

```json
{
	"fullName": "Buyer One",
	"avatarUrl": "https://res.cloudinary.com/...",
	"phoneNumber": "+84901234567",
	"address": "123 Music Street",
	"bio": "Music producer and content creator.",
	"dateOfBirth": "1995-08-20"
}
```

### Response

Returns `ApiResponse<UserResponse>` with the updated profile.

## Rules

- The endpoint must use the authenticated user from JWT, not a client-provided user id.
- Only allowed profile fields may be updated.
- Empty strings and blank strings for `avatarUrl`, `phoneNumber`, `address`, and `bio` are normalized to `null`.
- `fullName` is required.
- `avatarUrl` must be at most 2048 characters.
- `phoneNumber` must be at most 20 characters.
- `address` must be at most 500 characters.
- `bio` must be at most 1000 characters.
- `dateOfBirth` must be a past date.
- Password and OTP fields must never be exposed.
- `avatarUrl` stores only the final Cloudinary URL value; the profile service does not upload files.

# Admin User Management

## GET /api/v1/admin/users

Admin-only paged endpoint for the admin user table.

### Query params

- `keyword` - search by email or full name
- `role` - `ADMIN`, `SELLER`, or `BUYER`
- `status` - `ACTIVE` or `INACTIVE`
- Standard Spring `Pageable` params - `page`, `size`, `sort`

### Response

Returns `ApiResponse<PageResponse<AdminUserSummaryResponse>>` with summary fields for admin table rendering:
- `id`
- `email`
- `fullName`
- `avatarUrl`
- `role`
- `status`
- `emailVerified`
- `createdAt`

## GET /api/v1/admin/users/{id}

Admin-only endpoint to read the full user profile.

### Response

Returns `ApiResponse<AdminUserDetailResponse>` with all safe user detail fields:
- `id`
- `email`
- `fullName`
- `avatarUrl`
- `phoneNumber`
- `address`
- `bio`
- `dateOfBirth`
- `role`
- `status`
- `emailVerified`
- `createdAt`
- `updatedAt`
- `deletedAt`

## PATCH /api/v1/admin/users/{id}/status

Admin-only endpoint to change user status.

### Request

```json
{
	"status": "INACTIVE"
}
```

### Rules

- `status` is required.
- Allowed values are `ACTIVE` and `INACTIVE`.
- Admin cannot modify their own account status.
- Soft-deleted users are excluded from all admin lookups.
- Filters use Specification/Criteria API, not hard-coded repository methods.

# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[Table-User]]