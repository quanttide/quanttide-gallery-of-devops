# shell

- `comm` 比较两个清单 → 输入必须先 `sort`，否则报 not in sorted order 并退出 1；或改用 `grep -vxF -f`。
- 命令里有裸反引号 → `寻找匹配的 \` 时遇到了未预期的 EOF`（exit 2）。含反引号的文本一律用单引号包裹或走 heredoc。
- `bash -c` 里塞重定向进 `for` 列表、写裸括号（含中文括号）→ 「未预期的记号」语法错误。重定向写在 `done` 之后或命令上，提示语避开裸括号。
- cwd 常不是仓库根 → `cat Cargo.toml` 报 No such file，`find` 会扫进 `target/debug/incremental`。先确认目录或用绝对路径。
