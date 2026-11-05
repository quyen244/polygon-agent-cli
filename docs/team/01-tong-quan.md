# 01. Tổng quan dự án

Tên dự án: **White-label Agent Builder**.
"White-label" nghĩa là sản phẩm là một bộ dựng (builder): mỗi chủ dùng nó để sinh ra cặp agent của riêng mình, mang tên, luật chi và nhà cung cấp của riêng mình, từ cùng một bộ template.

## Vấn đề

AI agent ngày càng được giao việc có dính tới tiền: nhập hàng, mua dịch vụ, giao dịch.
Để làm được, agent phải đọc dữ liệu bên ngoài (chợ, website, biểu đồ, email của nhà cung cấp).
Dữ liệu bên ngoài là thứ kẻ xấu viết được, nên agent dễ bị lừa hoặc dính **prompt injection**: một đoạn chữ cài trong dữ liệu khiến agent làm theo lệnh của kẻ xấu.
Nếu chính agent đó cũng giữ ví, thì một đoạn chữ cài khéo là đủ để rút tiền.

## Ý tưởng

Thay vì tạo một agent đơn lẻ vừa đọc dữ liệu ngoài vừa giữ ví, hệ thống tạo ra **một cặp agent tách biệt**:

- **Agent Chân Tay (Worker Agent)**: chạy ngầm 24/24, đọc chợ/kho/biểu đồ, quyết định *nên* mua bán gì.
  Nó được tạo từ template có sẵn (Nhập Kho, Trade/Sale, mua dịch vụ...), mỗi template gắn sẵn bộ công cụ phù hợp.
  Nó **không giữ ví**.
  Khi cần chi tiền, nó gửi một *yêu cầu chi* sang Thủ Quỹ.
- **Agent Thủ Quỹ (Treasurer Agent)**: giữ ví trên Avalanche, kiểm tra mọi yêu cầu chi theo luật và hạn mức chủ đặt.
  Hợp lệ và trong hạn mức thì tự ký và chi (chuyển token, trả x402, swap DEX).
  Vượt hạn mức thì đóng băng yêu cầu và hỏi chủ.
  Đây là agent duy nhất liên lạc với chủ (Telegram, web).

Nếu Chân Tay bị hack và gửi lệnh rút tiền bậy, Thủ Quỹ chặn ở bước kiểm tra địa chỉ và hạn mức, và chủ được báo.

## Ai dùng, dùng thế nào

1. Chủ (owner) mở Claude Code, gọi Agent Factory: "tạo cho tôi một agent nhập kho, mỗi ngày chi tối đa 50 USDC, chỉ trả cho 3 nhà cung cấp này".
2. Factory sinh ra cặp Chân Tay + Thủ Quỹ, cấu hình sẵn luật chi.
3. Chủ đăng nhập ví một lần để duyệt hạn mức cho khóa của Thủ Quỹ.
4. Từ đó Chân Tay tự chạy, Thủ Quỹ tự chi trong hạn mức, chủ chỉ nhận báo cáo và bấm Duyệt / Chặn khi có yêu cầu vượt trần.

## Ba tình huống demo (giám khảo sẽ xem)

| # | Tình huống | Kết quả mong đợi |
|---|---|---|
| 1 | Kho thiếu hàng, Chân Tay xin mua 20 USDC từ nhà cung cấp trong whitelist | Thủ Quỹ tự duyệt, chi trên Avalanche, chủ nhận thông báo kèm tx hash |
| 2 | Chân Tay xin mua 300 USDC, vượt hạn mức ngày | Thủ Quỹ đóng băng, Telegram báo chủ, chủ bấm Duyệt thì mới chi |
| 3 | Một listing trên chợ chứa prompt injection, Chân Tay bị lừa xin chuyển toàn bộ tiền tới địa chỉ lạ | Thủ Quỹ chặn (`NOT_WHITELISTED`), chủ được báo có tấn công |

Tình huống 3 là điểm khác biệt của dự án, phải chạy được mượt nhất.

## Vì sao lại dùng repo này

Repo này là CLI ví cho agent (gốc của Polygon), đã có sẵn phần khó nhất về bảo mật: **session mode**.
Trong session mode, chủ duyệt một hạn mức (allowance) cho khóa riêng của agent, và khóa đó chỉ chuyển được token trong hạn mức đó, kiểm tra ở tầng ví chứ không chỉ ở tầng code của mình.
Đó chính là "Két sắt Bounded" trong sơ đồ.
Avalanche C-Chain (chain id 43114) đã có USDC/USDT trong bảng token của session mode.
Chi tiết phần tái sử dụng ở [02-kien-truc.md](02-kien-truc.md).

## Từ vựng chung

| Từ | Nghĩa trong dự án |
|---|---|
| Chân Tay / Worker | Agent đọc dữ liệu ngoài, không giữ ví |
| Thủ Quỹ / Treasurer | Agent giữ ví, duyệt và chi |
| Chủ / Owner | Người sở hữu ví, đặt luật, duyệt yêu cầu vượt trần |
| SpendRequest | Yêu cầu chi Chân Tay gửi Thủ Quỹ (JSON, schema cố định) |
| Decision | Kết quả Thủ Quỹ trả về cho một SpendRequest |
| Policy | Bộ luật chi: whitelist, hạn mức mỗi giao dịch, hạn mức ngày, token được phép |
| Allowance | Hạn mức chủ duyệt cho session key trên ví, lớp chặn cuối cùng |
| Session key | Khóa riêng của Thủ Quỹ, chỉ chi được trong allowance |
| x402 | Chuẩn trả tiền cho API qua HTTP 402: server trả giá, client ký thanh toán, gọi lại |
| Template | Mẫu cấu hình một loại Chân Tay (vai trò, công cụ, lịch chạy, policy mặc định) |
| Agent Factory | MCP server sinh cặp Chân Tay + Thủ Quỹ từ template |
