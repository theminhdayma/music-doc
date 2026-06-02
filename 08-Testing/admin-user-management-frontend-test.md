# Admin User Management Frontend Test

## Scope

- Admin-only `/admin/users` page
- Server-driven pagination through `page`, `keyword`, `role`, and `status` query params
- Detail drawer with enable/disable actions

## Manual Test Checklist

1. Open the Admin dropdown and navigate to `Admin Users`.
2. Confirm the page redirects non-admin users away from the route.
3. Verify the table loads paged results from the admin users API.
4. Search by full name or email and confirm the URL updates with `keyword`.
5. Filter by role and status and confirm the URL updates with the selected values.
6. Open a user detail drawer and confirm the full profile fields render.
7. Enable and disable a user from both the table row and the drawer.
8. Confirm the current admin user cannot disable their own account.
9. Confirm the page shows an empty state when the filter set returns no users.

## Regression Notes

- Keep the page using URL state so browser back/forward preserves pagination and filters.
- Do not add create, edit, or delete flows on this page.