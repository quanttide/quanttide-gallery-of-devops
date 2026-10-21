# 提交与锁

- 本机 git 没有 `status --staged` → 查全量暂存用 `git diff --cached --name-only` 或 `git status -s`。
- `.git/index.lock` 已存在 → 先核三件事：mtime 多久、是否 0 字节、有无运行中的 git 进程；三者都指向陈旧锁再删掉重试。
- 同一工作树有并行会话在提交 → 提交前 `.git status` 已显示 `M <file>` 这类信号时必须先看内容，直接 `write` 会覆盖别人的提交。
