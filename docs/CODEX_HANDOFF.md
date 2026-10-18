# BÀN GIAO CHO CODEX — LỊCH OFF

**Ngày lập:** 18/10/2026  
**Repo:** https://github.com/curtjnnguyen114-Yurik/L-ch-Off  
**App:** https://curtjnnguyen114-yurik.github.io/L-ch-Off/  
**Nhánh triển khai:** `main` (GitHub Pages)  
**Trạng thái:** ứng dụng PWA vanilla HTML/CSS/JS, không framework, dữ liệu cục bộ từng thiết bị. Đây là bản ghi nhận theo source đang có trong repo, không phải chứng nhận đã kiểm thử iPhone.

## 1. Bối cảnh và yêu cầu sản phẩm
Ứng dụng **LỊCH OFF** dành cho quản lý OFF nhân viên Lạc Cảnh, giao diện tiếng Việt, màu hồng phấn, ưu tiên iPhone/Safari và icon đã thêm vào màn hình chính. Các tab:
- **Nhập / Tìm:** một ô nhập chung, hỗ trợ gợi ý tên, tìm kiếm và nhập OFF theo cú pháp có **tên + OFF**; có mic (khả năng nhận dạng giọng nói phụ thuộc Web Speech API/iOS). Ghi chú chưa đủ ngày có thể lưu dạng chờ nếu đã nhận dạng được tên + OFF.
- **Lịch tổng thể:** theo tháng, chế độ toàn tháng/chỉ ngày có OFF/theo ngày; nhóm theo bộ phận, cột tên sticky, ngày OFF đánh dấu X/📝. Không có cột ghi chú tháng. Chạm ô lịch để **xem toàn bộ note, không chỉnh trực tiếp**, bấm **Sửa** rồi **Lưu** riêng.
- **Văn bản:** sinh mẫu OFF tháng 10/2026 để gửi Zalo, có thể chỉnh tay, lưu bản nháp, sao chép và mở Zalo. Đồng bộ lịch trên cùng iPhone sang văn bản, không phải đồng bộ đám mây. Ngày liên tiếp có cùng note được gộp thành `17-20 (off về quê)`, `11-30 (off dài hạn)`.
- Menu: nhân viên/ảnh, Excel .xlsx offline, sao lưu/khôi phục, nhập lịch tháng 10; trợ giúp theo tính năng.

## 2. File và kiến trúc thực tế
| File | Vai trò |
| --- | --- |
| `index.html` | Giao diện, CSS, JS nghiệp vụ, lưu localStorage, tạo Excel ZIP/SpreadsheetML. Đọc bằng tìm tên hàm; file hiện khoảng 88 KB. |
| `sw.js` | Service worker cache + offline fallback. **Đang ở `lich-off-pwa-2026-10-17-v19` theo source khi lập note.** |
| `manifest.webmanifest` | `display: standalone`, icon, `start_url: ./index.html`. |
| `icon-*.png` | Icon PWA. |

### Dữ liệu cục bộ — KHÔNG ĐƯỢC XÓA
`const KEY='lich-off-demo-v4'`; dữ liệu lưu bằng `persist()`:
```js
{
  staff: [{ id: '...', name: '...', group: '...', photo: '...' }],
  records: [{ person: '<staff.id>', day: 17, month: 10, status: 'OFF', note: 'off về quê' }],
  notes: [{ month: 10, day: 17, text: '...', person: '...', pendingOff: true }]
}
```
Một số trường `notes` là tùy chọn; phải chấp nhận note chung không có `person`/`day`, note chờ chưa có ngày. `status` có `OFF`, `ĐI LÀM`. Năm hiện được hardcode `2026`. Khi storage trống, app dùng `defaults` demo; **không coi dữ liệu demo là dữ liệu thật**.

Các key khác: `lich-off-share-draft-2026-10` (bản văn đã chỉnh tay), `lich-off-share-tracked-2026-10-v2` (metadata theo dõi đồng bộ), `lich-off-seen-features-v1` (đã xem trợ giúp); `sessionStorage['lich-off-entry-draft-session-v1']` (nội dung đang gõ).

**Quan trọng:** Ảnh nhân viên có thể nằm trong localStorage; quota iOS giới hạn. Không xóa app/website data hoặc đổi key để “sửa lỗi”. Khi cần thử migration, làm trên fixture/backup riêng. App hiện **không** tự đồng bộ dữ liệu giữa Safari, standalone PWA, hay các thiết bị nếu storage bị tách.

### Bộ phận
`DEPARTMENTS` hiện gồm:
1. Team Leader
2. Phục vụ nữ (có lương)
3. Phục vụ nữ (Ca không lương)
4. Nam tiếp thực + tả hổ
5. Tạp vụ

