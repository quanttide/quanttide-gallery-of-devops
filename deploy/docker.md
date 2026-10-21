# docker

`docker pull` 在国内网络环境下的两个坑：进度数据的位置，以及 `sickcodes/docker-osx` 这类镜像的拉取限制。

## 规则

- `docker pull` 的进度写在 containerd 存储，不在 `/var/lib/docker`
- 普通用户读不到 containerd 存储里的进度数据
- 国内镜像对 `sickcodes/docker-osx` 一律拉不动
- `sickcodes/docker-osx` 没有 `sonoma` 这类版本 tag

## 进度写在 containerd 存储

`docker pull` 的进度数据写在 containerd 存储（不在 `/var/lib/docker`）。想在拉取过程中查看进度细节，普通用户权限读不到对应位置，需要提升权限；不要去 `/var/lib/docker` 找。

## sickcodes/docker-osx 拉不动

国内镜像源对 `sickcodes/docker-osx` 一律拉不动，换源解决不了。另外，README 里的 Sonoma/Ventura 只是 SHORTNAME 示例，该镜像没有 `sonoma` 这类版本 tag，按版本 tag 拉取会直接失败。

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| 在 `/var/lib/docker` 找不到 pull 进度 | 进度写在 containerd 存储，普通用户读不到 | 提升权限后再查 containerd 存储 |
| 换国内镜像源仍拉不动 `sickcodes/docker-osx` | 国内镜像对该镜像一律拉不动 | 不要指望换源解决 |
| 按 `sonoma` 这类 tag 拉取失败 | 镜像没有版本 tag，README 里只是 SHORTNAME 示例 | 放弃按版本 tag 拉取 |
