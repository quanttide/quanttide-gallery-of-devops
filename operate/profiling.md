# Python 进程诊断

用 `py-spy` 抓挂起的 Python 进程时，工具必须装在目标进程所在的容器里，装在宿主机抓不到。

## 规则

- 目标进程在容器里，`py-spy` 就要在容器里装：`docker exec <容器> pip install py-spy`
- 装在宿主机抓不到容器内的进程

## 工作流

### 1. 确认目标进程位置

先确认挂起的 Python 进程是否运行在容器里。

### 2. 在容器内安装 py-spy

```bash
docker exec <容器> pip install py-spy
```

### 3. 抓取进程

在容器内对目标进程执行 `py-spy` 诊断。不要从宿主机跨容器抓，抓不到。

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| 宿主机装了 `py-spy` 但抓不到容器内进程 | 工具与目标进程不在同一环境 | `docker exec <容器> pip install py-spy`，在容器内抓 |
