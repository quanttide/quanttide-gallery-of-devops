# shell

在 agent / CI 场景下驱动 shell 的四个坑：`comm` 排序、裸反引号、`bash -c` 语法、工作目录。

## `comm` 的输入必须先排序

`comm` 比较两个清单时，输入必须先 `sort`。未排序的输入会报 `not in sorted order` 并以退出码 1 退出。

```bash
sort <清单1>
sort <清单2>
comm <排序后清单1> <排序后清单2>
```

替代做法：改用 `grep -vxF -f`。

```bash
grep -vxF -f
```

## 裸反引号触发 EOF 错误

命令里出现裸反引号时，报错：

```text
寻找匹配的 ` 时遇到了未预期的 EOF
```

退出码 2。

做法：含反引号的文本一律用单引号包裹，或走 heredoc，不让 shell 去做反引号匹配。

## `bash -c` 的两类语法雷

`bash -c` 里把重定向塞进 `for` 列表、或写了裸括号（含中文括号），都会报「未预期的记号」语法错误。

做法：

- 重定向写在 `done` 之后，或直接写在命令上
- 提示语等文本里避开裸括号；中文括号同样会触发

## cwd 常不是仓库根

执行命令时 cwd 常不是仓库根。后果：

```bash
cat Cargo.toml
```

报 No such file；`find` 则会扫进 `target/debug/incremental`。

做法：先确认目录，或用绝对路径。

## 常见错误

| 现象 | 原因 | 解决方案 |
|------|------|----------|
| `comm` 报 `not in sorted order` 并退出 1 | 输入未排序，`comm` 要求有序 | 先 `sort`，或改用 `grep -vxF -f` |
| `寻找匹配的 \` 时遇到了未预期的 EOF`（exit 2） | 命令里有裸反引号 | 含反引号的文本用单引号包裹或走 heredoc |
| 「未预期的记号」语法错误 | `bash -c` 里重定向进了 `for` 列表，或写了裸括号（含中文括号） | 重定向写在 `done` 之后或命令上；提示语避开裸括号 |
| `cat Cargo.toml` 报 No such file | cwd 不是仓库根 | 先确认目录或用绝对路径 |
| `find` 扫进 `target/debug/incremental` | cwd 不是仓库根 | 先确认目录或用绝对路径 |
