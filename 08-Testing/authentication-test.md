# Authentication Test Checklist

- [x] Test đăng ký: email đã tồn tại.
- [x] Test đăng ký: email sai định dạng.
- [x] Test đăng ký: trả về response xác nhận gửi OTP.
- [x] Test verify email: OTP đúng thì trả JWT.
- [x] Test verify email: OTP sai hoặc hết hạn.
- [x] Test login: user chưa verify thì bị chặn.
- [x] Test login: sai mật khẩu.
- [x] Test login: đúng mật khẩu sau khi verify.
- [x] Test forgot password: gửi OTP.
- [x] Test reset password: OTP đúng thì đổi mật khẩu.
- [x] Test logout: token hiện tại bị revoke.
- [x] Test logout: token đã logout không dùng lại được.
- [x] Test profile: xem được profile hiện tại.
- [x] Test profile: cập nhật full name.
- [x] Test profile: cập nhật avatar URL, phone number, address, bio, và ngày sinh.
- [x] Test profile: chuỗi rỗng hoặc chỉ gồm khoảng trắng được normalize thành `null` cho các field tùy chọn.
- [x] Test profile: không thể sửa `role`, `status`, `emailVerified`, hoặc `password` qua update profile.
- [x] Test JWT: token hết hạn.
- [x] Test JWT: token giả hoặc không hợp lệ.

# Related

- [[SRS-Authentication]]
- [[API-Auth]]
- [[API-User]]
- [[ADR-Authentication-JWT]]
- [[Table-User]]