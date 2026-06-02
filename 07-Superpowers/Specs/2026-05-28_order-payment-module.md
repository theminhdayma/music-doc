# Spec: ORDER + PAYMENT Module

## Problem

Hiện tại buyer có cart nhưng chưa có luồng checkout hoàn chỉnh để biến cart thành order, ghi nhận payment sandbox, cấp quyền sở hữu trong purchased library, và phục vụ download hợp lệ sau mua.

Module này cần giải quyết 3 vấn đề chính:
- Checkout phải tạo order và order items với snapshot dữ liệu tại thời điểm mua.
- Payment chỉ là sandbox mock cứng trong backend nhưng vẫn phải lưu trạng thái rõ ràng.
- Buyer chỉ được download khi thực sự sở hữu music, và lookup quyền sở hữu phải nhẹ bằng `purchased_library`.

## Goal

- Buyer checkout cart thành công sẽ tạo order, mark payment success, cấp purchased library, rồi clear cart.
- Buyer xem được lịch sử order, chi tiết order, và purchased library.
- Buyer download được file nhạc qua signed URL nếu đã sở hữu music.
- Admin xem được toàn bộ orders và chi tiết order để đối soát.
- Controller endpoints must return `ResponseEntity<ApiResponse<T>>` hoặc `ResponseEntity<ApiResponse<PageResponse<T>>>` tùy loại endpoint.
- API không trả entity trực tiếp, chỉ trả DTO qua `ApiResponse` và `PageResponse`.
- Tất cả controller responses phải dùng DTO response objects riêng, không expose entity trực tiếp.

## Scope

### In scope
- `POST /api/v1/buyer/orders/checkout`
- `GET /api/v1/buyer/orders`
- `GET /api/v1/buyer/orders/{orderId}`
- `GET /api/v1/buyer/library`
- `GET /api/v1/buyer/library/{musicId}/download`
- `GET /api/v1/admin/orders`
- `GET /api/v1/admin/orders/{id}`
- Tạo migration cho `orders`, `order_items`, `purchased_library`.
- Tạo service layer, repository layer, DTO response, và security rules cho role buyer/admin.

### Out of scope
- Payment gateway thật.
- Refund/cancel order flow.
- Partial payment hoặc multi-license.
- UI frontend cho flow checkout và library.
- Notification/email sau khi mua.

## User Flow

### Checkout
1. Buyer gửi request checkout.
2. Backend lấy cart hiện tại của buyer.
3. Backend validate cart không rỗng.
4. Backend validate từng music trong cart:
  - music tồn tại
  - chưa bị deleted
  - không bị hidden
  - buyer chưa sở hữu trước đó
  - cart không có duplicate music item trước khi checkout
5. Backend tạo `orders` với `PENDING`, sau đó mô phỏng payment sandbox thành công.
6. Backend tạo `order_items` bằng snapshot của music tại thời điểm mua.
7. Backend tạo `purchased_library` cho từng music đã mua.
8. Backend xóa cart items của buyer.
9. Backend trả về order summary.

### Buyer orders
1. Buyer gọi danh sách order.
2. Backend trả về paging của orders theo buyer hiện tại.
3. Buyer gọi chi tiết order để xem các item snapshot.

### Buyer library
1. Buyer gọi library.
2. Backend trả paging danh sách music đã mua, dựa trên `purchased_library`.
3. Buyer gọi download cho `musicId` đã sở hữu.
4. Backend verify ownership bằng `purchased_library`.
5. Backend trả signed URL có thời hạn để tải file.

### Admin orders
1. Admin gọi list/detail orders.
2. Backend trả toàn bộ order theo paging.
3. Admin xem chi tiết order gồm các item snapshot và payment status.

## Technical Design

### Database
Tạo 3 bảng mới:
- `orders`
- `order_items`
- `purchased_library`

### Entity design
- `Order`
  - `id`
  - `buyer`
  - `orderCode`
  - `totalAmount`
  - `orderStatus` with values `PENDING`, `PAID`
  - `paymentMethod` with value `SANDBOX`
  - `paymentStatus` with values `PENDING`, `SUCCESS`, `FAILED`
  - `createdAt`
  - `updatedAt`
- `OrderItem`
  - `id`
  - `order`
  - `music`
  - snapshot fields:
    - `musicTitleAtPurchase`
    - `artistNameAtPurchase`
    - `thumbnailUrlAtPurchase`
    - `priceAtPurchase`
  - `createdAt`
- `PurchasedLibrary`
  - `id`
  - `buyer`
  - `music`
  - `purchasedAt`

### Service responsibilities
- `OrderService`
  - checkout
  - get buyer orders
  - get buyer order detail
  - get admin orders
  - get admin order detail
- `LibraryService`
  - get buyer library
  - generate signed download URL
- `PaymentService`
  - sandbox payment success simulation

### Validation rules
- Reject checkout if cart is empty.
- Reject checkout if cart contains duplicated music items.
- Reject checkout if any music is deleted.
- Reject checkout if any music is hidden.
- Reject checkout if buyer already owns music.
- Reject access if user role is not allowed.
- Reject download if ownership does not exist in `purchased_library`.

### Transaction rule
- Checkout must run inside `@Transactional`.
- Any failure during checkout must rollback all writes.

### Signed URL rule
- Download endpoint must not expose raw storage asset URL directly.
- The implementation should use Cloudinary-compatible private access / signed delivery approach.
- TTL should be bounded and not permanent.

## API

### Buyer checkout
`POST /api/v1/buyer/orders/checkout`

Behavior:
- Use current authenticated buyer.
- Create order from current cart.
- Simulate payment success.
- Grant `purchased_library`.
- Clear cart.

Response:
- `ResponseEntity<ApiResponse<OrderResponse>>`
- `ApiResponse<OrderResponse>`

