# User Flow - Music Module

## Seller Upload Flow

1. Seller opens the upload form.
2. Seller enters title, artist name, description, genre, price, duration, and attaches thumbnail and audio.
3. System validates fields and file types.
4. System uploads files to Cloudinary.
5. System auto-generates slug.
6. System saves music as `DRAFT` by default.

## Seller Manage Flow

1. Seller views own music list.
2. Seller updates title, metadata, files, or status.
3. System checks ownership.
4. System saves the changes.
5. Seller can soft delete own music.

## Public Browse Flow

1. Public user opens music list.
2. System returns only published and non-deleted music.
3. User can filter by genre or search by title.
4. User opens a detail page by slug.

## Buyer Seller Profile Flow

1. Buyer opens a seller profile.
2. System returns published tracks for that seller.

## Admin Flow

1. Admin selects a music item.
2. Admin hides or deletes the music.
3. System applies status `HIDDEN` or soft delete.

# Related

- [[2026-05-27_music-module]]
- [[API-Music]]
- [[ADR-Music-Cloudinary]]
