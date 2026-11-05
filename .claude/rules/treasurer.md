---
paths:
  - 'packages/treasurer/**'
  - 'packages/treasury-protocol/**'
---

# Thủ Quỹ và hợp đồng chung (chủ: Khoa)

Đây là phần giữ tiền: ưu tiên đúng hơn nhanh, và mọi thay đổi phải có test.

## Policy engine

- `evaluate` là hàm thuần: cùng đầu vào thì cùng đầu ra. Không LLM, không mạng, không `Date.now()` (nhận `now` qua tham số), không đọc file.
- Thứ tự kiểm tra: schema, agent khớp token, chain, token, whitelist, trùng `requestId`, hạn mức một giao dịch, hạn mức ngày.
- Hạn mức ngày cộng mọi khoản `approved`, `executed` và đang `pending_owner` trong ngày, để chia nhỏ request không lách được.
- Không có nhánh nào đọc `reason`. Nếu thấy code đọc `reason` để quyết định, đó là lỗi bảo mật.
- Ngoài whitelist luôn `rejected` với `NOT_WHITELISTED`, đánh dấu `suspicious` khi phát sự kiện.

## Test bắt buộc khi sửa policy

Giữ xanh, và thêm ca mới khi thêm luật:
- `reason` chứa lệnh injection, `to` ngoài whitelist: vẫn `rejected`.
- Cùng `requestId` gửi hai lần: không chi lần hai.
- Chia nhỏ nhiều request dưới hạn mức một giao dịch nhưng tổng vượt hạn mức ngày: request vượt bị `pending_owner`.
- `amount` âm, số thực, rỗng, vượt `2^256`: `INVALID_REQUEST`.

## Service và ledger

- Xử lý một request mỗi lúc (hàng đợi), để hai request không cùng lọt hạn mức ngày.
- Ghi trạng thái vào ledger **trước** khi gọi CLI; khởi động lại không được làm mất hay chạy lại request.
- Request đang thực thi mà service chết: khi khởi động lại, đánh dấu cần kiểm tra trên chuỗi, không tự gửi lại.
- `/decide approve` chạy lại các kiểm tra trừ hạn mức (whitelist có thể đã đổi) trước khi chi.
- Xác thực: token Chân Tay và token chủ là hai loại khác nhau; token Chân Tay gọi `/decide` hay `/policy` thì `403`.

## Thực thi qua CLI

- Chạy CLI như tiến trình con (`polygon-agent send | swap | x402-pay ... --chain avalanche`), đọc JSON `{ ok, ... }`.
- `--dry-run` trước, `--broadcast` sau; `x402-pay` luôn có `--max-usd` bằng đúng số tiền đã duyệt.
- CLI báo lỗi thì ghi `failed` kèm `code` của CLI, không thử lại tự động.
- Không import code nội bộ của `packages/polygon-agent-cli`; nếu cần thứ CLI chưa có, thêm lệnh vào CLI.

## Hợp đồng chung

- Sửa schema trong `treasury-protocol` thì sửa `docs/team/03-hop-dong-chung.md` trong cùng PR, và cần Quyền và Lượng duyệt.
- Thêm trường thì cho phép tùy chọn trước, để Chân Tay và bot cũ không vỡ.
- Stub server phải theo kịp schema thật.
