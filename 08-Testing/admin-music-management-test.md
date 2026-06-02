# Admin Music Management Test

## Scope

- Admin-only `/admin/musics` page
- Server-driven pagination through `page`, `keyword`, and `status`
- Music detail drawer with hide and delete confirmations

## Manual Test Checklist

1. Open the Admin dropdown and navigate to `Admin Music`.
2. Confirm the page redirects non-admin users away from the route.
3. Verify the table loads paged results from the admin music API.
4. Search by title and confirm the URL updates with `keyword`.
5. Filter by `PUBLISHED`, `DRAFT`, and `HIDDEN` and confirm the list updates.
6. Open a music preview drawer and confirm the full metadata renders.
7. Hide a music item and confirm the list refreshes.
8. Delete a music item and confirm the list refreshes after the confirmation dialog.
9. Confirm the page shows an empty state when the filter set returns no music.

## Regression Notes

- Keep the page using URL state so browser back/forward preserves pagination and filters.
- Do not hardcode any music rows or preview content.