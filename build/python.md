# Python 包管理

本机 `pip install` 被 PEP 668 拦截、镜像源不稳定是 Python 环境里最常见的两个坑。本文记录实测可行的替代方案，以及各镜像源的实测情况。

## 规则

- `pip install` 被 PEP 668 拦（externally-managed-environment）时改用 `uv`
- `uv pip install` 装到临时 venv，一次性脚本用 `uv run --with <pkg>` 跑
- 长时间无输出、300s 超时通常是镜像在反复重试 404/403，不是网络慢

## PEP 668：externally-managed-environment

现象：`pip install` 被 PEP 668 拦，报 externally-managed-environment。

原因：系统 Python 被标记为受管环境，pip 拒绝直接安装。

处理：改用 `uv`：

- `uv pip install` 安装到临时 venv；
- 临时跑脚本用 `uv run --with <pkg> python3 <script>`。

两种方式本机实测可行。

## 镜像源实测

各 PyPI 镜像源的实测结果：

| 镜像源 | 实测结果 |
|--------|----------|
| 腾讯云 `mirrors.cloud.tencent.com/pypi/simple` | 可用 |
| 阿里云 | 偶发缺包 metadata（`pydantic_core` 404，uv 反复重试卡死） |
| 清华 tuna | 403 整体拒访 |
| 中科大 USTC | 404 |

换源前先确认当前走的是哪个源：安装卡住时看错误信息里给出的 index URL，再决定换哪个源。

## 长时间无输出与超时

现象：安装命令长时间无输出，最终 300s 超时。

原因：通常是镜像在反复重试 404/403，不是网络慢。

处理：

- 中断执行，看错误信息，换源。
- 长时间安装拆成步骤执行，或放到后台跑。
- 不要原地重跑：重跑会重复下载，永远跑不完。

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| `pip install` 报 externally-managed-environment | PEP 668 受管环境限制 | 改用 `uv pip install` 或 `uv run --with <pkg>` |
| uv 反复重试后卡死 | 镜像缺包 metadata（如阿里云 `pydantic_core` 404） | 中断后换源 |
| 403 整体拒访 | 清华 tuna 对当前请求返回 403 | 换其他镜像源 |
| 安装长时间无输出直至 300s 超时 | 镜像在反复重试 404/403 | 中断看错误、换源，不要原地重跑 |
