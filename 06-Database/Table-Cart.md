---
type: database-schema
project: ecommerce-music
module: cart
created: 2026-05-27
---

# Table: cart_item

Migration: `V4__create_cart_item.sql`

Columns:
- `id` BIGINT PRIMARY KEY AUTO_INCREMENT
- `buyer_id` BIGINT NOT NULL — FK -> `user_account(id)`
- `music_id` BIGINT NOT NULL — FK -> `music(id)`
- `created_at` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP

Constraints:
- `UNIQUE(buyer_id, music_id)` to prevent duplicates.
- Foreign keys with `ON DELETE RESTRICT` (follow current DB conventions); since users/music are soft-deleted, queries must filter on `deleted_at IS NULL`.

Indexing:
- Primary key on `id`.
- Unique index on `(buyer_id, music_id)`.
- Additional index on `buyer_id` for quick retrieval of a buyer's cart items.

Sample queries:
- Get cart items for buyer: `SELECT * FROM cart_item ci JOIN music m ON ci.music_id = m.id WHERE ci.buyer_id = ? ORDER BY ci.created_at DESC;`

# Related

- [[SRS-Cart]]
- [[API-Cart]]
- [[2026-05-27_ADR_cart-module]]
