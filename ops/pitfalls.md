# 运维与诊断

- `pkill -f <pattern>` 会匹配到自己那条 `bash -c` → 把当前 shell 一起杀掉（exit 143，后续输出全丢）。杀自己起的进程用 `pkill -x <进程名>` 或先 `pgrep` 拿 PID 再 kill。
- 服务「同步接口正常、异步接口全部挂起」（FastAPI/uvicorn 实证）→ 典型是事件循环被首个失败请求阻塞：先重启服务看是否自愈（最小动作），再进容器逐层查；别一上来改代码。
- `py-spy` 抓挂起的 Python 进程 → 目标进程在容器里就在容器里装（`docker exec <容器> pip install py-spy`），装在宿主机抓不到。
