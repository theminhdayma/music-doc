# Music Test Checklist

- [x] Test upload: size hợp lệ và vượt giới hạn.
- [x] Test upload: format hợp lệ và không hợp lệ.
- [x] Test upload: phân quyền Seller.
- [x] Test trạng thái: DRAFT -> PUBLISHED.
- [x] Test Search: theo query.
- [x] Test Search: theo GenreId.
- [x] Seller top musics: top 3 track ranking theo sales DESC, revenue DESC.
- [x] Seller top musics: loại trừ pending / failed / non-PAID / soft-deleted / seller khác.
- [x] Seller recent sales: latest 10 sales, ordered by purchase time DESC, dùng snapshot fields, gồm cả soft-deleted music.
- [x] Seller recent sales: loại trừ pending / failed / non-PAID / seller khác.
- [x] Seller dashboard UI route `/seller`: hiển thị overview cards, top selling list, recent sales, quick actions.
- [x] Seller dashboard UI: có loading skeleton, empty states, error state + retry action.
- [x] Seller dashboard UI: responsive desktop + tablet, không reload trang khi fetch dữ liệu.
- [x] Seller music detail: seller chỉ xem được music của mình, 403 nếu thuộc seller khác, 404 nếu không tồn tại.
- [x] Seller music list: keyword search theo title, filter theo status, kết hợp keyword + status, pagination giữ nguyên.
- [x] Seller music management UI route `/seller/musics`: hiển thị list, thumbnail, title, genre, price, status, created date.
- [x] Seller music management UI: có loading, empty, error state và nút retry.
- [x] Seller music management UI: hỗ trợ search keyword, filter Published/Draft/Hidden, pagination server-side.
- [x] Seller music management UI: có Create Music button, View action, Edit action, Delete action.
- [x] Seller music management delete: mở confirmation modal và refresh list sau khi xóa thành công.
- [x] Seller edit music UI route `/seller/musics/{id}/edit`: load data sẵn có, pre-fill form, và quay lại quản lý sau khi save.
- [x] Seller edit music UI: hỗ trợ update title, artist, description, genre, price, status, thumbnail, audio file.
- [x] Seller edit music UI: có loading state, validation, unsaved changes detection, cancel button, success/error handling.
- [ ] Seller create music UI: form validation chặn thiếu title/artist/genre/thumbnail/audio.
- [ ] Seller create music UI: thumbnail preview hiển thị đúng, audio file name được hiển thị, submit thành công redirect về `/seller/musics`.

# Related

- [[SRS-Music]]
- [[API-Music]]
- [[Table-Music]]
- [[ADR-Music-Cloudinary]]