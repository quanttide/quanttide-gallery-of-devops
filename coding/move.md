# 文件移动与改名

- `git mv` 报「fatal: 不在版本控制之下」→ 源或目标未 tracked，或要跨子模块移动。未跟踪文件用 `mv`；跨子模块＝源仓删、目标仓建，不能用 `git mv`；目标未 `git add` 前 `git mv` 同样报错。
- `git mv` 改名后只暂存了 rename → 同文件的内容改动漏在工作区，HEAD 文件名是新的、内容是旧的。改名类提交收尾必须核对 staged diff 含内容改动。
- `git` 不追踪空目录 → 语境仓里的空 `journal/`、`profile/` 删掉不会有任何提交变动，别把它当成「删了东西」。
- 删已被 `git rm` 或搬走的目录 → `rmdir` 报 No such file（exit 1）可忽略，批量脚本别把它当失败中断。
