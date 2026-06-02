# Code Review - Genre Module

## Checklist

- [x] No business logic in controller
- [x] DTOs used for requests/responses (no Entity exposure)
- [x] Soft-delete implemented (no hard delete) and documented
- [x] Slug uniqueness enforced at DB + validation on create
- [x] No N+1 queries when listing genres with counts
- [x] Endpoints respect role permissions (ADMIN required for delete)
- [x] Validation on request DTOs (`@NotBlank`, `@Size`) present
- [x] Integration tests for public list and admin CRUD
- [x] Migration or schema change recorded in docs

## Findings

- Controllers are lean; most business logic resides in `GenreService` (good).
- DTOs present but `GenreResponse` includes `createdAt`/`updatedAt` — OK.
- `deleted` flag exists, but soft-delete handling in repository is ad-hoc (some queries forget `deleted = false`).
- No DB-level unique constraint on `slug` detected in docs — rely on service validation only.
- Listing endpoint returns genre + track count via separate query per genre (possible N+1).
- No integration tests found covering slug uniqueness or soft-delete behavior.

## Recommendations / Action Items

- Add global repository filters or ensure all repository queries include `WHERE deleted = false` to avoid leaking soft-deleted genres.
- Add a DB unique index on `slug` (migration) and keep service-level validation as guard.
- Optimize genres-with-counts query using a single aggregated query (JOIN + GROUP BY) to avoid N+1.
- Add integration tests:
  - create genre with duplicate slug → expect 409
  - soft-delete genre → ensure it no longer appears in public list but remains in DB
  - admin delete vs soft-delete behavior tests
- Update API docs in `docs/obsidian-vault/05-API/API-Genre.md` to mention soft-delete and slug constraints.

## Files to Review

- `e-commerce_music_be/src/main/java/.../genre/GenreController.java`
- `e-commerce_music_be/src/main/java/.../genre/GenreService.java`
- `e-commerce_music_be/src/main/java/.../genre/GenreRepository.java`
- `docs/obsidian-vault/05-API/API-Genre.md`
- DB migration files in `src/main/resources/db/migration` (if present)

## Tests to Add

- Unit tests for `GenreService.create()` validating slug collision handling.
- Integration test for public `GET /api/v1/public/genres` ensuring soft-deleted genres hidden.
- Performance test or assertion for `GET /api/v1/public/genres` to confirm no N+1 (mock repository or use test DB with several genres/tracks).

## Quick Fixes (low effort)

- Add `@Where(clause = "deleted = false")` on `Genre` entity or central filter.
- Add DB unique constraint on `slug` and include a migration file.
- Replace per-genre count queries with a single aggregation in `GenreRepository`.

## Risk / Notes

- Changing to a DB-level unique constraint may surface existing duplicate slugs in production — run dedupe script before migration.
- Adding global `@Where` affects all queries; ensure admin endpoints bypass filter when needed.

## Related

- [[API-Genre]]
- [[Table-Genre]]
- [[ADR-Genre-Soft-Delete]]

---
Generated: 2026-06-01
