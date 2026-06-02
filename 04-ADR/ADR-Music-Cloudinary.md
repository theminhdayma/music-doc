# ADR - Music Media Storage with Cloudinary

## Status

Accepted

## Context

Music tracks require thumbnail and audio uploads. Raw local file paths must never be exposed in the API.

## Decision

- Use Cloudinary for image and audio uploads.
- Store Cloudinary `secure_url` values in the database.
- Separate upload methods for thumbnail images and audio files.
- Generate music slugs automatically from title.
- Use soft delete and status-based visibility for music tracks.

## Consequences

- Media handling is centralized in a reusable service.
- URLs remain public-safe and do not reveal local storage.
- Tests can mock the storage service cleanly.

# Related

- [[2026-05-27_music-module]]
- [[API-Music]]
- [[Table-Music]]