`LEGACY_DEPARTMENTS`, `departmentName()`, `normalizeDepartments()` ánh xạ tên cũ sang tên mới. Giữ nguyên khả năng đọc backup cũ. Các tên nhân viên có thể trùng giữa bộ phận; dùng **id**, không dùng tên làm khóa bản ghi.

## 3. Điểm vào trong mã nguồn
| Hàm / biến | Chức năng |
| --- | --- |
| `persist()`, `renderAll()` | Lưu dữ liệu, vẽ lại giao diện; `persist` cũng gọi đồng bộ văn bản. |
| `parseInput()`, `saveInput()` | Nhận lệnh “Tên OFF [ngày/tháng] [ghi chú]”, note chờ. |
| `renderRoster()` | Vẽ lịch nhóm bộ phận, lọc ngày OFF, danh sách theo ngày. |
| `openCell()`, `editSelectedCell()`, `saveCell()` | Popup chỉ đọc → sửa → lưu. |
| `shareFromSchedule()` | Sinh toàn bộ văn bản từ lịch tháng 10/2026. |
| `compactOffDetails()` | Gộp ngày liên tiếp có **note giống hệt sau khi trim/chuẩn hóa khoảng trắng**. |
| `initShareSync()`, `showShareText()` | Khôi phục bản nháp hoặc tạo văn bản khi bản nháp trống. |
| `shareCurrentItems()`, `shareLineDays()`, `missingFromShare()`, `syncShareAdditions()` | So khớp OFF trong lịch với văn bản, bổ sung mục thiếu; phải bảo toàn chỉnh tay. |
| `shortenShareText()` | Nút **⇄ Rút gọn** trên văn bản cũ, chỉ nhận dạng dòng nhân viên có cú pháp hợp lệ. |
| `saveManualShare()`, `copyAndOpenZalo()` | Lưu bản nháp chỉnh tay, sao chép/mở Zalo. |
| `exportXLSX()`, `sheetXML()` | Xuất .xlsx offline, có lịch nhóm bộ phận và note chi tiết. |
| `refreshNewVersion()`, `initPullToRefresh()` | Kiểm tra cập nhật PWA trên iOS; nút ⟳ dự phòng khi native pull gesture không hoạt động. |
| `applyOctober2026()` | Nhập dữ liệu mẫu tháng 10, có đường đi **thay thế dữ liệu tháng 10** sau xác nhận; đặc biệt cẩn thận. |

## 4. Yêu cầu không được làm hỏng
- Văn bản Zalo chỉnh tay **không được tự động tái tạo** sau mỗi lần lưu lịch. OFF mới chỉ bổ sung, không sửa dòng cũ. Trường hợp user chủ động bấm **Rút gọn**, chỉ gộp các ngày **liên tiếp** và có cùng ghi chú; không gộp note khác nhau, không bỏ mất thông tin.
- Ví dụ: `17 (off về quê), 18 (off về quê), 19 (off về quê), 20 (off về quê)` → `17-20 (off về quê)`; `11..30` cùng note → `11-30 (off dài hạn)`. Nếu ngày 19 có note khác, phải tách đoạn.
- Lịch tổng thể giữ duy nhất **cột nhân viên** và **các cột ngày** (có thể có cột tổng OFF), không phục hồi cột “Ghi chú tháng”.
- Popup OFF mở chỉ đọc; phải nhấn **Sửa** mới được thay đổi, và **Lưu** mới persist.
- Xuất Excel phản ánh nhóm nhân viên và ghi chú, không thay dữ liệu gốc.
- Cập nhật app/refresh **không xóa** localStorage, ảnh, văn bản chỉnh tay, note đang lưu. Nếu không có mạng, thông báo rõ.
- Không được coi nút **Đồng bộ lịch OFF** là sync giữa các iPhone. Hiển thị số OFF trên iPhone hiện tại là công cụ chẩn đoán.

## 5. Những điểm cần Codex kiểm tra / nợ kỹ thuật
Đây là **điểm rủi ro cần xác minh**, không khẳng định đã có lỗi trên máy người dùng:

**P0 — Độ an toàn dữ liệu**
- `persist()` ghi DB rồi gọi `syncShareAdditions()` trong cùng khối `try`; nếu đồng bộ văn bản ném lỗi, UI có thể báo “bộ nhớ đầy” dù DB đã ghi. Cần tách báo lỗi đúng nguyên nhân.
- `applyOctober2026(false)` có nhánh thay OFF tháng 10 sau xác nhận. Bắt buộc backup và cảnh báo rõ, không tự chạy khi khởi động/cập nhật.
- Kiểm tra thao tác sửa/xóa OFF có gây **dòng cũ** trong văn bản Zalo không khớp lịch; quy tắc là bảo vệ bản chỉnh tay, không tự xóa.
- Khi có nhiều nhân viên cùng tên ở hai nhóm, `shareNamedLine()` đối chiếu bằng tên có thể ghép sai. Thiết kế metadata liên kết theo `staff.id` thay vì parse text thô, nhưng không phá bản nháp cũ.

