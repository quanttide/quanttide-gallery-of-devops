# 进程

- `pkill -f <pattern>` 会匹配到自己那条 `bash -c` → 把当前 shell 一起杀掉（exit 143，后续输出全丢）。杀自己起的进程用 `pkill -x <进程名>` 或先 `pgrep` 拿 PID 再 kill。
