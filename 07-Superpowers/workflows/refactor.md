# Workflow: Refactor Code

Dùng khi cần cải thiện code hiện tại mà không thay đổi chức năng.

## Nguyên tắc vàng của refactor

**Chức năng trước và sau refactor phải giống hệt nhau.**
Nếu behavior thay đổi → đó không phải refactor, đó là feature/bug-fix.

## Khi nào nên refactor

- Code quá phức tạp, khó đọc, khó maintain
- Có code bị duplicate ở nhiều chỗ
- Function quá dài (> 50 dòng)
- Cần thêm feature mới nhưng code hiện tại khó mở rộng

## Bước 1 — Đảm bảo có test bao phủ

**Không refactor code không có test.**
Nếu chưa có test → viết test trước → sau đó mới refactor.
Test là lưới an toàn đảm bảo refactor không làm hỏng gì.

## Bước 2 — Refactor từng bước nhỏ

Không refactor tất cả một lúc. Mỗi bước nhỏ:
1. Thay đổi một chỗ
2. Chạy test → phải pass
3. Commit
4. Lặp lại

## Bước 3 — Verify

- [ ] Tất cả test vẫn pass sau refactor
- [ ] Không có behavior thay đổi
- [ ] Code mới dễ đọc hơn code cũ
- [ ] Không có performance regression

## Bước 4 — Commit

Commit message: `refactor(scope): mô tả ngắn`
Ví dụ: `refactor(license): extract license validation into separate service`