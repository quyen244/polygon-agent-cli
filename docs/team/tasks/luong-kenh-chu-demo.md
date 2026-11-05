# Lượng: Kênh báo Ông Chủ + Demo

## Vai trò

Bạn sở hữu mọi thứ chủ và giám khảo nhìn thấy: Telegram bot, dashboard, và buổi demo.
Thủ Quỹ là agent duy nhất nói chuyện với chủ, và nó nói qua code của bạn.

Đầu ra cuối cùng: chủ nhận thông báo trên Telegram, bấm Duyệt / Chặn cho yêu cầu vượt trần, xem và sửa luật chi trên web; và một kịch bản demo chạy lại được ba tình huống không lỗi.

## Đọc trước

- [01-tong-quan.md](../01-tong-quan.md): ba tình huống demo, bạn là người dựng chúng.
- [03-hop-dong-chung.md](../03-hop-dong-chung.md): API của Thủ Quỹ, `TreasurerEvent`, và vì sao địa chỉ lạ không bao giờ hỏi chủ.

## Bảo mật phần của bạn

Bot và dashboard cầm **token chủ**, thứ duy nhất mở được yêu cầu vượt trần.
- Bot chỉ chấp nhận nút bấm và lệnh từ đúng `chat id` của chủ (cấu hình), mọi chat khác bị bỏ qua.
- Token chủ và token bot nằm trong `.env`, không commit, không in ra log.
- Tin nhắn hiển thị `reason` của Chân Tay như văn bản thường (tắt parse Markdown/HTML cho phần đó), vì nó do phía không tin được viết.
  Kẻ tấn công có thể cài link hay chữ in đậm giả "ĐÃ ĐƯỢC CHỦ DUYỆT" vào `reason`.

## Các task (mỗi task là một issue)

### C1. Telegram bot: duyệt yêu cầu vượt trần

- **Label:** `owner-channel`
- **Phụ thuộc:** stub server của A2.
- **Việc cần làm:**
  1. `packages/owner-bot`: nghe `/events` của Thủ Quỹ.
  2. `request.pending_owner`: gửi tin cho chủ gồm agent, số tiền, người nhận (label trong whitelist + địa chỉ), `reason`, hạn 15 phút, và hai nút Duyệt / Chặn.
  3. Bấm nút: gọi `POST /requests/:id/decide`, sửa tin nhắn thành kết quả (đã chi + link Snowtrace, hoặc đã chặn).
  4. `request.rejected` với `suspicious: true`: gửi cảnh báo tấn công nổi bật.
- **Hoàn thành khi:** với stub server, tình huống 2 và 3 hiện đúng trên Telegram; bấm Duyệt hai lần không chi hai lần; chat khác bấm nút thì không có tác dụng.

### C2. Báo cáo

- **Label:** `owner-channel`
- **Phụ thuộc:** C1, `/summary` của A4.
- **Việc cần làm:**
  1. Thông báo mỗi lần `executed` (gộp nếu nhiều trong một phút).
  2. Lệnh `/report` và tổng kết cuối ngày: đã chi theo agent, allowance còn lại, số lần bị chặn và lý do.
  3. Tùy chọn: dùng LLM viết đoạn tóm tắt bằng lời từ số liệu `/summary` (chỉ đọc số, không ra quyết định).
- **Hoàn thành khi:** `/report` khớp với ledger của Thủ Quỹ trong một kịch bản có cả ba tình huống.

### C3. Web dashboard tối giản

- **Label:** `owner-channel`, `ui`
- **Phụ thuộc:** A2 (stub), A4 (thật).
- **Việc cần làm:**
  1. `packages/owner-dashboard`: danh sách yêu cầu theo trạng thái, cập nhật trực tiếp qua `/events`.
  2. Trang luật chi: sửa hạn mức, thêm / xóa whitelist (gọi `PUT /policy`).
  3. Nút Duyệt / Chặn cho yêu cầu đang chờ (cùng API với bot).
- **Hoàn thành khi:** demo được: thêm một địa chỉ vào whitelist trên web, request tới địa chỉ đó lần sau được duyệt.
  Giao diện gọn, không lệch, đọc được trên màn chiếu.

### C4. Kịch bản demo end-to-end

- **Label:** `demo`
- **Phụ thuộc:** A5, A6, B3, B6, C1.
- **Việc cần làm:**
  1. Một script khởi động mọi thứ (mock-market, Thủ Quỹ, Chân Tay, bot) và reset dữ liệu về seed.
  2. Ba bước kích hoạt ba tình huống theo thứ tự, mỗi bước một lệnh.
  3. Checklist trước demo: số dư USDC trên ví, allowance còn đủ, bot online, mạng.
  4. Phương án dự phòng: video quay sẵn nếu mạng hội trường lỗi.
- **Hoàn thành khi:** chạy trọn kịch bản 3 lần liên tiếp không can thiệp tay.

### C5. Pitch

- **Label:** `demo`, `docs`
- **Phụ thuộc:** C4.
- **Việc cần làm:** slide (vấn đề, giải pháp, kiến trúc ba lớp phòng thủ, demo, vì sao Avalanche), README gốc của repo viết lại cho dự án, video demo 2-3 phút.
- **Hoàn thành khi:** cả nhóm tập pitch đúng thời lượng BTC cho phép.

## Không thuộc phạm vi của bạn

- Quyết định duyệt hay chặn: bot chỉ chuyển ý của chủ tới Thủ Quỹ, không tự quyết.
- Chi tiền: bot không gọi CLI, chỉ gọi API Thủ Quỹ.
