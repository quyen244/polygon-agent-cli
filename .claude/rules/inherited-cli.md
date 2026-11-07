---
paths:
  - 'packages/avalanche-agent-cli/**'
  - 'packages/polygon-agent-cli/**'
  - 'packages/agentconnect-ui/**'
  - 'packages/oidc-relay/**'
  - 'skills/**'
  - 'plugins/**'
---

# Code kế thừa từ Polygon Agent CLI

Đây là code của repo gốc, có test kỹ và đang chạy được.
Nhóm dùng nó làm ví (session mode, allowance, `send`, `swap`, `x402-pay`), không viết lại.

- `CLAUDE.md` ở gốc repo mô tả phần này; đọc nó trước khi sửa.
- Chỉ sửa khi dự án cần (chủ yếu: hỗ trợ Avalanche, Fuji, lệnh Thủ Quỹ cần mà CLI chưa có). Người phụ trách là Khoa.
- Sửa ít nhất có thể, giữ cách làm sẵn có của code xung quanh; không refactor, không đổi tên hàng loạt.
- Mọi sửa đổi giữ test cũ xanh (`pnpm --filter @polygonlabs/agent-cli test` hoặc `pnpm test`) và thêm test cho hành vi mới.
- Không sửa `agentconnect-ui` và `oidc-relay` trừ khi cả nhóm đã chốt cần; chúng là hạ tầng web của bản gốc.
- Không đọc, in, copy hay chuyển file trong `~/.polygon-agent/` (khóa và phiên ví).
