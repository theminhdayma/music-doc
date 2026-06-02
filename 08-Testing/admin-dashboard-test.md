# Admin Dashboard Test Checklist

## Service and controller coverage

- [x] Admin dashboard returns total users, buyers, sellers, musics, published musics, orders, and revenue.
- [x] Admin dashboard counts only successful paid orders for totalOrders and totalRevenue.
- [x] Admin dashboard blocks non-admin access.
- [x] Admin dashboard service aggregates counts from repository queries.
- [x] Admin dashboard order stats projection returns zero-safe aggregates.

## Executed test suites

- [x] `OrderRepositoryDashboardStatsTest`
- [x] `AdminDashboardServiceTest`
- [x] `AdminDashboardControllerSecurityTest`

# Related

- [[API-Admin]]
- [[Table-User]]

# Admin Genre Test Checklist

## Service and controller coverage

- [x] Admin genres list returns paged results.
- [x] Admin genres list defaults to 5 items per page.
- [x] Admin genres list filters by keyword on genre name.
- [x] Admin genres list excludes soft-deleted genres.
- [x] Admin delete sets `active=false` and `deletedAt`.
- [x] Security test: admin genres blocked for non-ADMIN.

## Executed test suites

- [x] `GenreServiceTest`
- [x] `GenreControllerSecurityTest`