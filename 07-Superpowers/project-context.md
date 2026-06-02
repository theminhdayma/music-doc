# Project Context — Ecommerce Music Marketplace

## Dự án là gì

Marketplace mua bán nhạc bản quyền — nơi Seller (nhạc sĩ, producer) upload và bán track nhạc, Buyer (content creator, doanh nghiệp) mua license để sử dụng hợp pháp.

Không phải streaming như Spotify. Đây là nền tảng **mua đứt license** để dùng nhạc.

Dự án nhỏ, timeline 5 ngày — ưu tiên đơn giản, không over-engineering.

## Tech Stack

| Phần | Công nghệ |
|------|-----------|
| Frontend | Next.js (App Router), TypeScript, Tailwind CSS |
| Backend | Java Spring Boot (thư mục `e-commerce_music_be/`) |
| Database | MySQL |
| ORM | Spring Data JPA (Hibernate) |
| Auth | Spring Security + JWT (jjwt 0.12.6) |
| Validation | Spring Boot Starter Validation (Bean Validation) |
| Mapping | MapStruct 1.6.3 |
| API Docs | SpringDoc OpenAPI (Swagger UI) 2.8.9 |
| Boilerplate | Lombok |
| File storage | Cloud storage (S3 hoặc tương đương) |

## Backend Dependencies (Spring Boot)

```gradle
// Core
implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
implementation 'org.springframework.boot:spring-boot-starter-security'
implementation 'org.springframework.boot:spring-boot-starter-validation'
implementation 'org.springframework.boot:spring-boot-starter-web'

// Database
runtimeOnly 'com.mysql:mysql-connector-j'

// Lombok
compileOnly 'org.projectlombok:lombok'
annotationProcessor 'org.projectlombok:lombok'

// JWT
implementation 'io.jsonwebtoken:jjwt-api:0.12.6'
runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.12.6'
runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.12.6'

// MapStruct
implementation 'org.mapstruct:mapstruct:1.6.3'
annotationProcessor 'org.mapstruct:mapstruct-processor:1.6.3'

// Swagger
implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.8.9'
```

## Ba Role Người Dùng

**Buyer** — người mua license nhạc
- Tìm kiếm, nghe preview 30s, mua license, download file sau khi mua
- Xem lịch sử mua, quản lý license đã có

**Seller** — nhạc sĩ, producer bán nhạc
- Upload track, đặt giá, quản lý track của mình
- Xem doanh thu, thống kê lượt mua

**Admin** — quản trị hệ thống
- Duyệt track mới upload
- Quản lý user, xem báo cáo toàn hệ thống

## License (1 loại duy nhất — dự án nhỏ)

**Standard License**
- Buyer mua một lần, dùng cho mục đích cá nhân và thương mại cơ bản
- Không được bán lại hoặc redistribute file nhạc
- Mỗi lần mua = 1 license gắn với 1 buyer + 1 track

## Quy Tắc Nghiệp Vụ Quan Trọng

- Khi order bị cancel → license bị revoke ngay lập tức
- Preview nhạc chỉ 30 giây đầu, không download được
- File nhạc gốc không bao giờ expose URL trực tiếp — luôn dùng signed URL có thời hạn
- Buyer chỉ download được sau khi order COMPLETED

## Monorepo Structure

```
ECORMERCE_MUSIC/
├── docs/
│   ├── obsidian-vault/     → tài liệu nghiệp vụ, architecture, API spec
│   └── superpowers/        → rules cho AI agent
├── e-commerce_music_be/    → backend Java Spring Boot
└── ecommerce_music_fe/     → frontend Next.js
```