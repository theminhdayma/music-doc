# Genre Module Brainstorming

- Genres categorize music tracks for browsing and filtering.
- Roles:
  - Public, BUYER, SELLER: read active genres only.
  - ADMIN: create, update, deactivate, soft delete, and view all genres.
- Data needs:
  - name and slug must be unique.
  - soft delete with `deletedAt`.
  - activate/deactivate via `isActive`.
- Public API must exclude deleted genres.
- Admin API needs full management endpoints under `/api/v1/admin/genres`.
- Need DTOs, MapStruct mapper, Bean Validation, method security, Swagger docs, and service tests.
# Related

- [[SRS-Genre]]
- [[API-Genre]]
- [[Table-Genre]]
- [[ADR-Genre-Soft-Delete]]
