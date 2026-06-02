# Workflow: Release / Deploy

Checklist trước khi deploy.

## Checklist Bắt Buộc

### Build & Test
- [ ] Build thành công: `./gradlew build`
- [ ] Tất cả test pass: `./gradlew test`
- [ ] Frontend build: `npm run build`

### Luồng BẮT BUỘC Test Thủ Công Qua Swagger
- [ ] Đăng ký / đăng nhập → nhận JWT
- [ ] Buyer mua track → order COMPLETED → download được
- [ ] Cancel order → license REVOKED → không download được
- [ ] Seller upload track → track hiển thị
- [ ] Preview track → chỉ nghe 30 giây

### Database
- [ ] Migration đã review kỹ
- [ ] Backup database trước khi chạy migration production

### Security
- [ ] Không có secret key trong code
- [ ] File `.env` và `application-prod.properties` không bị commit
- [ ] JWT secret đã set trên môi trường production

### Environment
- [ ] `application-prod.properties` đã config đúng MySQL production
- [ ] Storage config đúng cho production

## Sau Khi Deploy

- [ ] Smoke test nhanh qua Swagger UI
- [ ] Kiểm tra log trong 15 phút đầu
- [ ] Nếu có lỗi nghiêm trọng → rollback, không cố fix trên production