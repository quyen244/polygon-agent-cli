# Avalanche Agent CLI (UniHack)

> **White-label Agent Builder trên Avalanche** — Bộ công cụ vận hành on-chain toàn diện cho AI Agent trên hệ sinh thái **Avalanche**.

Dự án thi UniHack của nhóm Quyền, Khoa, Lượng. Cung cấp kiến trúc cặp Agent tách biệt (Worker Agent không giữ ví & Treasurer Agent giữ session key trên Avalanche C-Chain) với cơ chế kiểm soát ngân sách, chính sách chi tiêu và phân quyền bảo mật.

## Cấu trúc Packages

- **[`packages/avalanche-agent-cli/`](packages/avalanche-agent-cli/)** — CLI cốt lõi vận hành on-chain trên Avalanche (Session keys, Transfers, Trails DEX Swaps, x402 payments).
- **[`packages/agentconnect-ui/`](packages/agentconnect-ui/)** — Giao diện web đăng nhập ví (Google / Email) phê duyệt allowance.
- **[`packages/oidc-relay/`](packages/oidc-relay/)** — OIDC relay kết nối trình duyệt với CLI trong quá trình đăng nhập.
- **[`docs/team/`](docs/team/)** — Toàn bộ tài liệu kiến trúc, hợp đồng chung và phân công công việc của nhóm UniHack.

## Tính năng cốt lõi trên Avalanche

- **Avalanche C-Chain Native**: Mặc định tương tác với Avalanche C-Chain (Chain ID: `43114`) và token AVAX, USDC, USDT.
- **Session Key Bounded Allowance**: Chủ ví cấp hạn mức chi tiêu cố định (ví dụ $10 trong 7 ngày), Agent chỉ được chi tiêu trong hạn mức.
- **Code thuần quyết định**: Mọi quyết định chi tiêu thực thi bằng code xác định, không phụ thuộc LLM.
- **Tương thích MCP**: Nhúng ví và rule engine vào Model Context Protocol để Antigravity / Claude gọi lệnh an toàn.

## Bắt đầu nhanh (Quickstart)

```bash
# 1. Cài đặt dependencies
pnpm install

# 2. Chạy CLI
pnpm avalanche-agent --help

# 3. Kiểm tra số dư mặc định trên Avalanche
pnpm avalanche-agent balances

# 4. Kiểm tra trạng thái ví và session allowance
pnpm avalanche-agent wallet status
```

## Yêu cầu môi trường

- Node.js >= 22 (khuyên dùng Node 24 qua `.nvmrc`)
- pnpm >= 10

## Bản quyền

MIT
