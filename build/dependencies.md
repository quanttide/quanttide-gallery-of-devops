# 依赖源

抓取 crates.io 与 pub.dev 的数据时，两家 API 的访问要求和返回结构差异很大。本文记录实测结论，避免解析和下载时踩坑。

## 规则

- crates.io API 请求必须带 User-Agent，否则返回 403
- crates.io 返回结构是顶层数据，不是 PyPI 那种 `{"crate": …}` 嵌套
- pub.dev 的 `/api/packages/<name>` 只含元数据，不含 README 正文
- README 正文要从归档包解包获取

## crates.io API

访问要求：

- 必须带 User-Agent，否则 curl 返回 403。

返回结构：

- 顶层直接给出数据，不是 PyPI 那种 `{"crate": …}` 嵌套结构。

写解析代码时不要照搬 PyPI 的取值路径，否则会取不到字段。

## pub.dev 元数据与 README

Dart/pub.dev 取数分两步，因为两个接口提供的内容不同：

1. 元数据走 `/api/packages/<name>`，该接口只返回元数据，不含 README 正文。
2. README 正文需要下载归档并解包：

```text
https://pub.dev/api/archives/<name>-<ver>.tar.gz
```

注意版本号：带 `+` 的版本号（如 pub 的 build 号）在归档 URL 中要原样带上，不要转义或改写。

## 常见错误

| 错误 | 原因 | 处理 |
|------|------|------|
| curl 返回 403（crates.io） | 请求未带 User-Agent | 补上 User-Agent 再请求 |
| 解析取不到数据 | 按 PyPI 的 `{"crate": …}` 嵌套结构取值 | crates.io 返回是顶层结构，直接取字段 |
| `/api/packages/<name>` 里没有 README | 该接口只含元数据 | 下载 `archives/<name>-<ver>.tar.gz` 解包取正文 |
| 归档 404 | 版本号中的 `+` 被转义或改写 | URL 中原样保留带 `+` 的版本号 |
