# Flutter 构建产物

`flutter build linux` 在 Linux 上产出可直接分发的 bundle 目录。本文记录产物路径、可执行文件的命名规则，以及定位产物的完整流程。

## 规则

- `flutter build linux` 产出的是一个 bundle 目录，不是单个二进制文件
- bundle 目录名取项目名，可执行文件名由 `--project-name` 决定

## 工作流

### 1. 执行构建

```bash
flutter build linux
```

构建完成后产物落在：

```text
build/linux/x64/debug/bundle/<project_name>
```

即 `build/linux/x64/debug/bundle/` 下以项目名命名的子目录。

### 2. 指定可执行文件名（可选）

可执行文件的名字由构建参数 `--project-name` 决定，而不是仓库目录名：

```bash
flutter build linux --project-name <project_name>
```

分发或启动脚本按 `<project_name>` 定位可执行文件即可。

### 3. 定位产物

```bash
ls build/linux/x64/debug/bundle/<project_name>
```

确认 bundle 目录内容后再打包分发。

## 关键信息

| 项目 | 规则 |
|------|------|
| 产物根目录 | `build/linux/x64/debug/bundle/` |
| bundle 子目录名 | `<project_name>` |
| 可执行文件名 | 由 `--project-name` 决定 |

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| 在预期位置找不到产物 | 产物路径固定含 `x64`、`debug`、`bundle` 层级 | 按 `build/linux/x64/debug/bundle/<project_name>` 定位 |
| 找到 bundle 但可执行名不符 | 可执行名由 `--project-name` 决定，与仓库目录名无关 | 按 `--project-name` 传入的名字取可执行文件 |
