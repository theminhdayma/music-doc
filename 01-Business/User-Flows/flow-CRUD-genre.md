# User Flow - Genre CRUD

## Goal

Allow ADMIN users to manage genres while public users only read active genres.

## Actors

- Public user
- BUYER
- SELLER
- ADMIN

## Main Flow: View Public Genres

1. User opens the public genre list.
2. System fetches only active genres that are not soft-deleted.
3. System returns the public genre list.

## Main Flow: Admin Creates Genre

1. ADMIN opens the genre management screen.
2. ADMIN enters name, slug, optional description, and active state.
3. System validates required fields and slug format.
4. System checks name and slug uniqueness.
5. System saves the genre.
6. System returns the created genre response.

## Main Flow: Admin Updates Genre

1. ADMIN selects an existing genre.
2. ADMIN edits name, slug, description, or active state.
3. System validates the input.
4. System checks that the updated name and slug remain unique.
5. System saves the updated genre.
6. System returns the updated genre response.

## Main Flow: Admin Deletes Genre

1. ADMIN requests delete for a genre.
2. System marks `deletedAt` instead of removing the row.
3. System saves the genre.
4. Deleted genres are hidden from public APIs.

## Alternate Flows

- If name already exists, creation or update is rejected.
- If slug already exists, creation or update is rejected.
- If a non-ADMIN user calls admin endpoints, access is denied.
- If genre is missing or already deleted, update/delete returns not found.

## Output Rules

- Public views must not show deleted genres.
- Public users, BUYER, and SELLER can only view active genres.
- ADMIN can view all genres and manage their lifecycle.

# Related

- [[SRS-Genre]]
- [[API-Genre]]
- [[ADR-Genre-Soft-Delete]]
