# lark-cli

- `lark-cli`：`--as bot` 不能配 `--mailbox me`（须显式邮箱）；`--print-output-schema` 不存在；分页时 stdout 会混入 `[page N] fetching...` 进度行，子进程调用不能直接 `json.loads`；查技能用 `lark-cli skills list` / `skills read <path>`；取群消息两条路径（`+messages-search` 关键词与 `+chat-messages-list` 全量本地匹配）结果可能不一致，要取集合差再报数。
