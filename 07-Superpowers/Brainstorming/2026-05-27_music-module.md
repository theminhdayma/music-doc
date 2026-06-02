# Music Module Brainstorming

## Problem

Sellers need a secure way to upload and manage music tracks for the marketplace.

## Initial Decisions

- Use the existing `genre` table as the music category reference.
- Use Cloudinary for thumbnail and audio uploads.
- Keep the module simple: one service interface, one service implementation, DTOs, repository, and controller.
- Use soft delete and status-based visibility.
- Use pageable public and seller listing endpoints.

## Open Questions

- Whether admin hide should set status to `HIDDEN` or use a separate visibility flag. Current choice: status = `HIDDEN`.
- Whether seller uploads should default to `DRAFT` or `PUBLISHED`. Current choice: `DRAFT` by default.
- Whether public seller profile views should require auth. Current choice: public.

# Related

- [[2026-05-27_music-module]]
- [[API-Music]]
- [[Table-Music]]
- [[ADR-Music-Cloudinary]]
