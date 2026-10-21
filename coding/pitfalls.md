# 编码与版本控制

- 推送/克隆偶发 `GnuTLS, handshake failed: The TLS connection was non-properly terminated`（exit 128）→ 瞬时错误，原样重试即成功；一次会话里可能连抖三到四次，可以写成重试循环。不是认证、不是 remote 配置问题，别去查证书。
- `git mv` 报「fatal: 不在版本控制之下」→ 源或目标未 tracked，或要跨子模块移动。未跟踪文件用 `mv`；跨子模块＝源仓删、目标仓建，不能用 `git mv`；目标未 `git add` 前 `git mv` 同样报错。
- 分离头指针上 `git push` 显示 Everything up-to-date 但什么都没推 → 用 `git push origin HEAD:main` 显式指定远端分支，推完核对 `HEAD` 与 `origin/main`。
- push 被拒 `(fetch first)` → 有并行提交：`git fetch` 看远端新增了什么、确认不冲突 → `git rebase` → 重跑门禁（fmt/clippy/测试，注意 cargo 要在含 `Cargo.toml` 的目录）→ 重推；rebase 后 SHA 变了，父仓指针链要按新 SHA 重核。
- 本机 git 没有 `status --staged` → 查全量暂存用 `git diff --cached --name-only` 或 `git status -s`。
- `git mv` 改名后只暂存了 rename → 同文件的内容改动漏在工作区，HEAD 文件名是新的、内容是旧的。改名类提交收尾必须核对 staged diff 含内容改动。
- 子模块「三处名字」不一致：`.gitmodules`、子模块 `.git/config`、目录内 `origin` 常各写一套（改过名的仓尤其）。旧名 push 仍能成功（GitHub 重定向），所以问题会藏很久。核对法与修法：三处各看一遍，本地 `.git/config` 的旧名改掉不入库，`.gitmodules` 写错的必须改；`.gitmodules` 出现重复键（同一 path 两行 url）不会报错，用 `git config -f .gitmodules --get-regexp url` 查出来手删。
- 子模块长期 detached HEAD、本地 main 落后 origin/main → 动手前 `git fetch` + `git checkout main` + `git pull --ff-only`，并核对三方时间线（子模块 HEAD vs 父仓 gitlink `git ls-files -s` vs `origin/main`），否则收指针会把指针回退、丢掉别人的提交。
- `.git/index.lock` 已存在 → 先核三件事：mtime 多久、是否 0 字节、有无运行中的 git 进程；三者都指向陈旧锁再删掉重试。
- `git` 不追踪空目录 → 语境仓里的空 `journal/`、`profile/` 删掉不会有任何提交变动，别把它当成「删了东西」。
- 删已被 `git rm` 或搬走的目录 → `rmdir` 报 No such file（exit 1）可忽略，批量脚本别把它当失败中断。
- cwd 在子模块里对父仓 `git add` → 相对路径解析错位，报「路径规格未匹配任何文件」。用 `git -C <父仓根>` 或先 cd 回去。
- 同一工作树有并行会话在提交 → 提交前 `.git status` 已显示 `M <file>` 这类信号时必须先看内容，直接 `write` 会覆盖别人的提交。
- `git submodule add` 要求远端仓已有分支 → 新建空私有仓先做一次空提交建出 `main`，再挂进父仓，否则父仓记了 gitlink 而 `update` 检不出来。
- `comm` 比较两个清单 → 输入必须先 `sort`，否则报 not in sorted order 并退出 1；或改用 `grep -vxF -f`。
