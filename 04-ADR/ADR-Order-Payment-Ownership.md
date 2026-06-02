---
type: adr
project: ecommerce-music
module: order-payment
created: 2026-05-28
---

## Context
Order + payment MVP cần checkout đơn giản nhưng nhất quán: cart -> order -> payment sandbox -> ownership -> download authorization. Yêu cầu quan trọng là lookup quyền sở hữu phải nhẹ và không phụ thuộc join phức tạp.

## Decision
1. Dùng 3 bảng: `orders`, `order_items`, `purchased_library`.
2. Lưu snapshot dữ liệu music tại thời điểm mua trong `order_items`.
3. Quyền sở hữu dùng bảng `purchased_library` làm source of truth cho download authorization.
4. Payment trong MVP dùng `SANDBOX`; lưu `payment_method`, `payment_status` ngay trên `orders`.
5. Tất cả endpoint trả DTO thông qua `ApiResponse` / `PageResponse`.

## Consequences
- Ưu điểm:
  - Lookup ownership nhanh và đơn giản.
  - Có lịch sử giao dịch (orders + order_items) tách biệt với quyền truy cập hiện tại.
  - Dễ mở rộng payment provider sau này mà vẫn giữ API checkout.
- Đổi lại:
  - Cần giữ đồng bộ giữa order success và purchased_library trong cùng transaction.
  - Signed URL hiện tại là cách ký tạm thời; production cần private delivery strategy rõ hơn.

# Related

- [[SRS-Order]]
- [[SRS-Payment]]
- [[API-Order]]
- [[Table-Order]]
- [[ADR-Music-Cloudinary]]
