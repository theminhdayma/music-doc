# API Auth

## POST /api/v1/auth/register

Public endpoint for Buyer and Seller registration.

### Request

```json
{
	"email": "buyer@example.com",
	"password": "StrongPass123",
	"role": "BUYER"
}
```

### Response

Returns `ApiResponse<VerificationResponse>` with the email and OTP expiry window.

### Rules

- Email must be unique.
- Role is required.
- Role cannot be `ADMIN`.
- Default account status is `ACTIVE`.
- User starts with `emailVerified = false`.

## POST /api/v1/auth/verify-email

Public endpoint for email verification with 6-digit OTP.

### Request

```json
{
	"email": "buyer@example.com",
	"otp": "123456"
}
```

### Response

Returns `ApiResponse<AuthResponse>` with JWT access token and user profile.

## POST /api/v1/auth/resend-verification

Public endpoint to resend the verification OTP.

### Request

```json
{
	"email": "buyer@example.com"
}
```

### Response

Returns `ApiResponse<VerificationResponse>`.

## POST /api/v1/auth/login

Public endpoint for login with email and password after email verification.

### Request

```json
{
	"email": "buyer@example.com",
	"password": "StrongPass123"
}
```

### Response

Returns `ApiResponse<AuthResponse>` with JWT access token and user profile.

## POST /api/v1/auth/forgot-password

Public endpoint to send a password reset OTP.

### Request

```json
{
	"email": "buyer@example.com"
}
```

## POST /api/v1/auth/reset-password

Public endpoint to reset password after OTP verification.

### Request

```json
{
	"email": "buyer@example.com",
	"otp": "123456",
	"newPassword": "NewStrongPass123"
}
```

## POST /api/v1/auth/logout

Authenticated endpoint to invalidate the current access token.

### Request

Send the current JWT in the `Authorization` header.

### Response

Returns `ApiResponse<Void>` when the token is blacklisted until expiry.

## Error Cases

- `400 Bad Request` for invalid input
- `400 Bad Request` for invalid or expired OTP
- `401 Unauthorized` for invalid credentials
- `401 Unauthorized` for revoked or reused token after logout
- `403 Forbidden` for public admin registration attempt
- `409 Conflict` for duplicate email

# Related

- [[SRS-Authentication]]
- [[API-User]]
- [[ADR-Authentication-JWT]]
- [[Table-User]]
