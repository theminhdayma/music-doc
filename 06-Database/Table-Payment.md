
# Payment Data Model (MVP)

Payment trong MVP được lưu trực tiếp trong bảng `orders`, chưa tách bảng `payments` riêng.

## Payment Columns on `orders`

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| payment_method | VARCHAR(20) | NOT NULL, default `SANDBOX` | `SANDBOX` only in MVP |
| payment_status | VARCHAR(20) | NOT NULL, default `PENDING` | `PENDING`, `SUCCESS`, `FAILED` |

## Lifecycle

1. Create order -> `payment_status = PENDING`
2. Sandbox processing:
	- success -> `payment_status = SUCCESS`, `order_status = PAID`
	- failure -> `payment_status = FAILED`

## Future Extension

- Nếu cần audit sâu hơn, có thể tách `payment_transactions` table gồm gateway payload, provider transaction id, timestamps, and retry metadata.

# Related

- [[SRS-Payment]]
- [[API-Payment]]
- [[SRS-Order]]
