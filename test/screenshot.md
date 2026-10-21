# 截图与窗口注入

Ubuntu 26.04 + Wayland 会话下，传统截图工具链大量失效，窗口点击注入同样送不进应用。本文记录实测可用的路径，以及各工具的失败方式。

## 规则

- 文件大小正常 ≠ 截图成功：`import` 有时静默失败但写出正常大小的文件，要读图确认内容
- 动作链改由 widget/Bloc 层测试覆盖，截屏只用于确认渲染

## 截图：失效的工具

Ubuntu 26.04 + Wayland 会话下，以下工具实测全部失败：

| 工具 | 现象 |
|------|------|
| ImageMagick `import -window root` / `import -window <id>` | `missing an image filename` 或 `X_GetImage BadMatch` |
| `ffmpeg -f x11grab` | Not yet implemented |

## 截图：可用的路径

- Python + Xlib `XGetImage` 取像素，再交 PIL 处理。
- 整体降级为「不截图、只跑测试」。

## 确认截图真的成功

文件大小正常不代表截图成功：`import` 有时静默失败，但仍写出正常大小的文件。两个确认办法：

- 读图看内容。
- 统计非黑像素数，确认界面真的渲染了。

## 窗口点击注入

用 `xdotool` 给 Xwayland 下的 Flutter 窗口注入点击：能定位、能激活，但事件送不到应用。

处理：动作链改由 widget/Bloc 层测试覆盖，截屏只用于确认渲染。

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| `import` 报 `missing an image filename` 或 `X_GetImage BadMatch` | Wayland 会话下 ImageMagick 截图失效 | 改用 Python + Xlib `XGetImage` 取像素交 PIL |
| `ffmpeg -f x11grab` 报 Not yet implemented | Wayland 下不支持 | 同上，或降级为不截图只跑测试 |
| 截图文件大小正常但内容不对 | `import` 静默失败仍写出文件 | 读图看内容，或统计非黑像素数确认 |
| `xdotool` 能定位、能激活，但应用无响应 | Xwayland 下事件送不到应用 | 动作链改由 widget/Bloc 层测试覆盖 |
