# GitHub CLI

`gh` 日常使用中的三条边界：组织创建、仓库搜索字段、contents 接口的报错链。

## 组织只能在网页创建

- 现象：想用 API 建组织。
- 原因：GitHub 没有「创建组织」的 API。
- 做法：REST、GraphQL、`gh` CLI 三条路都做不到，只能在网页上创建。

## 搜索结果没有 stargazers 字段

- 现象：用 `gh search repos` 筛 star 数，拿不到。
- 原因：该命令没有 stargazers 字段。
- 做法：要查 stars、`pushed_at`、`archived` 这类仓库元信息，改用仓库详情接口：

```bash
gh api repos/<owner>/<repo>
```

## contents 接口：后面的报错是连锁反应

读取仓库文件的常用管道：

```bash
gh api …/contents/<path> -q .content | base64 -d
```

- 现象：仓库或路径不存在时，`gh` 先报 404，管道下游的 `base64` 随后报 `invalid input`。
- 原因：base64 的报错是连锁反应，第一个错误（404）才是根因。
- 做法：排查时从管道最左侧的命令看起，先确认仓库和路径是否存在。

## 常见错误

| 错误 | 原因 | 解决方案 |
|------|------|----------|
| REST/GraphQL/gh CLI 都建不了组织 | GitHub 没有「创建组织」的 API | 只能在网页创建 |
| `gh search repos` 结果里没有 stars | 该命令没有 stargazers 字段 | 用 `gh api repos/<owner>/<repo>` 查 stars / pushed_at / archived |
| `base64` 报 `invalid input` | 上游 `gh` 已报 404（仓库或路径不存在），属连锁报错 | 看第一个错误，确认仓库与路径是否存在 |
