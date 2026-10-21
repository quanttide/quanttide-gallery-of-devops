# cargo 构建陷阱

cargo 的默认行为在锁文件、工作目录、增量编译和打包数据上有不少反直觉之处，出错信息往往指向误导性的结论。本文记录 7 个实测踩过的坑，涉及锁文件、工作目录、增量编译、文档注释、clippy、打包数据与元数据同步。

## 规则

- `--locked` 只在 `Cargo.lock` 已存在时可用
- cargo 命令必须在含 `Cargo.toml` 的目录跑
- 增量编译的 `Fresh` 状态和「秒过」的测试都可能是假象
- `pub mod` 不要手写 `///`，模块级 `//!` 已经是文档
- 二进制里不要用 `env!("CARGO_MANIFEST_DIR")` 定位运行时数据
- 改 `Cargo.toml` 元数据后必须全仓 grep 旧值

## `--locked` 与尚未生成的锁文件

现象：

```text
cannot create the lock file … because --locked was passed
```

原因：`cargo … --locked` 在 `Cargo.lock` 尚未生成时会直接报错。

处理：新建包或改过包名后，先不带 `--locked` 跑一次生成锁文件，再回到门禁流程。

## 必须在含 `Cargo.toml` 的目录跑

现象：在仓库根目录跑 cargo 命令报 `could not find Cargo.toml`。

原因：`cargo` 要在含 `Cargo.toml` 的目录跑。

处理：`qtcloud-*` 多组件仓的 `Cargo.toml` 在 `src/cli/`，进该目录再执行 cargo 命令。

## `Fresh` 与「全部通过」的旧测试二进制

现象：写完文件后 cargo 仍报 `Fresh`，旧的测试二进制跑出「全部通过」的结果。

原因：源文件 mtime 变了但没有触发重编，这个现象极具误导性。

处理：

```bash
cargo clean -p <pkg>
```

清掉该包后重跑。改动后测试「秒过」就要怀疑没有真正重编。

## 给 `pub mod` 手写 `///` 注释

不要给 `pub mod` 手写 `///` 注释，会破坏该模块 `//!` 里 `[`Step`]` 这类 in-scope 链接（rustdoc unresolved link）。模块级 `//!` 已经是文档，不用再加。

## `clippy -D warnings` 常见拦点

`cargo clippy -D warnings` 的常见拦点：

| lint | 说明 |
|------|------|
| `ok_expect` | `.ok().expect()` 写法 |
| `enum_variant_names` | 变体名与枚举名重复 |
| `ptr_arg` | `&mut String` 参数；宏生成的用 `#[allow]` 挂在方法上 |

## `env!("CARGO_MANIFEST_DIR")` 定位数据文件

现象：用 `env!("CARGO_MANIFEST_DIR")` 定位数据文件，二进制运行时报 No such file。

原因：从 crates.io 装出的二进制里没有源码树。

处理：要让数据随包走，就用 `include_str!` 编进二进制。

## 改 `Cargo.toml` 元数据后全仓 grep 旧值

改 `Cargo.toml` 元数据（edition、版本、包名）后，全仓 grep 旧值：文档与 dev-guide 里常留着旧号码。

## 常见错误速查

| 现象 | 原因 | 处理 |
|------|------|------|
| `cannot create the lock file … because --locked was passed` | `Cargo.lock` 尚未生成 | 先不带 `--locked` 跑一次生成锁文件 |
| `could not find Cargo.toml` | 不在含 `Cargo.toml` 的目录 | `qtcloud-*` 进 `src/cli/` 再跑 |
| 报 `Fresh` 且测试「全部通过」 | 源文件变了但没重编 | `cargo clean -p <pkg>` 后重跑 |
| rustdoc unresolved link | 给 `pub mod` 手写 `///` 破坏了 `//!` 的 in-scope 链接 | 删掉 `///`，模块级 `//!` 已是文档 |
| clippy 报 `ok_expect` / `enum_variant_names` / `ptr_arg` | `-D warnings` 把常见写法拦下 | 按 lint 逐个修正，宏生成的用 `#[allow]` 挂在方法上 |
| 运行时报 No such file | 二进制里没有源码树，`env!("CARGO_MANIFEST_DIR")` 失效 | 用 `include_str!` 编进二进制 |
| 文档与 dev-guide 里是旧号码 | 改了 `Cargo.toml` 元数据但没同步 | 全仓 grep 旧值并替换 |
