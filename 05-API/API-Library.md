# API Library

## Purpose

Library API serves the buyer's purchased music vault.
It exposes the buyer-owned music list and signed download URL generation.

## Frontend Integration

- Buyer library route: `/library`
- Buyer library page feature: `src/features/library/`
- Persistent player refreshes ownership from the buyer library endpoint.
- Signed download flow is triggered from the library page and validated server-side.

## GET /api/v1/buyer/library

Authenticated buyer endpoint to read the purchased library with pagination.

### Response

Returns `ApiResponse<PageResponse<PurchasedLibraryResponse>>` with purchased library rows.

### PurchasedLibraryResponse

Typical fields returned for UI rendering:

- `musicId`
- `title`
- `artistName`
- `thumbnailUrl`
- `purchasedAt`

### Rules

- BUYER only.
- List must be filtered by the authenticated buyer from JWT.
- Download access is derived from ownership, not from cart or public music access.

## GET /api/v1/buyer/library/{musicId}/download

Authenticated buyer endpoint that returns a signed download URL for an owned music track.

### Response

Returns `ApiResponse<DownloadUrlResponse>` with:

- `musicId`
- `downloadUrl`
- `expiresAt`

### Rules

- BUYER only.
- The requested music must exist in the buyer's purchased library.
- The response must contain a time-limited signed URL.
- Raw permanent audio URLs must never be exposed directly.

## Related

- [[SRS-Order]]
- [[SRS-Payment]]
- [[Table-Order]]
- [[API-Order]]
- [[API-Payment]]
- [[Frontend-Architecture]]