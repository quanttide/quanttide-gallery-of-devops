# 部署与基础设施

- `aliyun` CLI：很多产品是「插件可装但未安装」，未装时命令集不全；`domain` 产品没有询价接口，查价走 `bssopenapi QuerySkuPriceList`（参数是 `--CommodityCode/--PriceEntityCode`）。
- `snap saved --list` 不存在（unknown flag）→ 直接 `snap saved` 列出快照，`snap forget <组号>` 删。
- `docker pull` 的进度写在 containerd 存储（不在 `/var/lib/docker`），普通用户读不到；国内镜像对 `sickcodes/docker-osx` 一律拉不动，且它没有 `sonoma` 这类版本 tag（README 里的 Sonoma/Ventura 只是 SHORTNAME 示例）。
- Terraform 拉 alicloud provider 超时 → 走本地文件系统镜像（把 provider 解到 `~/.terraform.d/plugin-cache/…` 后用 `-plugin-dir`）。