### Buyer list orders
`GET /api/v1/buyer/orders`

Response:
- `ResponseEntity<ApiResponse<PageResponse<OrderSummaryResponse>>>`
- `ApiResponse<PageResponse<OrderSummaryResponse>>`

### Buyer order detail
`GET /api/v1/buyer/orders/{orderId}`

Response:
- `ResponseEntity<ApiResponse<OrderDetailResponse>>`
- `ApiResponse<OrderDetailResponse>`

### Buyer library list
`GET /api/v1/buyer/library`

Response:
- `ResponseEntity<ApiResponse<PageResponse<PurchasedLibraryResponse>>>`
- `ApiResponse<PageResponse<PurchasedLibraryResponse>>`

### Buyer download
`GET /api/v1/buyer/library/{musicId}/download`

Response:
- `ResponseEntity<ApiResponse<DownloadUrlResponse>>`
- `ApiResponse<DownloadUrlResponse>`

### Admin list orders
`GET /api/v1/admin/orders`

Response:
- `ResponseEntity<ApiResponse<PageResponse<OrderSummaryResponse>>>`
- `ApiResponse<PageResponse<OrderSummaryResponse>>`

### Admin order detail
`GET /api/v1/admin/orders/{id}`

Response:
- `ResponseEntity<ApiResponse<OrderDetailResponse>>`
- `ApiResponse<OrderDetailResponse>`

## Response DTOs

### `OrderResponse`
- `id`
- `orderCode`
- `totalAmount`
- `orderStatus`
- `paymentMethod`
- `paymentStatus`
- `createdAt`
- `itemsCount`

### `OrderSummaryResponse`
- `id`
- `orderCode`
- `buyerId`
- `buyerName`
- `totalAmount`
- `orderStatus`
- `paymentStatus`
- `createdAt`

### `OrderDetailResponse`
- `id`
- `orderCode`
- `buyerId`
- `buyerName`
- `totalAmount`
- `orderStatus`
- `paymentMethod`
- `paymentStatus`
- `createdAt`
- `items`

### `PaymentStatus`
- `PENDING`
- `SUCCESS`
- `FAILED`

### `PaymentMethod`
- `SANDBOX`

### `OrderStatus`
- `PENDING`
- `PAID`

### `OrderItemResponse`
- `id`
- `musicId`
- `musicTitleAtPurchase`
- `artistNameAtPurchase`
- `thumbnailUrlAtPurchase`
- `priceAtPurchase`

### `PurchasedLibraryResponse`
- `musicId`
- `title`
- `artistName`
- `thumbnailUrl`
- `purchasedAt`

### `DownloadUrlResponse`
- `musicId`
- `downloadUrl`
- `expiresAt`

## Database

### `orders`
- `id BIGINT PRIMARY KEY AUTO_INCREMENT`
- `buyer_id BIGINT NOT NULL`
- `order_code VARCHAR(50) NOT NULL UNIQUE`
- `total_amount DECIMAL(10,2) NOT NULL`
- `order_status VARCHAR(20) NOT NULL DEFAULT 'PENDING'` with enum values `PENDING`, `PAID`
- `payment_method VARCHAR(20) NOT NULL DEFAULT 'SANDBOX'`
- `payment_status VARCHAR(20) NOT NULL DEFAULT 'PENDING'` with enum values `PENDING`, `SUCCESS`, `FAILED`
- `created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP`
- `updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP`
- FK to `user_account(id)`
- Recommended index: `(buyer_id, created_at)`

### `order_items`
- `id BIGINT PRIMARY KEY AUTO_INCREMENT`
- `order_id BIGINT NOT NULL`
- `music_id BIGINT NOT NULL`
- `music_title_at_purchase VARCHAR(255) NOT NULL`
- `artist_name_at_purchase VARCHAR(255) NOT NULL`
- `thumbnail_url_at_purchase VARCHAR(500)`
- `price_at_purchase DECIMAL(10,2) NOT NULL`
- `created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP`
- FK to `orders(id)`
- FK to `music(id)`

### `purchased_library`
- `id BIGINT PRIMARY KEY AUTO_INCREMENT`
- `buyer_id BIGINT NOT NULL`
- `music_id BIGINT NOT NULL`
- `purchased_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP`
- FK to `user_account(id)`
- FK to `music(id)`
- Unique `(buyer_id, music_id)`
- Recommended index: `(buyer_id, music_id)`

## Edge Cases

- Cart empty → `400 Bad Request`.
- Cart containing duplicate music items → `400 Bad Request`.
- Music deleted → `400 Bad Request`.
- Music hidden → `400 Bad Request`.
- Buyer already owns music → `409 Conflict` or `400 Bad Request` depending on implementation choice, but must be rejected consistently.
- Download without ownership → `404 Not Found` or `403 Forbidden` depending on final API convention.
- Admin endpoint accessed by non-admin → `403 Forbidden`.
- Any failure during checkout → rollback all created order / library rows.

## Acceptance Criteria

- Buyer can checkout cart successfully.
- Successful checkout creates order, marks payment as `SUCCESS`, transitions order to `PAID`, creates order items snapshot, and purchased library rows.
- Successful checkout clears cart.
- Buyer can list own orders and open order detail.
- Buyer can list purchased library.
- Buyer can request a signed download URL only for owned music.
- Admin can list all orders and view any order detail.
- All controller endpoints return `ResponseEntity<ApiResponse<T>>` or `ResponseEntity<ApiResponse<PageResponse<T>>>`.
- APIs return DTOs wrapped in `ApiResponse` and paging in `PageResponse` only; controllers never expose entities directly.
- Checkout is transactional and rollback-safe.
- No entity object is returned directly from controller responses.
