# 跨阶段工具链

## 命令行

- 命令里有裸反引号 → `寻找匹配的 \` 时遇到了未预期的 EOF`（exit 2）。含反引号的文本一律用单引号包裹或走 heredoc。
- `bash -c` 里塞重定向进 `for` 列表、写裸括号（含中文括号）→ 「未预期的记号」语法错误。重定向写在 `done` 之后或命令上，提示语避开裸括号。
- cwd 常不是仓库根 → `cat Cargo.toml` 报 No such file，`find` 会扫进 `target/debug/incremental`。先确认目录或用绝对路径。

## 外部服务

- GitHub 没有「创建组织」的 API → REST/GraphQL/gh CLI 都做不到，只能在网页建。
- `gh search repos` 没有 stargazers 字段 → 查 stars / pushed_at / archived 用 `gh api repos/<owner>/<repo>`。
- `gh api …/contents/<path> -q .content | base64 -d` → 仓库或路径不存在时 gh 报 404，base64 随后连锁报 invalid input；第一个错误才是根因。
- `lark-cli`：`--as bot` 不能配 `--mailbox me`（须显式邮箱）；`--print-output-schema` 不存在；分页时 stdout 会混入 `[page N] fetching...` 进度行，子进程调用不能直接 `json.loads`；查技能用 `lark-cli skills list` / `skills read <path>`；取群消息两条路径（`+messages-search` 关键词与 `+chat-messages-list` 全量本地匹配）结果可能不一致，要取集合差再报数。
