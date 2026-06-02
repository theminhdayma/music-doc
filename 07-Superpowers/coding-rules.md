# Coding Rules

## Nguyên Tắc Chung (áp dụng cả BE và FE)

- Không để code thừa, không over-engineering — dự án 5 ngày, ưu tiên đơn giản
- Xử lý lỗi rõ ràng, không để exception silent
- Không hardcode config — dùng `application.properties` (BE) hoặc `.env` (FE)
- Commit thường xuyên, mỗi commit một việc

---

## Backend — Java Spring Boot

### Cấu trúc package

```
e-commerce_music_be/src/main/java/
└── com.example.music/
    ├── config/          → SecurityConfig, JwtConfig, SwaggerConfig
    ├── controller/      → REST controllers
    ├── service/         → Business logic
    ├── repository/      → Spring Data JPA repositories
    ├── entity/          → JPA entities (ánh xạ với MySQL)
    ├── dto/
    │   ├── request/     → Request DTOs (input từ client)
    │   └── response/    → Response DTOs (output trả về client)
    ├── mapper/          → MapStruct mappers
    ├── exception/       → Custom exceptions + GlobalExceptionHandler
    ├── security/        → JwtUtil, JwtFilter, UserDetailsServiceImpl
    └── enums/           → Các enum dùng chung
```

### Quy tắc đặt tên

| Thứ | Convention | Ví dụ |
|-----|-----------|-------|
| Class | PascalCase | `TrackService`, `LicenseController` |
| Method / field | camelCase | `findByBuyerId`, `licenseStatus` |
| Constant | SCREAMING_SNAKE_CASE | `MAX_FILE_SIZE`, `JWT_EXPIRATION` |
| Package | lowercase | `com.example.music.service` |
| Entity | PascalCase số ít | `Track`, `Order`, `License` |
| Table MySQL | snake_case số ít | `track`, `order_item`, `license` |
| Cột MySQL | snake_case | `created_at`, `file_url`, `buyer_id` |

### Entity — JPA

- Dùng `@Entity`, `@Table(name = "...")` rõ ràng
- Mọi entity phải có: `id` (Long, auto increment), `createdAt`, `updatedAt`
- Dùng `@PrePersist` / `@PreUpdate` để tự set timestamp
- Dùng Lombok: `@Getter @Setter @NoArgsConstructor @AllArgsConstructor @Builder`
- Soft delete: thêm field `deletedAt`, không xóa thật
- Quan hệ: khai báo rõ `FetchType.LAZY` để tránh N+1

```java
// Ví dụ entity chuẩn
@Entity
@Table(name = "track")
@Getter @Setter @NoArgsConstructor @AllArgsConstructor @Builder
public class Track {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "seller_id")
    private User seller;

    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
    private LocalDateTime deletedAt;

    @PrePersist
    protected void onCreate() { createdAt = updatedAt = LocalDateTime.now(); }

    @PreUpdate
    protected void onUpdate() { updatedAt = LocalDateTime.now(); }
}
```

### Service Layer

- Business logic nằm hoàn toàn ở Service, không để ở Controller
- Service gọi Repository, không gọi trực tiếp EntityManager
- Dùng `@Transactional` ở method cần transaction
- Ném custom exception thay vì return null

### Controller Layer

- Chỉ nhận request, gọi Service, trả response — không có business logic
- Dùng `@Valid` để trigger Bean Validation trên request DTO
- Response thống nhất qua một wrapper class `ApiResponse<T>`
- Khai báo `@Tag` và `@Operation` cho Swagger

```java
// Ví dụ response wrapper
@Data @AllArgsConstructor @NoArgsConstructor
public class ApiResponse<T> {
    private boolean success;
    private String message;
    private T data;

    public static <T> ApiResponse<T> ok(T data) {
        return new ApiResponse<>(true, "Success", data);
    }
    public static <T> ApiResponse<T> error(String message) {
        return new ApiResponse<>(false, message, null);
    }
}
```

### DTO & MapStruct

- Request DTO: dùng Bean Validation annotation (`@NotBlank`, `@Size`, `@Email`...)
- Response DTO: chỉ trả đúng field client cần, không expose toàn bộ entity
- Dùng MapStruct để convert entity ↔ DTO, không convert thủ công

### Exception Handling

- Tạo `GlobalExceptionHandler` với `@RestControllerAdvice`
- Custom exception kế thừa `RuntimeException`
- Trả về HTTP status code đúng nghĩa (400 bad request, 401 unauthorized, 404 not found, 403 forbidden)

### JWT & Security

- JWT tạo bằng jjwt 0.12.6
- Lưu userId và role vào JWT claims
- `JwtFilter` extends `OncePerRequestFilter`, đặt trước `UsernamePasswordAuthenticationFilter`
- Access token TTL: 1 giờ; Refresh token TTL: 7 ngày

---

## Frontend — Next.js

### Cấu trúc thư mục

```
ecommerce_music_fe/src/
├── app/
│   ├── (auth)/          → login, register
│   ├── (buyer)/         → trang buyer
│   ├── (seller)/        → trang seller
│   ├── (admin)/         → trang admin
│   └── api/             → API routes Next.js (nếu cần)
├── components/
│   ├── ui/              → component dùng chung
│   └── features/        → component theo feature
├── lib/                 → axios instance, helpers
├── hooks/               → custom React hooks
└── types/               → TypeScript types
```

### Quy tắc

- Dùng Server Component mặc định, chỉ `"use client"` khi thực sự cần
- Không fetch API ở client nếu có thể fetch ở server
- Tên component: PascalCase — `AudioPlayer`
- Tên file: kebab-case — `audio-player.tsx`
- TypeScript: không dùng `any`

---

## Git Commit Format

```
feat(module): mô tả ngắn
fix(module): mô tả ngắn
refactor(module): mô tả ngắn
test(module): mô tả ngắn
docs(module): mô tả ngắn
```

Ví dụ: `feat(track): add 30s preview endpoint`