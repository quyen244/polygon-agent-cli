---
paths:
  - 'packages/workers/**'
  - 'packages/agent-factory/**'
  - 'packages/mock-market/**'
---

# Agent Chân Tay, Agent Factory, chợ giả lập (chủ: Quyền)

## Chân Tay

- Coi Chân Tay là **có thể đã bị hack**. Đừng cố làm nó không thể bị lừa; việc chặn là của Thủ Quỹ.
- Không bao giờ thêm vào Chân Tay: private key, session key, lệnh CLI chi tiền (`send`, `swap`, `x402-pay`), token chủ. Nếu một tính năng có vẻ cần thứ đó, kiến trúc đang đi sai: chuyển nó sang Thủ Quỹ.
- Chân Tay chỉ nói chuyện với Thủ Quỹ qua `POST /requests` và `GET /requests/:id`, dùng schema của `treasury-protocol`.
- Sinh `requestId` một lần cho mỗi ý định mua và dùng lại khi gửi lại; `pending_owner` thì chờ và hỏi lại trạng thái, không tạo request mới.
- Giữ cờ `naive` để demo tấn công tái hiện được; chế độ mặc định của demo do kịch bản C4 quyết định.
- LLM: dùng model mới nhất phù hợp (mặc định `claude-sonnet-5-5`), API key qua `.env`.

## Agent Factory (MCP)

- Tool MCP nhận đầu vào qua schema zod và trả lỗi rõ ràng, không ném stack trace ra cho client.
- `create_agent_pair` không bao giờ sinh hay in ra private key; nó sinh cấu hình Chân Tay, token Chân Tay và đoạn policy cho Thủ Quỹ.
- Token sinh ra được ghi vào file `.env` của dự án sinh ra, không in vào output của tool.
- Template được validate bằng schema khi load; template sai báo lỗi chỉ rõ trường nào.

## Chợ giả lập

- Dữ liệu seed cố định để demo chạy giống nhau mọi lần; mọi tính ngẫu nhiên có seed.
- Listing độc bật / tắt qua endpoint riêng cho kịch bản demo, mặc định tắt.
- Địa chỉ của kẻ tấn công trong seed bắt đầu bằng `0xdead`, để stub server và test nhận ra.
