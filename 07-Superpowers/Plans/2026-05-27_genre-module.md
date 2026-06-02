---
type: implementation-plan
status: ready
agent: copilot
module: genre
---

# Objective
Implement a secure genre module with soft delete and role-based access.

# Approved Spec
[[2026-05-27_genre-module]]

# Tasks

## Task 1: Tạo migration và entity
- File: `src/main/resources/db/migration/V2__create_genre.sql`
- File: `src/main/java/com/tien/ecommerce_music_be/entity/Genre.java`

## Task 2: Tạo DTOs, mapper, repository
- File: `dto/request/GenreRequest.java`
- File: `dto/response/GenreResponse.java`
- File: `mapper/GenreMapper.java`
- File: `repository/GenreRepository.java`

## Task 3: Tạo service và controller
- File: `service/GenreService.java`
- File: `controller/GenreController.java`

## Task 4: Bổ sung security rule
- File: `config/SecurityConfig.java`

## Task 5: Tạo tests
- File: `src/test/java/com/tien/ecommerce_music_be/service/GenreServiceTest.java`
- File: `src/test/java/com/tien/ecommerce_music_be/controller/GenreControllerSecurityTest.java`

## Task 6: Verify
- Chạy: `./gradlew test`

# Review Checklist
- [ ] Đúng spec
- [ ] Có test
- [ ] Không expose entity
- [ ] Soft delete đúng
- [ ] Public API không trả genre đã xóa
# Related

- [[SRS-Genre]]
- [[API-Genre]]
- [[Table-Genre]]
- [[ADR-Genre-Soft-Delete]]
