# 量潮DevOps工程案例

## 定位

研发流程的自动化与形式化：让规划、编码、构建、测试、发布、部署、运维、监控八阶段里的每一步都能被工具自动、可重复、可审计地完成。目录按阶段划分，另设平台一层收 DevOps 依赖的环境与服务；阶段之内一个主题一个文件。

## 分类标准

先归阶段，再看主题。阶段回答「这条经验挡住的是哪一步」，主题回答「它讲的是哪一个对象」，文件名只写对象，不重复动作。所有条目都写成完整文章：流程性内容写成工作流，单点经验展开为「现象 → 原因 → 处理」。

同一做法在多个阶段都成立、且属于流程所依赖的平台（shell、GitHub、飞书等），归平台。

## 目录

### 编码

- [push.md](code/push.md)：推送与克隆。
- [submodule.md](code/submodule.md)：子模块。
- [move.md](code/move.md)：文件移动与改名。
- [commit.md](code/commit.md)：提交与锁。

### 构建

- [cargo.md](build/cargo.md)：cargo 与制品。
- [flutter.md](build/flutter.md)：Flutter 构建产物。
- [python.md](build/python.md)：Python 包管理。
- [dependencies.md](build/dependencies.md)：依赖源。
- [myst.md](build/myst.md)：MyST 写入。

### 测试

- [screenshot.md](test/screenshot.md)：截图与窗口注入。

### 部署

- [myst.md](deploy/myst.md)：MyST 文档站点发布，流程案例。
- [aliyun.md](deploy/aliyun.md)：aliyun CLI。
- [snap.md](deploy/snap.md)：snap。
- [docker.md](deploy/docker.md)：docker。
- [terraform.md](deploy/terraform.md)：Terraform。

### 运维

- [process.md](operate/process.md)：进程。
- [service.md](operate/service.md)：服务挂起。
- [profiling.md](operate/profiling.md)：Python 进程诊断。

### 平台

DevOps 依赖的平台，不对应单一阶段。

- [shell.md](platform/shell.md)：命令行。
- [github-cli.md](platform/github-cli.md)：GitHub CLI。
- [lark-cli.md](platform/lark-cli.md)：lark-cli。

## 关于空阶段

规划、发布、监控三站暂无条目。语料来自失败记录，失败集中在版本控制、构建与外部工具链；规划阶段的问题多是判断，监控阶段的问题多以指标形式存在，两者都没有沉淀成「现象 → 做法」。这里留空，不硬凑。

## 来源

踩坑清单自 `quanttide-founder` 的案例集迁入，共 46 条；与代码、智能体两份同出 `data/profile/pi/pitfalls.md` 的 78 条汇总。
