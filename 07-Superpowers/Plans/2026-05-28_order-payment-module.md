# Implementation Plan: ORDER + PAYMENT Module

## Mục tiêu

Triển khai luồng checkout order/payment cho buyer, lưu order snapshot, cấp purchased library, và cho phép buyer/admin truy vấn order/library theo đúng Spec đã chốt.

## Phạm vi kỹ thuật

- Spring Boot backend trong `e-commerce_music_be/`
- Tái sử dụng `ApiResponse` và `PageResponse`
- DTO response riêng cho mọi endpoint
- Checkout chạy trong `@Transactional`
- Payment sandbox mock cứng trong backend
- Không đổi scope đã chốt trong Spec

## Danh sách task

### 1. Database migration
File cần tạo:
- `e-commerce_music_be/src/main/resources/db/migration/V5__create_order_payment_tables.sql`

Nội dung:
- Tạo bảng `orders`
- Tạo bảng `order_items`
- Tạo bảng `purchased_library`
- Thêm foreign keys đúng theo `user_account` và `music`
- Thêm unique constraint cho `(buyer_id, music_id)` ở `purchased_library`
- Thêm index khuyến nghị:
  - `orders(buyer_id, created_at)`
  - `purchased_library(buyer_id, music_id)`

### 2. Entity và enum
File cần tạo:
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/entity/Order.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/entity/OrderItem.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/entity/PurchasedLibrary.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/enums/OrderStatus.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/enums/PaymentStatus.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/enums/PaymentMethod.java`

Nội dung chính:
- `OrderStatus`: `PENDING`, `PAID`
- `PaymentStatus`: `PENDING`, `SUCCESS`, `FAILED`
- `PaymentMethod`: `SANDBOX`
- Entity có `id`, `createdAt`, `updatedAt` khi phù hợp
- Quan hệ JPA dùng `FetchType.LAZY`
- `OrderItem` lưu snapshot data của music tại thời điểm mua

### 3. Repository layer
File cần tạo:
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/repository/OrderRepository.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/repository/OrderItemRepository.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/repository/PurchasedLibraryRepository.java`

Cần hỗ trợ:
- Lấy orders theo buyer
- Lấy orders cho admin
- Lấy order detail theo id
- Kiểm tra duplicate ownership trong `purchased_library`
- Lấy library theo buyer
- Kiểm tra ownership để phục vụ download

### 4. DTO response
File cần tạo:
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/OrderResponse.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/OrderSummaryResponse.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/OrderDetailResponse.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/OrderItemResponse.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/PurchasedLibraryResponse.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/dto/response/DownloadUrlResponse.java`

### 5. DTO mapper layer
File cần tạo:
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/mapper/OrderMapper.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/mapper/PurchasedLibraryMapper.java`

Yêu cầu:
- Map entity -> response DTO
- Không map trực tiếp trong controller
- Hạn chế duplicated mapping logic trong service layer

Nguyên tắc:
- Không trả entity ra controller
- Dùng DTO riêng cho từng endpoint
- Pagination của list endpoint dùng `PageResponse<T>`

