---
paths:
  - 'packages/owner-bot/**'
  - 'packages/owner-dashboard/**'
---

# Kênh báo Ông Chủ: Telegram bot, web dashboard (chủ: Lượng)

Bot và dashboard cầm token chủ, thứ duy nhất mở được yêu cầu vượt trần, nên phần này cũng là phần bảo mật.

## Bot Telegram

- Chỉ chấp nhận lệnh và nút bấm từ đúng `chat id` của chủ (cấu hình qua `.env`); mọi chat khác bị bỏ qua và ghi log (không ghi nội dung bí mật).
- Hiển thị `reason`, tên sản phẩm và mọi chuỗi đến từ Chân Tay hay chợ như **văn bản thường**: tắt `parse_mode` hoặc escape. Kẻ tấn công có thể cài chữ in đậm, link hay dòng giả "ĐÃ ĐƯỢC CHỦ DUYỆT".
- Hiện địa chỉ nhận đầy đủ kèm label trong whitelist, không cắt ngắn kiểu `0x12...ab` (địa chỉ giả có thể trùng đầu và đuôi).
- Bấm Duyệt / Chặn thì gọi `POST /requests/:id/decide` một lần; bấm lại thì hiện kết quả cũ, không gọi tiếp.
- Sau khi quyết định, sửa tin nhắn gốc thành kết quả (link Snowtrace khi đã chi) để nút không bấm lại được.

## Dashboard

- Không lưu token chủ trong `localStorage`; dùng cookie HttpOnly hoặc giữ phía server.
- Render chuỗi từ Chân Tay như text, không `dangerouslySetInnerHTML`.
- Sửa policy (hạn mức, whitelist) đi qua `PUT /policy`, hiện lại giá trị server trả về chứ không phải giá trị vừa gõ.
- Giao diện phải đọc được trên màn chiếu: chữ đủ lớn, không lệch, không tràn.

## Báo cáo

- Số liệu lấy từ `/summary` của Thủ Quỹ, không tự cộng từ sự kiện (dễ lệch khi mất sự kiện).
- LLM chỉ viết lời từ số liệu đã có, không quyết định và không bịa số.
