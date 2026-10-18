# AGENTS.md — LỊCH OFF / hướng dẫn cho Codex

> Đọc `docs/CODEX_HANDOFF.md` và `docs/CODEX_ACCEPTANCE.md` trước khi sửa ứng dụng.

## Mục tiêu
Duy trì PWA quản lý lịch nghỉ nhân viên (tiếng Việt, giao diện iOS hồng phấn), deploy GitHub Pages. Ưu tiên tính đúng của lịch, không mất dữ liệu, dễ dùng trên iPhone đã “Thêm vào màn hình chính”.

## Nguyên tắc bắt buộc
1. **Không làm mất dữ liệu**: dữ liệu thật nằm trong `localStorage['lich-off-demo-v4']` trên *từng thiết bị*. Không đổi/xóa key, không tự ghi đè bằng `defaults`, không gọi `localStorage.clear()`, không xóa dữ liệu website/service worker theo cách ảnh hưởng storage. Nếu thay đổi schema, có migration tương thích ngược và bản sao lưu.
2. **Bản nháp Zalo do người dùng sửa là nguồn ưu tiên**: `lich-off-share-draft-2026-10`. Đồng bộ chỉ bổ sung mục OFF còn thiếu; không tự viết lại/xóa/đảo thứ tự những dòng người dùng đã chỉnh tay. Chức năng “⇄ Rút gọn” chỉ chạy khi người dùng bấm, bảo toàn thông tin và ghi chú.
3. **Không tuyên bố đồng bộ đa thiết bị**: app hiện chỉ dùng localStorage, không có backend. “Đồng bộ lịch OFF” nghĩa là từ lịch trên *cùng thiết bị* sang tab Văn bản.
4. Giữ các tab **Nhập / Tìm**, **Lịch tổng thể**, **Văn bản**; giữ 5 bộ phận và mapping bộ phận cũ. Tab Lịch tổng thể chỉ có cột tên/bộ phận và các ngày, không có cột ghi chú tháng. Chạm ô OFF mở popup *chỉ đọc*; bấm **Sửa** mới hiện form và **Lưu** riêng.
5. Nhập ghi chú chỉ lưu khi xác định được **Tên nhân viên + OFF**; ngày/tháng và nội dung ghi chú có thể bổ sung sau. Không ghi đè dữ liệu chỉ vì người dùng đang tìm kiếm.
6. Giữ chức năng xuất Excel .xlsx offline, nhóm bộ phận và ghi chú chi tiết; giữ sao lưu/khôi phục, ảnh nhân viên, bản văn Zalo, nút sao chép/mở Zalo.
7. iOS: không tự zoom khi focus; tránh phá scroll ngang bảng, bàn phím, modal; giữ cách tải lại phiên bản bằng nút ⟳ và vuốt xuống (nếu hệ điều hành hỗ trợ).
8. Trước khi sửa, kiểm tra mã thực tế. Mỗi thay đổi liên quan PWA cần cân nhắc tăng `CACHE` trong `sw.js`, xác minh cache mới thực sự hoạt động; không cần xóa dữ liệu.
9. Không nhúng secrets, API key hay dữ liệu riêng từ máy người dùng vào repo công khai.
10. Kiểm tra JavaScript parse, chức năng nghiệp vụ và iOS Safari/PWA thực tế trước khi xác nhận hoàn thành. Nếu chưa kiểm thử thiết bị, nói rõ.

## Cấu trúc
- `index.html`: gần như toàn bộ HTML/CSS/JS, các hàm `persist`, `renderRoster`, `openCell`, `shareFromSchedule`, `compactOffDetails`, `syncShareAdditions`, `shortenShareText`, `exportXLSX`.
- `sw.js`: service worker và version cache.
- `manifest.webmanifest`: PWA standalone.
- `docs/CODEX_HANDOFF.md`: lịch sử, cấu trúc dữ liệu, các điểm cần kiểm tra.
- `docs/CODEX_ACCEPTANCE.md`: kịch bản nghiệm thu.

## Quy trình
Đọc source → sao lưu dữ liệu mẫu/fixture → sửa nhỏ có kiểm thử → tăng version cache khi phù hợp → kiểm tra deploy GitHub Pages → ghi rõ thay đổi và hạn chế còn lại. Tránh refactor lớn làm thay đổi dữ liệu hoặc hành vi chỉnh sửa thủ công nếu không được yêu cầu.
