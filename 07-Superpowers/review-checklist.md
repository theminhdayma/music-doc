# Review Checklist

Dùng trước khi coi task là "Done". Đánh dấu từng mục.

## Code Quality

- [ ] Không có business logic trong Controller
- [ ] Không có magic number/string — phải đặt thành constant
- [ ] Không có hàm quá dài (> 50 dòng) — tách nhỏ nếu cần
- [ ] Tên biến, method, class mô tả đúng chức năng
- [ ] Không có code bị comment ra vô lý
- [ ] Không có `System.out.println` bị quên — dùng logger

## Backend — Spring Boot

- [ ] Entity có đủ: `id`, `createdAt`, `updatedAt`
- [ ] Dùng DTO — không trả thẳng Entity ra ngoài
- [ ] Request DTO có `@Valid` + Bean Validation annotations
- [ ] Response dùng `ApiResponse<T>` wrapper
- [ ] Exception được xử lý bởi `GlobalExceptionHandler`
- [ ] Không có N+1 query (kiểm tra FetchType và query)
- [ ] Thay đổi schema có migration file

## Security

- [ ] Endpoint cần auth đã được bảo vệ bởi Spring Security
- [ ] Check role đúng (BUYER / SELLER / ADMIN) trước khi thực hiện
- [ ] Không expose URL file nhạc trực tiếp — phải dùng signed URL
- [ ] Input đã được validate
- [ ] Không có secret key trong code

## Test

- [ ] Unit test đã viết cho service layer
- [ ] Tất cả test pass: `./gradlew test`
- [ ] Không có test bị skip không có lý do

## Với Feature Liên Quan Đến License / Order

- [ ] Test case: mua thành công → license ACTIVE
- [ ] Test case: cancel order → license REVOKED
- [ ] Test case: không có license → không download được

## Documentation

- [ ] API mới đã cập nhật vào `docs/obsidian-vault/05-API/`
- [ ] Schema mới đã cập nhật vào `docs/obsidian-vault/06-Database/`
- [ ] Đã lưu kết quả review vào `docs/obsidian-vault/07-Superpowers/Code-Review/`

## Trước Khi Merge

- [ ] Build thành công: `./gradlew build`
- [ ] Tất cả test pass: `./gradlew test`
- [ ] Swagger UI hiển thị đúng endpoint mới
- [ ] Đã review lại diff lần cuối