# 子模块

- 子模块「三处名字」不一致：`.gitmodules`、子模块 `.git/config`、目录内 `origin` 常各写一套（改过名的仓尤其）。旧名 push 仍能成功（GitHub 重定向），所以问题会藏很久。核对法与修法：三处各看一遍，本地 `.git/config` 的旧名改掉不入库，`.gitmodules` 写错的必须改；`.gitmodules` 出现重复键（同一 path 两行 url）不会报错，用 `git config -f .gitmodules --get-regexp url` 查出来手删。
- 子模块长期 detached HEAD、本地 main 落后 origin/main → 动手前 `git fetch` + `git checkout main` + `git pull --ff-only`，并核对三方时间线（子模块 HEAD vs 父仓 gitlink `git ls-files -s` vs `origin/main`），否则收指针会把指针回退、丢掉别人的提交。
- cwd 在子模块里对父仓 `git add` → 相对路径解析错位，报「路径规格未匹配任何文件」。用 `git -C <父仓根>` 或先 cd 回去。
- `git submodule add` 要求远端仓已有分支 → 新建空私有仓先做一次空提交建出 `main`，再挂进父仓，否则父仓记了 gitlink 而 `update` 检不出来。
