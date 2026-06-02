---
type: spec
project: ecommerce-music
module: music
status: approved
created: 2026-05-27
---

# Problem
Sellers need to upload, update, and manage music tracks while buyers and public visitors can browse published content safely.

# Goal
Provide a secure music module with Cloudinary-backed uploads, role-aware access control, slug-based public lookup, and soft delete support.

# Scope
- Seller uploads and manages own music tracks.
- Public users and buyers can browse published tracks and seller public catalogs.
- Admin can hide or delete any track.
- Do not expose entities directly.

# User Flow
- Seller uploads music with thumbnail and audio files.
- Seller updates own music, changes status, or soft deletes own music.
- Public users browse published tracks, search by title, filter by genre, and open music detail by slug.
- Buyers open a seller profile and view published tracks for that seller.
- Admin hides or deletes any music.

# Technical Design
- Persist music in MySQL using Flyway.
- Use a `Music` entity with seller and genre relations.
- Store Cloudinary `secure_url` values for thumbnail and audio.
- Use DTOs, MapStruct, a service interface, and a service implementation.
- Use Spring Security authorization at the route and method levels.

# API
- `POST /api/v1/seller/musics`
- `PUT /api/v1/seller/musics/{id}`
- `DELETE /api/v1/seller/musics/{id}`
- `GET /api/v1/seller/musics/me`
- `GET /api/v1/public/musics`
- `GET /api/v1/public/musics/{slug}`
- `GET /api/v1/public/musics/search`
- `GET /api/v1/public/sellers/{sellerId}/musics`
- `PATCH /api/v1/admin/musics/{id}/hide`
- `DELETE /api/v1/admin/musics/{id}`

# Database
- Add `music` table with foreign keys to `user_account` and `genre`.
- Use `slug` as the unique public lookup key.

# Edge Cases
- Seller cannot manage another seller's music.
- Public APIs only expose published and non-deleted tracks.
- Duplicate slug must be auto-resolved.
- Invalid file type or oversized file must be rejected.
- Admin hide should not physically delete the record.

# Acceptance Criteria
- [ ] Seller can upload music with Cloudinary-backed files.
- [ ] Public APIs return only published music.
- [ ] Buyer can view seller published catalogs.
- [ ] Seller can only manage own music.
- [ ] Admin can hide or delete any music.

# Related
- [[ADR-Music-Cloudinary]]
- [[Music-Module-Plan]]
