# 推送与克隆

- 推送/克隆偶发 `GnuTLS, handshake failed: The TLS connection was non-properly terminated`（exit 128）→ 瞬时错误，原样重试即成功；一次会话里可能连抖三到四次，可以写成重试循环。不是认证、不是 remote 配置问题，别去查证书。
- 分离头指针上 `git push` 显示 Everything up-to-date 但什么都没推 → 用 `git push origin HEAD:main` 显式指定远端分支，推完核对 `HEAD` 与 `origin/main`。
- push 被拒 `(fetch first)` → 有并行提交：`git fetch` 看远端新增了什么、确认不冲突 → `git rebase` → 重跑门禁（fmt/clippy/测试，注意 cargo 要在含 `Cargo.toml` 的目录）→ 重推；rebase 后 SHA 变了，父仓指针链要按新 SHA 重核。
