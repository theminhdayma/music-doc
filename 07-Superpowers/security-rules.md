# Security Rules

## Bảo Vệ File Nhạc — QUAN TRỌNG NHẤT

- **Tuyệt đối không** trả URL file nhạc trực tiếp trong API response
- Luôn dùng **Signed URL** có TTL: preview tối đa 15 phút, download sau mua tối đa 1 giờ
- Signed URL chỉ cấp sau khi verify license hợp lệ và status = ACTIVE
- Preview 30 giây phải xử lý server-side, không expose file gốc

## JWT & Spring Security

- Dùng jjwt 0.12.6 để tạo và verify JWT
- Lưu `userId` và `role` vào JWT claims
- Access token TTL: 1 giờ
- Refresh token TTL: 7 ngày
- `JwtFilter` chạy trước mọi request, set `SecurityContext` nếu token hợp lệ
-- Endpoint public (không cần auth): `POST /api/v1/auth/login`, `POST /api/v1/auth/register`, `GET /api/v1/public/musics` (danh sách), `GET /api/v1/public/musics/{slug}/preview`

## Phân Quyền Theo Role

| Action | BUYER | SELLER | ADMIN |
|--------|-------|--------|-------|
| Xem danh sách track | Có | Có | Có |
| Nghe preview 30s | Có | Có | Có |
| Mua license | Có | Không | Không |
| Download sau mua | Có (cần license) | Không | Không |
| Upload track | Không | Có | Không |
| Xóa track của mình | Không | Có | Có |
| Quản lý tất cả user | Không | Không | Có |

## Validation

- Dùng Bean Validation (`@NotBlank`, `@Size`, `@Email`, `@Min`, `@Max`) trên Request DTO
- Đặt `@Valid` ở tham số trong Controller method
- File upload: chỉ chấp nhận `.mp3`, `.wav`, `.flac`, tối đa 200MB

## Khi License Bị Revoke

Khi order cancel, phải làm ngay:
1. Set `license.status = REVOKED`
2. Set `license.revokedAt = now()`
3. Invalidate signed URL liên quan
4. Save và commit transaction

## Không Được Làm

- Không log thông tin payment, số thẻ
- Không trả password hash trong response
- Không để secret key / JWT secret trong code — dùng `application.properties`