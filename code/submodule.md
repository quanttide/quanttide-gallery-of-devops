# 子模块

子模块的四类问题：三处名字不一致、指针回退丢提交、路径解析错位、空仓挂载不上。子模块的状态分散在父仓与子仓多处，动手前先核对，而不是直接改。

## 「三处名字」不一致

现象：`.gitmodules`、子模块 `.git/config`、目录内 `origin` 常各写一套名字（改过名的仓尤其）。

因：旧名 push 仍能成功（GitHub 重定向），所以问题会藏很久。

做法：三处各看一遍，逐处处理：

| 位置 | 处理 |
|------|------|
| `.gitmodules` | 写错的必须改 |
| 子模块 `.git/config` | 旧名改掉，不入库 |
| 目录内 `origin` | 与前两处核对一致 |

`.gitmodules` 出现重复键（同一 path 两行 url）不会报错，查出来手删：

```bash
git config -f .gitmodules --get-regexp url
```

## 长期 detached HEAD、本地 main 落后

现象：子模块长期 detached HEAD，本地 main 落后 origin/main。

因：不先更新就动手，收指针会把指针回退、丢掉别人的提交。

做法：动手前先更新：

```bash
git fetch
git checkout main
git pull --ff-only
```

再核对三方时间线——子模块 HEAD vs 父仓 gitlink（`git ls-files -s`）vs `origin/main`——确认一致后再收指针。

## 在子模块里对父仓 `git add`

现象：cwd 在子模块里对父仓 `git add`，报「路径规格未匹配任何文件」。

因：相对路径解析错位，命令按子模块目录解析了父仓路径。

做法：用 `git -C <父仓根>` 把命令挂到父仓根上，或先 cd 回去。

```bash
# 模式：在父仓根上执行父仓命令
git -C <父仓根> add <路径>
```

## `git submodule add` 要求远端已有分支

规则：`git submodule add` 要求远端仓已有分支。

因：新建的空私有仓没有任何分支，父仓却会记下 gitlink，之后 `git submodule update` 检不出来。

做法：新建空私有仓先做一次空提交建出 `main`，再挂进父仓。

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| 三处名字各写一套，旧名 push 仍成功 | GitHub 对旧名做了重定向，问题藏得久 | `.gitmodules`、子模块 `.git/config`、目录内 `origin` 三处各看一遍；子模块 `.git/config` 旧名改掉不入库，`.gitmodules` 写错的必须改 |
| `.gitmodules` 同一 path 有两行 url | 重复键，git 不报错 | `git config -f .gitmodules --get-regexp url` 查出来手删 |
| 收指针后丢了别人的提交 | 子模块 detached HEAD、本地 main 落后，指针被回退 | 先 `git fetch`、`git checkout main`、`git pull --ff-only`，核对子模块 HEAD / 父仓 gitlink（`git ls-files -s`）/ `origin/main` 三方时间线 |
| 父仓 `git add` 报「路径规格未匹配任何文件」 | cwd 在子模块里，相对路径解析错位 | `git -C <父仓根>` 或先 cd 回去 |
| 父仓记了 gitlink 但 `update` 检不出来 | 远端空仓没有分支就挂进父仓 | 先做一次空提交建出 `main`，再 `git submodule add` |
