
# Table: orders / order_items / purchased_library

## `orders`

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| buyer_id | BIGINT | NOT NULL, FK -> user_account(id) | Buyer |
| order_code | VARCHAR(50) | NOT NULL, UNIQUE | Order code |
| total_amount | DECIMAL(10,2) | NOT NULL | Total amount at checkout |
| order_status | VARCHAR(20) | NOT NULL, default `PENDING` | `PENDING`, `PAID` |
| payment_method | VARCHAR(20) | NOT NULL, default `SANDBOX` | MVP payment method |
| payment_status | VARCHAR(20) | NOT NULL, default `PENDING` | `PENDING`, `SUCCESS`, `FAILED` |
| created_at | DATETIME | NOT NULL | Created timestamp |
| updated_at | DATETIME | NOT NULL | Updated timestamp |

Indexes:
- `idx_orders_buyer_created_at (buyer_id, created_at)`

## `order_items`

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| order_id | BIGINT | NOT NULL, FK -> orders(id) | Parent order |
| music_id | BIGINT | NOT NULL, FK -> music(id) | Purchased music |
| music_title_at_purchase | VARCHAR(255) | NOT NULL | Snapshot title |
| artist_name_at_purchase | VARCHAR(255) | NOT NULL | Snapshot artist |
| thumbnail_url_at_purchase | VARCHAR(500) | NULL | Snapshot thumbnail |
| price_at_purchase | DECIMAL(10,2) | NOT NULL | Snapshot price |
| created_at | DATETIME | NOT NULL | Created timestamp |

Notes:
- Snapshot fields are immutable historical purchase metadata.

## `purchased_library`

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | BIGINT | PK, auto increment | Surrogate key |
| buyer_id | BIGINT | NOT NULL, FK -> user_account(id) | Owner |
| music_id | BIGINT | NOT NULL, FK -> music(id) | Owned music |
| purchased_at | DATETIME | NOT NULL | Purchase timestamp |

Constraints & Indexes:
- Unique: `uk_library_buyer_music (buyer_id, music_id)`
- Index: `idx_library_buyer_music (buyer_id, music_id)`

Strategy:
- Ownership check for download/API authorization uses `purchased_library` directly.

# Related

- [[SRS-Order]]
- [[API-Order]]
- [[SRS-Payment]]
