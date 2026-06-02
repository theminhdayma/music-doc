# ADR - Genre Soft Delete and Admin Authorization

## Status

Accepted

## Context

Genres are shared catalog data used across the marketplace. They must remain auditable and must not disappear physically when removed by an admin.

## Decision

- Use soft delete with `deletedAt` instead of physical delete.
- Treat `isActive` as the publish switch for public visibility.
- Restrict create, update, deactivate, and delete operations to ADMIN.
- Allow public, BUYER, and SELLER users to read only active, non-deleted genres.

## Consequences

- Genre history remains in the database.
- Public APIs stay simple and predictable.
- Service queries must consistently filter out deleted genres.
- Admin endpoints require explicit authorization checks.
# Related

- [[SRS-Genre]]
- [[API-Genre]]
- [[Table-Genre]]
