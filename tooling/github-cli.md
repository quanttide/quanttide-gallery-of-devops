# GitHub CLI

- GitHub 没有「创建组织」的 API → REST/GraphQL/gh CLI 都做不到，只能在网页建。
- `gh search repos` 没有 stargazers 字段 → 查 stars / pushed_at / archived 用 `gh api repos/<owner>/<repo>`。
- `gh api …/contents/<path> -q .content | base64 -d` → 仓库或路径不存在时 gh 报 404，base64 随后连锁报 invalid input；第一个错误才是根因。
