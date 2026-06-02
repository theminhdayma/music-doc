# SRS - Genre

## Goals

- Allow public users to view active genres.
- Allow BUYER and SELLER to view active genres.
- Allow ADMIN to manage genres.

## Functional Requirements

- The system must enforce unique genre name.
- The system must enforce unique genre slug.
- The system must validate slug as URL friendly.
- The system must support soft delete for genres.
- The system must support activating and deactivating genres.
- The system must hide deleted genres from public APIs.

## Roles and Permissions

- Public: view active genres only.
- BUYER: view active genres only.
- SELLER: view active genres only.
- ADMIN: view all genres, create, update, deactivate, delete.

## Non-Goals

- Genre hierarchy.
- Genre translation or localization.
- Automatic genre inference from track metadata.

## Acceptance Criteria

- Public genre endpoint returns only active non-deleted genres.
- Admin can manage genres through protected endpoints.
- Duplicate name or slug is rejected.
- Deleted genres do not appear in public responses.
# Related

- [[API-Genre]]
- [[Table-Genre]]
- [[ADR-Genre-Soft-Delete]]
