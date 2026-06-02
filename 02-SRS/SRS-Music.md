# SRS - Music

## Goals

- Allow public users to browse and stream metadata for published music.
- Allow SELLER to upload and manage their own music assets.
- Allow ADMIN to moderate, hide, or delete music.

## Functional Requirements

- The system must allow SELLER to upload music files and thumbnails via Cloudinary.
- The system must store metadata: title, artist, genre, duration, price, slug, status (DRAFT/PUBLISHED), owner id, uploaded_at.
- The system must enforce owner-only updates and deletions for SELLER-managed music.
- The system must support soft delete for music and hide deleted items from public APIs.
- The system must only return published music in public endpoints; seller and admin endpoints may view drafts.
- The system must generate URL-friendly slugs and enforce uniqueness.
- The system must support pagination and sorting for list endpoints.
- The system must not expose direct file URLs in public responses; signed URLs must be generated for delivery flows.
- The system must validate file types and sizes on upload.

## Roles and Permissions

- Public: view published music metadata and stream preview where applicable.
- BUYER: view/purchase published music, add to cart.
- SELLER: upload, update, and delete (soft) own music; publish/unpublish.
- ADMIN: view and manage any music.

## Non-Goals

- Full streaming platform features (this is an e-commerce license store).
- In-Memory streaming; playback implementation is out of scope.

## Acceptance Criteria

- Public list endpoints return only published and non-deleted music.
- Sellers cannot manage music they do not own.
- Uploads fail for disallowed file types or large files.
- Slugs are unique and URL-safe.

# Related

- [[API-Music]]
- [[Table-Music]]
- [[ADR-Music-Cloudinary]]
