# ERD - Ecommerce Music Marketplace

```mermaid
erDiagram
    user_account {
        BIGINT id PK
        VARCHAR email UK
        VARCHAR full_name
        VARCHAR role
        VARCHAR status
        DATETIME created_at
        DATETIME updated_at
        DATETIME deleted_at
    }

    genre {
        BIGINT id PK
        VARCHAR name UK
        VARCHAR slug UK
        BIT is_active
        DATETIME created_at
        DATETIME updated_at
        DATETIME deleted_at
    }

    music {
        BIGINT id PK
        BIGINT seller_id FK
        BIGINT genre_id FK
        VARCHAR title
        VARCHAR slug UK
        VARCHAR artist_name
        DECIMAL price
        VARCHAR status
        DATETIME created_at
        DATETIME updated_at
        DATETIME deleted_at
    }

    orders {
        BIGINT id PK
        BIGINT buyer_id FK
        VARCHAR order_code UK
        DECIMAL total_amount
        VARCHAR order_status
        VARCHAR payment_method
        VARCHAR payment_status
        DATETIME created_at
        DATETIME updated_at
    }

    order_items {
        BIGINT id PK
        BIGINT order_id FK
        BIGINT music_id FK
        VARCHAR music_title_at_purchase
        VARCHAR artist_name_at_purchase
        DECIMAL price_at_purchase
        DATETIME created_at
    }

    purchased_library {
        BIGINT id PK
        BIGINT buyer_id FK
        BIGINT music_id FK
        DATETIME purchased_at
    }

    cart_item {
        BIGINT id PK
        BIGINT buyer_id FK
        BIGINT music_id FK
        DATETIME created_at
    }

    user_account ||--o{ music : "seller_id"
    genre ||--o{ music : "genre_id"
    user_account ||--o{ orders : "buyer_id"
    orders ||--o{ order_items : "order_id"
    music ||--o{ order_items : "music_id"
    user_account ||--o{ purchased_library : "buyer_id"
    music ||--o{ purchased_library : "music_id"
    user_account ||--o{ cart_item : "buyer_id"
    music ||--o{ cart_item : "music_id"
```

## Tổng Quan Nhanh

```mermaid
flowchart LR
    U[user_account] -->|seller_id| M[music]
    G[genre] -->|genre_id| M
    U -->|buyer_id| O[orders]
    O -->|order_id| OI[order_items]
    M -->|music_id| OI
    U -->|buyer_id| PL[purchased_library]
    M -->|music_id| PL
    U -->|buyer_id| CI[cart_item]
    M -->|music_id| CI
```

## Ghi Chú Thiết Kế

- `payment_method` và `payment_status` được lưu trực tiếp trong `orders`, chưa tách bảng `payments` riêng trong MVP.
- `order_items` giữ snapshot dữ liệu tại thời điểm mua để lịch sử đơn hàng không bị ảnh hưởng khi metadata của `music` thay đổi.
- `purchased_library` là bảng kiểm tra ownership chính thức cho luồng download và authorization.
- `cart_item` là bảng tạm cho giỏ hàng, có unique constraint trên `(buyer_id, music_id)` để tránh trùng item.
- Các bảng có `deleted_at` đều dùng soft delete, nên truy vấn public phải lọc `deleted_at is null`.

## Luồng Quan Hệ Chính

1. Một `user_account` theo role `SELLER` có thể sở hữu nhiều `music`.
2. Một `genre` có thể được gắn cho nhiều `music`.
3. Một `user_account` theo role `BUYER` có thể tạo nhiều `orders`.
4. Một `orders` có nhiều `order_items`.
5. Một `music` có thể xuất hiện trong nhiều `order_items`, `purchased_library`, và `cart_item`.
6. Một `user_account` theo role `BUYER` có thể có nhiều mục trong `purchased_library` và `cart_item`.

## Related

- [[Database-Design]]
- [[Table-User]]
- [[Table-Genre]]
- [[Table-Music]]
- [[Table-Order]]
- [[Table-Payment]]
- [[Table-Cart]]