# 推送与克隆

推送与克隆阶段的三类问题：瞬时 TLS 抖动、分离头指针推空、远端领先导致的拒绝。三者根因不同，先分清是网络抖动、推送目标不对，还是有并行提交。

## GnuTLS 握手失败（exit 128）

现象：推送/克隆偶发下面的错误，退出码 128：

```
GnuTLS, handshake failed: The TLS connection was non-properly terminated
```

因：瞬时错误。不是认证问题，不是 remote 配置问题，别去查证书。

做法：原样重试即成功。一次会话里可能连抖三到四次，可以写成重试循环。

## 分离头指针上推空

现象：`git push` 显示 Everything up-to-date，但什么都没推。

因：处于分离头指针，推送目标没有指向你所在的提交。

做法：显式指定远端分支，推完核对 `HEAD` 与 `origin/main`。

```bash
git push origin HEAD:main
```

## push 被拒（fetch first）

现象：push 被拒，提示 `(fetch first)`。

因：有并行提交，远端比本地新。

做法按顺序：

```bash
# 看远端新增了什么
git fetch

# 确认不冲突后变基
git rebase
```

1. `git fetch` 看远端新增了什么，确认不冲突
2. `git rebase`
3. 重跑门禁：fmt/clippy/测试，注意 cargo 要在含 `Cargo.toml` 的目录
4. 重推

rebase 后 SHA 变了，父仓指针链要按新 SHA 重核。

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| `GnuTLS, handshake failed: The TLS connection was non-properly terminated`（exit 128） | 瞬时网络抖动 | 原样重试即成功，可写成重试循环（一次会话可能连抖三到四次）；不是认证、不是 remote 配置，别查证书 |
| Everything up-to-date 但什么都没推 | 分离头指针，推送目标不对 | `git push origin HEAD:main`，推完核对 `HEAD` 与 `origin/main` |
| push 被拒 `(fetch first)` | 有并行提交 | `git fetch` → 确认不冲突 → `git rebase` → 重跑门禁（fmt/clippy/测试，cargo 在含 `Cargo.toml` 的目录）→ 重推 |
| 重推后父仓指针对不上 | rebase 后 SHA 变了 | 父仓指针链按新 SHA 重核 |
