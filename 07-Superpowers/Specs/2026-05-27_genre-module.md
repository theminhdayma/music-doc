---
type: spec
project: ecommerce-music
module: genre
status: approved
created: 2026-05-27
---

# Problem
Music tracks need a stable genre taxonomy so users can browse and filter content consistently.

# Goal
Provide a secure genre management module with public read access to active genres and admin-only write access.

# Scope
- Create, update, deactivate, and soft delete genres for ADMIN.
- Public, BUYER, and SELLER users can only read active, non-deleted genres.
- Do not expose JPA entities directly.

# User Flow
- Public users call the public genre endpoint and see active genres only.
- Admin users manage genres from the admin endpoints.
- Deleted genres remain in the database but are excluded from public APIs.

# Technical Design
- Persist genres in MySQL with Flyway migration.
- Use a `Genre` entity with soft delete fields.
- Use DTOs and MapStruct for response mapping.
- Enforce ADMIN access with Spring Security method authorization.

# API
- `GET /api/v1/genres/public`
- `GET /api/v1/admin/genres`
- `POST /api/v1/admin/genres`
- `PUT /api/v1/admin/genres/{id}`
- `DELETE /api/v1/admin/genres/{id}`

# Database
- Add `genre` table with unique `name` and `slug` columns.

# Edge Cases
- Duplicate name or slug returns conflict.
- Updating or deleting a missing or already deleted genre returns not found.
- Slug must remain URL friendly.

# Acceptance Criteria
- [x] Public endpoint only returns active and non-deleted genres.
- [x] Admin can create, update, deactivate, and soft delete genres.
- [x] Duplicate name and slug are rejected.
- [x] Deleted genres do not appear in public APIs.
- [x] Tests cover service behavior and admin authorization.

# Related
- [[2026-05-27_genre-module]]