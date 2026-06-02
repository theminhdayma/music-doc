---
type: tdd
project: ecommerce-music
module: music
created: 2026-05-27
---

# TDD - Music Module

## RED Cases

- Seller uploads music with thumbnail and audio files.
- Public music list returns only `PUBLISHED` and non-deleted tracks.
- Public detail lookup by slug returns only published music.
- Seller cannot update another seller's music.
- Admin can hide any music.
- Invalid file type is rejected.

## GREEN Target

- Add the minimum repository, service, controller, and Cloudinary implementation to satisfy the above tests.

## REFACTOR Target

- Keep controller thin.
- Keep file validation and ownership checks in the service layer.
- Keep mapper logic in MapStruct.

# Related

- [[2026-05-27_music-module]]
- [[API-Music]]
- [[Table-Music]]
- [[ADR-Music-Cloudinary]]
