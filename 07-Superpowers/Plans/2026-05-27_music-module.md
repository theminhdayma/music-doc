---
type: implementation-plan
status: ready
agent: copilot
module: music
---

# Objective
Implement a minimal but secure music module with Cloudinary uploads, pagination, and role-aware management.

# Approved Spec
[[2026-05-27_music-module]]

# Tasks

## Task 1: Create Entity & Migration
- File: `entity/Music.java`
- File: `enums/MusicStatus.java`
- SQL migration: `V3__create_music.sql`

## Task 2: Create DTOs, Mapper, Repository
- File: `dto/request/MusicCreateRequest.java`
- File: `dto/request/MusicUpdateRequest.java`
- File: `dto/response/MusicResponse.java`
- File: `mapper/MusicMapper.java`
- File: `repository/MusicRepository.java`

## Task 3: Create Storage Layer
- File: `service/CloudinaryService.java`
- File: `service/CloudinaryServiceImpl.java`

## Task 4: Create Service Interface & Impl
- File: `service/MusicService.java`
- File: `service/MusicServiceImpl.java`

## Task 5: Create Controller & Security
- File: `controller/MusicController.java`
- File: `config/SecurityConfig.java`

## Task 6: Create Tests
- File: `src/test/java/com/tien/ecommerce_music_be/service/MusicServiceTest.java`
- File: `src/test/java/com/tien/ecommerce_music_be/controller/MusicControllerSecurityTest.java`

## Task 7: Verify
- Run: `./gradlew test`

# Review Checklist
- [ ] DTO boundaries are respected
- [ ] Public APIs only expose published music
- [ ] Seller ownership is enforced
- [ ] File upload validation exists
- [ ] Soft delete is used instead of physical delete

# Related

- [[2026-05-27_music-module]]
- [[API-Music]]
- [[Table-Music]]
- [[ADR-Music-Cloudinary]]
