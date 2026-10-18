# CODEX — CHECKLIST NGHIỆM THU LỊCH OFF

Dùng dữ liệu giả (không chạm dữ liệu iPhone thật). Đánh dấu hoàn thành chỉ khi có kết quả kiểm thử thực tế.

## A. Không mất dữ liệu
- [ ] LocalStorage `lich-off-demo-v4` vẫn tồn tại và giữ nguyên sau refresh, offline, nâng cache service worker, đóng/mở PWA.
- [ ] Không có `localStorage.clear()`, không tự reset DB, không chạy import ghi đè khi tải app.
- [ ] Thêm/sửa OFF, ghi chú chờ, ảnh nhân viên, sao lưu/khôi phục không mất dữ liệu.
- [ ] Bản nháp Zalo `lich-off-share-draft-2026-10` vẫn giữ nguyên qua refresh.
- [ ] Nhập dở trong ô chung được giữ khi refresh nếu chưa LƯU.

## B. Chuyển lịch → văn bản Zalo
- [ ] Có OFF trên thiết bị + bản nháp trống → tab Văn bản tự sinh đầy đủ các bộ phận/ngày/note.
- [ ] OFF `17,18,19,20` cùng note `off về quê` → `17-20 (off về quê)`.
- [ ] OFF `11..30` cùng note `off dài hạn` → `11-30 (off dài hạn)`.
- [ ] Note khác ở ngày 19 → không gộp qua ngày 19.
- [ ] Dãy ngày rời rạc `7,11,15` vẫn rời rạc.
- [ ] `⇄ Rút gọn` trên văn bản cũ giữ các dòng sửa tay không parse được; không mất ngày/note.
- [ ] Chỉnh tay một dòng, thêm OFF mới, đồng bộ → dòng chỉnh tay giữ nguyên; OFF mới được nối thêm.
- [ ] Mở tab Văn bản / bấm đồng bộ liên tiếp 3 lần → không sinh bản ghi trùng.
- [ ] Hai nhân viên trùng tên ở hai bộ phận không lẫn OFF.
- [ ] Note chờ chưa có ngày không bị biến thành ngày OFF giả.
- [ ] Bấm Gửi/Sao chép dùng đúng văn bản đang hiện; Zalo không mở vẫn có đường sao chép.

## C. Lịch tổng thể
- [ ] Các nhóm đúng thứ tự: Team Leader, Phục vụ nữ có lương, Ca không lương, Nam tiếp thực + tả hổ, Tạp vụ.
- [ ] Không có cột “Ghi chú tháng”. Cột tên sticky; ngày OFF được đánh dấu.
- [ ] Toàn tháng, chỉ ngày OFF (ẩn ngày trống), theo ngày hoạt động.
- [ ] Chạm OFF → popup xem toàn bộ ghi chú, không sửa trực tiếp.
- [ ] Chạm Sửa → nhập trạng thái/note → Lưu → thay đổi có trong lịch và bản xuất.
- [ ] Bảng cuộn ngang không làm vỡ cột tên, không tự zoom ô nhập iOS.

## D. Excel
- [ ] Xuất `.xlsx` offline, file mở được bằng Excel/Numbers.
- [ ] Sheet lịch theo nhóm bộ phận, ngày 1..31, tổng OFF, note trong ô ngày OFF.
- [ ] Sheet chi tiết, ghi chú chung và tổng OFF theo nhân viên không mất dữ liệu.
- [ ] Dữ liệu trong Excel khớp lịch trên cùng thiết bị.

## E. iPhone/PWA
- [ ] Mở link trong Safari, sau đó từ icon trên màn hình chính.
- [ ] Nút ⟳ cập nhật online; offline báo rõ, không xóa localStorage.
- [ ] Vuốt xuống ở đầu trang nếu gesture được iOS cho phép; không cản scroll trong lịch/modal.
- [ ] Thay đổi `index.html` kèm version `sw.js` phù hợp; xác minh app standalone nhận bản mới.
- [ ] Mic có thông báo hỗ trợ/quyền truy cập rõ; fallback nhập tay.
- [ ] Không tự phóng to khi focus ô nhập.

## F. Báo cáo Codex
- [ ] Nêu commit/file đã thay đổi.
- [ ] Nêu test đã chạy, kết quả và lỗi còn tồn tại.
- [ ] Phân biệt test tự động với test Safari/iPhone thực tế.
- [ ] Nếu chưa kiểm thử thiết bị thật, không tuyên bố đã xác nhận chạy trên iPhone.
