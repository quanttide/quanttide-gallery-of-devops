# 进程

`pkill -f` 的模式匹配会连当前 shell 一起杀掉。杀自己起的进程有更安全的写法。

## 规则

- `pkill -f <pattern>` 会匹配到自己那条 `bash -c`，把当前 shell 一起杀掉（exit 143，后续输出全丢）
- 杀自己起的进程用 `pkill -x <进程名>`，或先 `pgrep` 拿 PID 再 kill

## 现象与原因

现象：执行 `pkill -f <pattern>` 后会话直接中断（exit 143），后续输出全丢。

原因：`-f` 按完整命令行匹配，当前 shell 自己那条 `bash -c` 命令行里就包含 `<pattern>`，于是被一起匹配杀掉。

## 处理

两种安全做法：

1. 按进程名精确匹配，不用 `-f`：

```bash
pkill -x <进程名>
```

2. 先查 PID 再杀，杀之前能核对目标：

```bash
pgrep <pattern>
kill <PID>
```

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| `pkill -f` 后会话中断（exit 143），后续输出全丢 | `-f` 匹配到当前 shell 自己那条 `bash -c` | 改用 `pkill -x <进程名>`，或先 `pgrep` 拿 PID 再 kill |
