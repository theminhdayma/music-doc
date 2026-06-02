# Table Music

## Columns

- id
- seller_id
- genre_id
- title
- slug
- artist_name
- description
- thumbnail_url
- audio_url
- duration_seconds
- price
- status
- created_at
- updated_at
- deleted_at

## Constraints

- `slug` unique
- `seller_id` references `user_account(id)`
- `genre_id` references `genre(id)`
- `status` defaults to `DRAFT`
- soft delete uses `deleted_at`

## Notes

- Music is visible publicly only when `status = PUBLISHED` and `deleted_at is null`.
- Cloudinary `secure_url` values are stored in `thumbnail_url` and `audio_url`.
- Seller dashboard statistics count musics by `MusicStatus` and exclude soft-deleted rows.

# Related

- [[2026-05-27_music-module]]
- [[API-Music]]
- [[ADR-Music-Cloudinary]]
