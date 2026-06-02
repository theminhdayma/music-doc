# Workflow: Phát Triển Feature Mới

Đây là workflow chuẩn theo Superpowers. Làm đúng thứ tự, không bỏ bước.

---

## Bước 1 — Ghi yêu cầu vào Obsidian

Trước khi làm bất cứ thứ gì, ghi lại yêu cầu thô vào:
`docs/obsidian-vault/07-Superpowers/Brainstorming/YYYY-MM-DD_ten-feature.md`

Ghi tự do, chưa cần hoàn chỉnh — mục đích là có chỗ để brainstorm.

---

## Bước 2 — Brainstorm để làm rõ yêu cầu

Dùng Superpowers brainstorming để trả lời các câu hỏi:
- Feature này phục vụ role nào? (Buyer / Seller / Admin)
- Cần endpoint API nào?
- Cần thay đổi database không?
- Edge cases cần xử lý?
- Liên quan đến license hoặc payment không?

Đọc tài liệu liên quan trong vault nếu cần:
- `docs/obsidian-vault/01-Business/` → business rules
- `docs/obsidian-vault/02-SRS/` → requirements
- `docs/obsidian-vault/05-API/` → API hiện có
- `docs/obsidian-vault/06-Database/` → schema hiện có

---

## Bước 3 — Viết Spec và lưu vào Obsidian

Dùng template sau, lưu vào:
`docs/obsidian-vault/07-Superpowers/Specs/YYYY-MM-DD_ten-feature.md`

```markdown
---
type: spec
project: ecommerce-music
module: [tên module]
status: approved
created: YYYY-MM-DD
---

# Problem
[Vấn đề cần giải quyết]

# Goal
[Mục tiêu cần đạt được]

# Scope
[Làm gì / không làm gì]

# User Flow
[Mô tả luồng người dùng từng bước]

# Technical Design
[Mô tả kỹ thuật ngắn gọn]

# API
[Các endpoint cần tạo]

# Database
[Thay đổi schema nếu có]

# Edge Cases
[Các trường hợp đặc biệt cần xử lý]

# Acceptance Criteria
- [ ] ...
- [ ] ...

# Related
- [[ADR-xxx]]
- [[Plan-xxx]]
```

---

## Bước 4 — Tạo Implementation Plan

Dùng template sau, lưu vào:
`docs/obsidian-vault/07-Superpowers/Plans/YYYY-MM-DD_ten-feature.md`

```markdown
---
type: implementation-plan
status: ready
agent: copilot
module: [tên module]
---

# Objective
[Mục tiêu ngắn gọn]

# Approved Spec
[[Spec-TenFeature]]

# Tasks

## Task 1: Tạo/cập nhật Entity & Migration
- File: `entity/TenEntity.java`
- SQL migration: `V{n}__ten_migration.sql`

## Task 2: Tạo Repository
- File: `repository/TenRepository.java`
- Methods cần: [liệt kê]

## Task 3: Tạo Service
- File: `service/TenService.java`
- Logic chính: [mô tả]

## Task 4: Tạo Controller & DTO
- File: `controller/TenController.java`
- Request DTO: `dto/request/TenRequest.java`
- Response DTO: `dto/response/TenResponse.java`

## Task 5: Frontend
- Page/Component: [tên file]
- API call: [endpoint]

## Task 6: Verify
- Chạy: `./gradlew test`
- Test thủ công qua Swagger UI: `http://localhost:8080/swagger-ui.html`

# Review Checklist
- [ ] Đúng spec
- [ ] Có test
- [ ] Không over-engineering
- [ ] Không phá flow cũ
```

---

## Bước 5 — TDD: RED → GREEN → REFACTOR

Với feature liên quan đến license / payment → **bắt buộc TDD**.
Với feature khác → khuyến khích.

**RED**: Viết test thất bại trước
```java
@Test
void shouldDoSomething() {
    // Arrange
    // Act
    // Assert — test này phải FAIL trước khi có implementation
}
```

**GREEN**: Viết code tối thiểu để test pass

**REFACTOR**: Dọn dẹp code, đảm bảo test vẫn pass

Lưu test cases vào:
`docs/obsidian-vault/07-Superpowers/TDD/YYYY-MM-DD_ten-feature.md`

---

## Bước 6 — Code theo Plan

Làm theo từng Task trong plan đã tạo ở Bước 4.
Tuân thủ:
- `docs/superpowers/coding-rules.md`
- `docs/superpowers/security-rules.md`

---

## Bước 7 — Review

Tự review bằng: `docs/superpowers/review-checklist.md`

Lưu kết quả vào:
`docs/obsidian-vault/07-Superpowers/Code-Review/YYYY-MM-DD_ten-feature.md`

---

## Bước 8 — Lưu kết quả, decision, lesson learned

Cập nhật tài liệu nếu có thay đổi:
- API mới → `docs/obsidian-vault/05-API/`
- Schema mới → `docs/obsidian-vault/06-Database/`
- Quyết định kỹ thuật → tạo ADR mới trong `docs/obsidian-vault/04-ADR/`

Ghi lesson learned vào:
`docs/obsidian-vault/07-Superpowers/Retrospective/YYYY-MM-DD_ten-feature.md`