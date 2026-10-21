# Python 包管理

- `pip install` 被 PEP 668 拦（externally-managed-environment）→ 用 `uv`：`uv pip install` 到临时 venv、或 `uv run --with <pkg> python3 <script>`，本机实测可行。
- PyPI 镜像实测：腾讯云 `mirrors.cloud.tencent.com/pypi/simple` 可用；阿里云偶发缺包 metadata（`pydantic_core` 404，uv 反复重试卡死）、清华 tuna 403 整体拒访、中科大 USTC 404。卡住先看错误里给出的 index URL 再换源。
- 安装命令长时间无输出、300s 超时 → 通常是镜像在反复重试 404/403，不是网络慢。中断看错误、换源；长安装拆步骤或后台跑，别原地重跑（会重复下载，永远跑不完）。
