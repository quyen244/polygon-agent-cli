# 04. Quy ước làm việc

## Nhánh và PR

- `dev` là nhánh mặc định, mọi PR đổ vào `dev`.
- `main` chỉ nhận merge từ `dev` khi có bản chạy được (trước demo, trước nộp bài).
- Mỗi issue một nhánh, tách từ `dev`: `<loại>/<số issue>-<mô tả ngắn>`, ví dụ `feat/12-policy-engine`.
- PR ghi `Closes #<số issue>` để issue tự đóng khi merge.
- Mỗi PR cần một người còn lại duyệt.
  PR đụng tới `treasury-protocol` (hợp đồng chung) cần cả hai người còn lại duyệt.

## Commit

Repo bật commitlint theo chuẩn Conventional Commits (hook `commit-msg`), nên commit sai định dạng sẽ bị từ chối.
Định dạng: `<loại>(<phạm vi>): <mô tả>`, ví dụ:

```
feat(treasurer): reject requests to addresses outside the whitelist
fix(owner-bot): ignore button presses from other chats
docs(team): add spike notes for Avalanche
```

Loại hay dùng: `feat`, `fix`, `docs`, `test`, `refactor`, `chore`.

## Các hook có sẵn của repo (dễ vấp)

| Hook | Chạy gì | Khi bị chặn |
|---|---|---|
| `pre-commit` | `lint-staged` (eslint, prettier cho file đã stage) | sửa lỗi lint rồi commit lại |
| `commit-msg` | commitlint | sửa message theo định dạng trên |
| `pre-push` | `typecheck`, và **bắt buộc có changeset** so với `origin/main` | chạy `pnpm exec changeset add --empty` cho docs/tooling, hoặc `pnpm exec changeset add` cho thay đổi code, commit file sinh ra, push lại |

CI của repo (kế thừa từ bản gốc) chỉ chạy cho PR vào `main`, không chạy cho PR vào `dev`.
Nên trước khi mở PR, tự chạy:

```bash
pnpm run lint
```

```bash
pnpm test
```

Các workflow deploy kế thừa (`deploy-agentconnect-ui.yml`, `deploy-oidc-relay.yml`) cần secrets của Polygon mà repo nhóm không có; nếu chúng chạy và đỏ thì không phải lỗi của bạn, báo Quyền (chủ repo).

## Thêm thư viện

`pnpm-workspace.yaml` đặt `minimumReleaseAge: 1440`: pnpm từ chối bản phát hành mới hơn 1 ngày.
Nếu `pnpm add` báo lỗi kiểu này, chọn bản cũ hơn một chút, đừng tắt cấu hình.

## Bí mật và tiền

- Không commit `.env`, private key, token bot, token chủ. `.env` đã có trong `.gitignore`; mỗi package có `.env.example` không chứa giá trị thật.
- Không đọc, in, copy file trong `~/.polygon-agent/` (khóa và phiên ví).
- Ví demo chỉ giữ số tiền nhỏ, allowance nhỏ (vài USD).
- Lệnh chi tiền của CLI luôn chạy `--dry-run` trước khi `--broadcast`.

## Thế nào là xong một task (Definition of Done)

1. Đạt mục "Hoàn thành khi" của issue, và đã chạy thử thật, không chỉ "code trông đúng".
2. Có test cho phần logic (bắt buộc với policy engine và mọi thứ đụng tới tiền).
3. `pnpm run lint` và `pnpm test` xanh.
4. README của package có cách chạy nếu task thêm thứ chạy được.
5. Ghi trong PR: đã kiểm tra bằng cách nào, cái gì chưa kiểm tra được.

## Giao tiếp

- Bị chặn quá nửa ngày vì phụ thuộc người khác: báo ngay trong nhóm, đừng chờ.
- Muốn đổi hợp đồng chung: mở PR sửa [03-hop-dong-chung.md](03-hop-dong-chung.md) và package `treasury-protocol` cùng lúc.
- Mỗi ngày một tin ngắn: hôm qua xong gì, hôm nay làm gì, đang bị chặn bởi gì.
