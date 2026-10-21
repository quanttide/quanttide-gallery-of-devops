# snap

`snap saved` 子命令的常见误用：`--list` flag 不存在，直接不带参数即可列出快照。

## 规则

- `snap saved --list` 不存在（unknown flag）
- 列出快照直接 `snap saved`
- 删除快照用 `snap forget <组号>`

## 工作流

### 1. 列出快照

```bash
snap saved
```

不要加 `--list`：该 flag 不存在，会报 unknown flag。

### 2. 删除快照

按列出的组号删除：

```bash
snap forget <组号>
```

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| `snap saved --list` 报 unknown flag | 该子命令没有 `--list` flag | 直接 `snap saved` 列出快照 |
