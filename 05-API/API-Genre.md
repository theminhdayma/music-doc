# API Genre

## Public

### GET /api/v1/genres/public
Returns active, non-deleted genres.

## Admin

### GET /api/v1/admin/genres
Returns paged genres for admin management.

### Query params

- `keyword` - optional partial search by genre name, case-insensitive
- Standard Spring `Pageable` params - `page`, `size`, `sort`

### Response

Returns `ApiResponse<PageResponse<GenreResponse>>` with genres where `deletedAt IS NULL`.

### Rules

- Default page size is 5.
- Default sorting is `createdAt DESC`.
- Soft-deleted genres are excluded.

### POST /api/v1/admin/genres
Creates a genre.

### PUT /api/v1/admin/genres/{id}
Updates a genre and can activate or deactivate it.

### DELETE /api/v1/admin/genres/{id}
Soft deletes a genre by setting `active=false` and `deletedAt` to the current timestamp.
# Related

- [[SRS-Genre]]
- [[Table-Genre]]
- [[ADR-Genre-Soft-Delete]]