### 6. Service layer
File cần tạo:
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/OrderService.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/OrderServiceImpl.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/LibraryService.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/LibraryServiceImpl.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/PaymentService.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/service/PaymentServiceImpl.java`

Logic cần có:
- Checkout buyer cart
- Validate cart rỗng
- Validate duplicated cart items trước checkout
- Validate music deleted/hidden
- Validate buyer chưa own music
- Tạo order, order items snapshot, purchased library
- Mock payment sandbox success
- Clear cart sau khi checkout thành công
- Lấy buyer orders / admin orders
- Lấy buyer order detail / admin order detail
- Lấy buyer library
- Tạo signed download URL cho music đã owned

Checkout transaction order:
1. Validate cart and ownership
2. Create order with `PENDING`
3. Simulate sandbox payment success
4. Update payment status to `SUCCESS`
5. Update order status to `PAID`
6. Create order items snapshot
7. Create purchased library entries
8. Clear buyer cart
9. Return response DTO

### 7. Controllers
File cần tạo:
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/controller/OrderController.java`
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/controller/LibraryController.java`

Yêu cầu:
- Tất cả endpoint trả về `ResponseEntity<ApiResponse<T>>` hoặc `ResponseEntity<ApiResponse<PageResponse<T>>>`
- Dùng `@PreAuthorize` đúng role
- Không chứa business logic
- Không expose entity

### 8. Security and wiring
File cần cập nhật:
- `e-commerce_music_be/src/main/java/com/tien/ecommerce_music_be/config/SecurityConfig.java`
- Có thể cần thêm config/helper cho signed URL sau khi nghiên cứu Cloudinary private access

Yêu cầu:
- `/api/v1/buyer/orders/**` chỉ cho BUYER
- `/api/v1/buyer/library/**` chỉ cho BUYER
- `/api/v1/admin/orders/**` chỉ cho ADMIN

### 9. Exception handling

Yêu cầu:
- Reuse global exception handling convention hiện có
- Business validation phải trả message rõ ràng
- Không throw generic `RuntimeException`
- Chọn `BadRequestException`, `ConflictException`, `ForbiddenException`, `NotFoundException` đúng theo từng case
- Không thêm handler mới nếu handler hiện có đã đáp ứng được response convention

### 10. Tests
File cần tạo/cập nhật:
- Unit tests cho service layer
- Repository tests cho query/fetch behavior
- Controller security tests cho buyer/admin route protection

## Test cases dự kiến

### Checkout
- Buyer checkout thành công theo thứ tự transaction đã chốt
- Checkout thành công tạo order `PAID`
- Checkout thành công tạo payment `SUCCESS`
- Checkout thành công tạo order items snapshot
- Checkout thành công cấp purchased library
- Checkout thành công xóa cart
- Cart rỗng thì checkout fail
- Cart có duplicate item thì checkout fail
- Music deleted thì checkout fail
- Music hidden thì checkout fail
- Buyer đã sở hữu music thì checkout fail

### Buyer orders/library
- Buyer xem được list orders của mình
- Buyer xem được chi tiết order của mình
- Buyer xem được library của mình
- Buyer download chỉ khi đã owned music

### Admin orders
- Admin xem được list orders toàn hệ thống
- Admin xem được chi tiết bất kỳ order nào
- Non-admin bị chặn

### Repository behavior
- Query orders theo buyer trả đúng paging/order
- Query library theo buyer hoạt động đúng unique ownership
- Query order detail fetch đủ dữ liệu cần cho response mapping
- Orders sort mặc định `createdAt DESC`
- Purchased library sort mặc định `purchasedAt DESC`
- Ownership validation luôn dựa trên `purchased_library`, không join qua orders/order_items

## Chạy lệnh kiểm tra

### Chạy test
```bash
cd /Users/mrbee/Desktop/ecormerce_music/e-commerce_music_be
JAVA_HOME=/Users/mrbee/Library/Java/JavaVirtualMachines/corretto-17.0.14/Contents/Home ./gradlew test
```

### Chạy riêng test liên quan
```bash
JAVA_HOME=/Users/mrbee/Library/Java/JavaVirtualMachines/corretto-17.0.14/Contents/Home ./gradlew test --tests "com.tien.ecommerce_music_be.service.OrderServiceTest"
JAVA_HOME=/Users/mrbee/Library/Java/JavaVirtualMachines/corretto-17.0.14/Contents/Home ./gradlew test --tests "com.tien.ecommerce_music_be.repository.*"
```

### Chạy lint / build khi cần
```bash
JAVA_HOME=/Users/mrbee/Library/Java/JavaVirtualMachines/corretto-17.0.14/Contents/Home ./gradlew lint
JAVA_HOME=/Users/mrbee/Library/Java/JavaVirtualMachines/corretto-17.0.14/Contents/Home ./gradlew build
```

## Checkpoints

1. Migration tạo bảng thành công và schema khớp Spec.
2. Entity/enum/repository compile sạch.
3. Service checkout pass toàn bộ validation và transactional behavior.
4. Controller trả đúng wrapper DTO.
5. Tests pass trước khi refactor.
6. Sau refactor, chạy lại test để đảm bảo không đổi behavior.

## Ghi chú rủi ro

- Phần signed URL Cloudinary cho audio private access cần kiểm tra cách triển khai phù hợp trước khi code phần download.
- Nếu repository query cho admin/buyer order cần fetch join để tránh lazy loading khi map DTO, sẽ ưu tiên query rõ ràng theo từng use case thay vì over-engineering.
