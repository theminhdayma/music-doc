# API Music

## Seller

### POST /api/v1/seller/musics
Create music with thumbnail and audio upload.

### PUT /api/v1/seller/musics/{id}
Update own music.

### DELETE /api/v1/seller/musics/{id}
Soft delete own music.

### GET /api/v1/seller/musics/{id}
Get detail of the authenticated seller's own music for editing.

### GET /api/v1/seller/musics/me
Return seller's music list.
Supports optional `keyword` search on title and optional `status` filter.

### GET /api/v1/seller/dashboard
Return authenticated seller statistics for total, published, draft, and hidden musics.

### GET /api/v1/seller/dashboard/statistics
Seller Dashboard UI endpoint for overview cards.
Returns total musics, published musics, draft musics (and hidden if available).

### GET /api/v1/seller/revenue
Return authenticated seller revenue statistics based on successful sales only.

### GET /api/v1/seller/dashboard/revenue
Seller Dashboard UI endpoint for revenue cards.

### GET /api/v1/seller/top-musics
Return the authenticated seller's top 3 performing musics based on successful sales.

### GET /api/v1/seller/dashboard/top-selling
Seller Dashboard UI endpoint for top-selling tracks section.

### GET /api/v1/seller/dashboard/recent-sales
Return the authenticated seller's latest 10 successful sales ordered by purchase time descending.
Uses snapshot fields from `order_items` so historical sales stay accurate even when music is updated or soft-deleted.
Returns `ApiResponse<List<SellerRecentSaleResponse>>`.

## Frontend Integration Note

- Seller Dashboard page route: `/seller`
- Feature module: `src/features/seller-dashboard/`
- API adapter currently supports both dashboard-prefixed endpoints and existing fallback endpoints to keep compatibility during rollout.

## Public

### GET /api/v1/public/musics
List published music with pagination, genre filter, and search.

### GET /api/v1/public/musics/{slug}
Get public music detail by slug.

### GET /api/v1/public/musics/search
Search published music by title.

### GET /api/v1/public/sellers/{sellerId}/musics
List published music for a seller profile.

## Admin

### GET /api/v1/admin/musics
Return paged music records for admin management.
Supports optional `keyword` search on title and optional `status` filter.

### GET /api/v1/admin/musics/{id}
Return full music detail for admin review.

### PATCH /api/v1/admin/musics/{id}/hide
Hide any music.

### DELETE /api/v1/admin/musics/{id}
Soft delete any music.

# Related

- [[2026-05-27_music-module]]
- [[Table-Music]]
- [[ADR-Music-Cloudinary]]
