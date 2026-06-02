# Brainstorming: ORDER + PAYMENT Module

## Feature phục vụ role nào
- Buyer: checkout cart, xem danh sách order, xem chi tiết order, xem purchased library, lấy signed download URL.
- Admin: xem tất cả order và chi tiết order để đối soát.
- Seller: không có luồng thao tác trực tiếp trong scope này.

## API cần implement
- `POST /api/v1/buyer/orders/checkout`
- `GET /api/v1/buyer/orders`
- `GET /api/v1/buyer/orders/{orderId}`
- `GET /api/v1/buyer/library`
- `GET /api/v1/buyer/library/{musicId}/download`
- `GET /api/v1/admin/orders`
- `GET /api/v1/admin/orders/{id}`

## Database thay đổi
- Thêm migration tạo 3 bảng mới:
- `orders`
- `order_items`
- `purchased_library`
- `order_items` cần snapshot dữ liệu tại thời điểm mua để tránh phụ thuộc vào thay đổi sau này ở `music`.
- `purchased_library` cần unique `(buyer_id, music_id)` để chặn mua trùng và giảm join nặng khi kiểm tra quyền tải.

## Edge cases cần xử lý
- Cart rỗng thì checkout phải bị reject.
- Music đã bị xóa hoặc bị hidden thì checkout phải bị reject.
- Buyer đã sở hữu music đó rồi thì checkout phải bị reject.
- Nếu một item trong cart lỗi thì toàn bộ checkout phải rollback bằng `@Transactional`.
- Sau checkout thành công thì order phải được tạo, payment sandbox phải được mark success, purchased library phải được cấp, cart phải được clear.
- Download chỉ được phép khi buyer thật sự sở hữu music trong `purchased_library`.

## Payment / sandbox
- Payment chỉ là sandbox, mô phỏng success trong backend.
- Không cần tích hợp cổng thanh toán thật ở scope này.
- `orders` sẽ lưu `payment_method` và `payment_status` để giữ trace trạng thái payment.

## Business rules quan trọng
- Chỉ Buyer được checkout và tải xuống.
- File nhạc gốc không được expose trực tiếp.
- Download endpoint phải trả signed URL có thời hạn.
- Dùng `purchased_library` để tra cứu quyền sở hữu thay vì join nặng qua order/order_items mỗi lần tải.

## Câu hỏi kỹ thuật cần chốt ở bước Spec
- Response của checkout/order/library sẽ reuse `ApiResponse` và `PageResponse`, đồng thời dùng response DTO riêng thay vì trả entity trực tiếp.
- Signed URL cho download sẽ cần research thêm cách phù hợp với Cloudinary cho audio file private access.
- Order status tối thiểu sẽ chỉ gồm `PENDING` và `COMPLETED`.
- Chưa cần `payment_reference` riêng trong scope hiện tại.

## Quyết định đã chốt
- Response wrapper và paging sẽ reuse theo structure đang dùng ở các module trước như `ApiResponse` và `PageResponse`.
- Các API cần trả response DTO riêng, không return trực tiếp entity object.
- Media assets đang nằm trên Cloudinary nên signed download URL sẽ theo hướng hỗ trợ private access của Cloudinary cho audio file.
- Payment hiện là sandbox mock cứng trong backend.
