# Admin API

## Purpose

Admin API provides the top-level admin dashboard statistics endpoint and serves as the entry point
for admin-facing management surfaces in the frontend.

The detailed CRUD endpoints for users, genres, and music are documented in:

- [[API-User]]
- [[API-Genre]]
- [[API-Music]]
- [[API-Order]]

## Frontend Integration

- Admin dashboard route: `/admin`
- Admin user management route: `/admin/users`
- Admin genre management route: `/admin/genres`
- Admin music management route: `/admin/musics`
- Admin order management route: `/admin/orders`
- Frontend modules live under `src/features/admin-dashboard/`, `src/features/admin-users/`, `src/features/admin-genre-management/`, `src/features/admin-music-management/`, and `src/features/admin-order-management/`.

## GET /api/v1/admin/dashboard

Admin-only dashboard statistics endpoint.

### Response

Returns `ApiResponse<AdminDashboardStatsResponse>` with:

- `totalUsers`
- `totalBuyers`
- `totalSellers`
- `totalMusics`
- `publishedMusics`
- `totalOrders`
- `totalRevenue`

### Rules

- `totalOrders` counts only successful paid orders.
- `totalRevenue` sums only successful paid orders.
- User and music counts exclude soft-deleted records.
- Non-admin users must receive `403 Forbidden`.

## Admin Response Contract

- Responses use `ApiResponse<T>` wrappers.
- Dashboard values are aggregation results and should never expose raw entities.
- Admin dashboard should remain read-only from the frontend perspective.

## Related

- [[SRS-Admin]]
- [[User-Roles]]
- [[System-Architecture]]
- [[API-User]]
- [[API-Genre]]
- [[API-Music]]
- [[API-Order]]
