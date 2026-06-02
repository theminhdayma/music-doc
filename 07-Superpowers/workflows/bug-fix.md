# Workflow: Fix Bug

Làm theo thứ tự này khi fix bug.

## Bước 1 — Tái hiện bug

Trước khi fix, phải tái hiện được bug:
- Bug xảy ra ở đâu? (Frontend / Backend / Database)
- Role nào bị ảnh hưởng? (Buyer / Seller / Admin)
- Các bước để tái hiện là gì?
- Bug xảy ra 100% hay đôi khi?

## Bước 2 — Tìm nguyên nhân gốc rễ

Không fix triệu chứng — tìm nguyên nhân thật sự:
- Đọc error log
- Trace lại luồng dữ liệu từ UI → API → Service → Database
- Kiểm tra có phải do race condition, edge case, hoặc assumption sai không

## Bước 3 — Viết failing test trước khi fix

Viết test mô tả đúng bug → test này phải FAIL trước khi fix.
Sau khi fix xong → test này phải PASS.

Điều này đảm bảo bug không bao giờ quay lại.

## Bước 4 — Fix

Fix đúng nguyên nhân gốc rễ, không patch tạm.
Tham khảo:
- `docs/superpowers/coding-rules.md`
- `docs/superpowers/security-rules.md` (nếu bug liên quan đến security)

## Bước 5 — Verify

- [ ] Failing test đã pass sau khi fix
- [ ] Không có test khác bị break
- [ ] Bug không còn tái hiện được nữa
- [ ] Không tạo ra bug mới ở chỗ khác

## Bước 6 — Review và merge

Dùng review checklist: `docs/superpowers/review-checklist.md`

Commit message: `fix(scope): mô tả bug đã fix`
Ví dụ: `fix(license): revoke license correctly when order cancelled`