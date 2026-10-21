# lark-cli

`lark-cli` 使用中的五条实测结论：身份与邮箱参数的互斥、不存在的输出 schema 选项、分页输出污染 stdout、技能查询命令、群消息两条路径的取数差异。

## `--as bot` 不能配 `--mailbox me`

`--as bot` 不能与 `--mailbox me` 一起用。要指定邮箱时须写显式邮箱。

## `--print-output-schema` 不存在

没有 `--print-output-schema` 这个选项。

## 分页输出会污染 stdout

分页时 stdout 会混入进度行：

```text
[page N] fetching...
```

stdout 因此不是纯 JSON，子进程调用不能直接对它做 `json.loads`。

## 查技能

查技能用：

```bash
lark-cli skills list
lark-cli skills read <path>
```

## 群消息两条路径结果可能不一致

取群消息有两条路径：

| 路径 | 取法 |
|------|------|
| `+messages-search` | 关键词搜索 |
| `+chat-messages-list` | 全量拉取后本地匹配 |

两条路径的结果可能不一致。报数前先取两条路径结果的集合差，再据此报数。

## 常见错误

| 现象 | 原因 | 解决方案 |
|------|------|----------|
| 想用 `--as bot` 配 `--mailbox me` | 两者不能配对 | 改写显式邮箱 |
| 找不到 `--print-output-schema` | 该选项不存在 | — |
| 子进程里 `json.loads` 解析 stdout 失败 | 分页时 stdout 混入 `[page N] fetching...` 进度行 | 不能直接 `json.loads` |
| 群消息数量对不上 | 关键词搜索与全量本地匹配两条路径结果可能不一致 | 取集合差再报数 |
