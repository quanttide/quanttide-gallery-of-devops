# Terraform

Terraform 拉取 alicloud provider 在国内网络下容易超时，走本地文件系统镜像可以绕开在线下载。

## 规则

- Terraform 拉 alicloud provider 超时时，走本地文件系统镜像
- 本地镜像方式：把 provider 解到 `~/.terraform.d/plugin-cache/…` 后用 `-plugin-dir` 指定

## 工作流

### 1. 手动放置 provider

把 alicloud provider 解压到本地插件缓存目录：

```text
~/.terraform.d/plugin-cache/…
```

### 2. 用本地插件目录初始化

```bash
terraform init -plugin-dir ~/.terraform.d/plugin-cache
```

Terraform 不再在线拉取 provider，直接从 `-plugin-dir` 指定的本地文件系统读取，绕开超时问题。

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| `terraform init` 拉 alicloud provider 超时 | 在线下载 provider 受网络限制 | 把 provider 解到 `~/.terraform.d/plugin-cache/…`，用 `-plugin-dir` 走本地镜像 |
