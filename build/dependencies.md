# 依赖源

- crates.io API 必须带 User-Agent（curl 403），且返回结构是顶层不是 PyPI 那种 `{"crate": …}` 嵌套。
- Dart/pub.dev 取数：`/api/packages/<name>` 只返回元数据、不含 README；要正文得下 `https://pub.dev/api/archives/<name>-<ver>.tar.gz` 解包（带 `+` 的版本号要原样带）。
