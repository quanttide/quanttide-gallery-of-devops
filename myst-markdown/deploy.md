# MyST 文档部署

MyST 文档站点发布到 GitHub Pages 的标准流程。

## 规则

- `myst.yml` 放在文档项目根目录
- 构建产物 `_build/` 必须加入 `.gitignore`
- GitHub Pages 使用 GitHub Actions 构建，不推送构建产物到仓库
- 每次推送 `main` 分支自动触发部署

## 依赖

- GitHub CLI (`gh`)：启用 Pages、管理 Release
- MyST CLI (`myst`)：本地验证构建

## 工作流

### 1. 创建 `myst.yml`

在文档项目根目录创建 `myst.yml`：

```yaml
version: 1
project:
  title: 文档标题
  description: 文档描述
  keywords: []
  authors:
    - name: 作者名
  license: CC-BY-4.0
  toc:
    - file: README.md
    - title: 章节一
      children:
        - file: section1/index.md
site:
  template: book-theme
```

关键字段：

| 字段 | 说明 |
|------|------|
| `project.toc` | 目录结构，明确列出所有页面 |
| `project.exclude` | 排除非文档文件（可选） |
| `site.template` | 网站模板，默认 `book-theme` |

### 2. 本地验证构建

```bash
# 构建静态 HTML 站点
myst build --html
```

构建成功后 `_build/html/` 下生成 HTML 静态文件。可用 `npx serve _build/html` 本地预览。

> `myst build --site` 生成的是 MyST 服务器运行时数据（输出到 `_build/site/`），不包含 HTML，**不能用于静态托管**。

### 3. 添加 `.gitignore`

```gitignore
_build/
```

### 4. 启用 GitHub Pages

```bash
# 使用 GitHub Actions 构建模式
gh api repos/<owner>/<repo>/pages -X POST -f build_type=workflow
```

或在仓库 Settings → Pages → Source 选择 **GitHub Actions**。

### 5. 创建部署工作流

```bash
# 也可用 MyST 官方命令自动生成
myst init --gh-pages
```

或手动创建 `.github/workflows/deploy.yml`，参考 [MyST 官方模板](https://mystmd.org/guide/deployment-github-pages)：

```yaml
# This file was created automatically with `myst init --gh-pages`

name: MyST GitHub Pages Deploy
on:
  push:
    branches: [main]

env:
  BASE_URL: /${{ github.event.repository.name }}

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Pages
        uses: actions/configure-pages@v3
      - uses: actions/setup-node@v4
        with:
          node-version: 18.x
      - name: Install MyST Markdown
        run: npm install -g mystmd
      - name: Build HTML Assets
        run: myst build --html
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./_build/html
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

关键区别：

| 部署方式 | 命令 | 产物路径 |
|----------|------|----------|
| **静态站点（GitHub Pages）** | `myst build --html` | `_build/html` |
| MyST 服务器（Curvenote） | `myst build --site` | `_build/site` |

### 6. 推送并触发部署

```bash
git add -A && git commit -m "ci: 配置 MyST 文档站点部署"
git push
```

推送后 GitHub Actions 自动执行：安装 `mystmd` → `myst build --html` 构建静态 HTML → 部署到 Pages。

### 7. 验证部署

```bash
# 查看 Pages 状态
gh api repos/<owner>/<repo>/pages --jq '{html_url, build_type, source}'

# 等待 Actions 完成
gh run list --repo <owner>/<repo> --workflow deploy.yml --limit 1
```

## 常见错误

| 错误 | 原因 | 解决方案 |
|------|------|----------|
| `myst: command not found` | Node.js 未安装 | 确保 workflow 中安装 Node.js |
| Pages 返回 404 | 用了 `myst build --site` 而非 `--html` | `--site` 输出服务器数据（无 HTML），改用 `--html` 输出到 `_build/html` |
| Pages 返回 404 | `upload-pages-artifact` 路径错误 | 路径必须指向 `./_build/html` |
| 路径 `/repo-name` 不匹配 | 缺少 `BASE_URL` | workflow 中设置 `BASE_URL: /${{ github.event.repository.name }}` |
| 子模块未构建 | 未 `checkout` 子模块 | 在 checkout 步骤加 `submodules: true` |
