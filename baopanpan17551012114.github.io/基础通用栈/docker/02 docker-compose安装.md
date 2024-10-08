https://blog.mutoe.com/2024/install-docker-compose-v2-in-macos/

# 在 macOS 上安装 Docker Compose v2

在 macOS 上很久了都没有用上 `docker compose` 这个子命令，今天终于把它解决了特此记录一下以备后续之需。

## [1. 找到适合你系统架构的镜像](https://blog.mutoe.com/2024/install-docker-compose-v2-in-macos/#1-找到适合你系统架构的镜像)

前往 Docker compose 官方仓库的 Release 页面，找到适合你系统架构的镜像下载地址，复制下载链接。

https://github.com/docker/compose/releases

macOS 系统架构为 `darwin`, 如果你是 M 系列芯片的 Mac，那么你需要下载 `darwin-aarch64` 版本的镜像。否则下载 `darwin-x86_64` 版本的镜像。

## [2. 下载并安装](https://blog.mutoe.com/2024/install-docker-compose-v2-in-macos/#2-下载并安装)

```
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL [粘贴你复制的链接] -o $DOCKER_CONFIG/cli-plugins/docker-compose
```

## [3. 添加执行权限](https://blog.mutoe.com/2024/install-docker-compose-v2-in-macos/#3-添加执行权限)

```
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
```

## [4. 验证安装](https://blog.mutoe.com/2024/install-docker-compose-v2-in-macos/#4-验证安装)

```
docker compose version
```

## [参考资料](https://blog.mutoe.com/2024/install-docker-compose-v2-in-macos/#参考资料)

- [Install the plugin manually - Docker](https://docs.docker.com/compose/install/linux/#install-the-plugin-manually)