**P1 — Đồng bộ văn bản**
- `shareLineDays()` đang parse bằng regex các token ngày; thử các mẫu `17-20 (note)`, `17–20`, `17,18`, dấu chấm phẩy, note có số, nhiều dòng cùng tên, phần “BỔ SUNG”. Không tạo dòng OFF trùng khi mở tab nhiều lần.
- `missingFromShare()` hiện tìm **một** dòng nhân viên; phần bổ sung cuối văn bản có thể tạo thêm dòng cùng tên. Cần kiểm thử idempotence với nhiều lần cập nhật.
- `shortenShareText()` chỉ gộp khi parse được **toàn bộ** danh sách ngày trên một dòng; phải giữ nguyên dòng có văn bản đặc biệt, ghi chú dài, xuống dòng, hoặc cấu trúc chỉnh tay.
- `compactOffDetails()` chỉ gộp note có cùng nội dung sau chuẩn hóa khoảng trắng, chưa chuẩn hóa đồng nghĩa (`off về quê` vs `về quê`); không tự hợp nhất ghi chú khác ý.
- Văn bản hiện cố định tháng 10/2026; cần tránh âm thầm xuất nhầm tháng nếu người dùng chọn tháng khác.

**P1 — iOS PWA**
- Sau thay đổi gần nhất, `sw.js` vẫn ở cache `v19` dù `index.html` đã đổi; **cần tăng version cache ở lần sửa tiếp theo** và kiểm tra bản standalone thật sự nhận bản mới.
- Kiểm tra nút ⟳, vuốt xuống khi ở đầu trang, gesture native iOS, trạng thái offline, không mất draft. Tránh vòng lặp reload.
- `manifest.webmanifest` vẫn có `theme_color: #eaf9f6`, `background_color: #f6fafc` (tone xanh cũ), trong khi giao diện yêu cầu **hồng phấn**; cân nhắc đồng bộ nếu người dùng muốn.

**P2 — Chất lượng**
- JS/CSS tập trung trong `index.html`, chưa có bộ test tự động. Nên tách dần pure helpers hoặc tạo test harness không phá PWA.
- Kiểm tra HTML/CSS sticky cột tên và scroll ngang trên iPhone nhỏ.
- Test Excel mở được trong Excel/Numbers, ghi chú nhiều dòng, nhóm trống, ngày cuối tháng.
- Mic iOS/Web Speech API không bảo đảm được hỗ trợ; có fallback UI và thông báo rõ, không hứa hoạt động trên mọi iPhone.
- Zalo deep link `zalo://launch` phụ thuộc app/iOS; đảm bảo nút **Sao chép** vẫn hoạt động khi deep link không mở.

## 6. Triển khai và xác minh
Repo công khai, GitHub Pages lấy từ nhánh `main`. Sửa file và commit/push lên `main` theo yêu cầu chủ dự án. Đọc SHA mới nhất trước khi update. Sau mỗi thay đổi:
1. Kiểm tra cú pháp tất cả inline `<script>` và kiểm tra HTML/CSS liên quan.
2. Chạy test nghiệp vụ bằng dữ liệu giả, nhất là note dài, OFF liên tiếp, draft sửa tay, reload.
3. Nếu thay giao diện/PWA, bump `CACHE` trong `sw.js` và xác minh Pages đã deploy; lưu ý việc cập nhật có độ trễ.
4. Kiểm tra trên Safari iPhone **và** PWA “Thêm vào màn hình chính” nếu có thiết bị; nếu không có, ghi rõ giới hạn.
5. Không yêu cầu người dùng gỡ app, xóa cache toàn bộ hay xóa dữ liệu Safari để nhận cập nhật.

## 7. Việc nên làm tiếp
**Ưu tiên 1:** bổ sung regression tests cho tạo văn bản Zalo + gộp OFF có cùng note + bảo vệ bản chỉnh tay.  
**Ưu tiên 2:** ổn định cập nhật PWA iOS, tăng cache version sau thay đổi gần nhất, kiểm tra standalone.  
**Ưu tiên 3:** rà soát các trường hợp đồng bộ thiếu/trùng ngày và xử lý lỗi quota/storage chính xác.  
**Ưu tiên 4:** kiểm thử bảng lịch và xuất Excel khớp giao diện, ghi chú chi tiết.

**Nhắc lại:** Không có backend và không có quyền đọc localStorage của iPhone từ GitHub/Codex. Chỉ dữ liệu demo/fixture mới có thể được kiểm thử trong repo. Muốn chẩn đoán dữ liệu thật, yêu cầu người dùng **xuất bản sao lưu** có chủ đích, tránh đăng dữ liệu riêng vào issue/repo công khai.
