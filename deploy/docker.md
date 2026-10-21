# docker

- `docker pull` 的进度写在 containerd 存储（不在 `/var/lib/docker`），普通用户读不到；国内镜像对 `sickcodes/docker-osx` 一律拉不动，且它没有 `sonoma` 这类版本 tag（README 里的 Sonoma/Ventura 只是 SHORTNAME 示例）。
