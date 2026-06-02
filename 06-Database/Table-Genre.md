# Table Genre

## Columns

- id
- name
- slug
- description
- is_active
- created_at
- updated_at
- deleted_at

## Constraints

- `name` unique
- `slug` unique
- `is_active` defaults to true
- soft delete uses `deleted_at`
# Related

- [[SRS-Genre]]
- [[API-Genre]]
- [[ADR-Genre-Soft-Delete]]
