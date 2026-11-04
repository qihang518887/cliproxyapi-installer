# CLIProxyAPI Installer

[CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) 一键安装与升级脚本，自动适配 root 和普通用户的 systemd 服务。

## 安装 / 升级

```bash
curl -fsSL https://raw.githubusercontent.com/qihang518887/cliproxyapi-installer/refs/heads/master/cliproxyapi-installer | bash
```

支持 Linux x86_64、aarch64，需要 Bash、curl 或 wget、tar。上面的命令使用 curl。

- 安装目录：当前账号的 `~/cliproxyapi`。
- 升级保留 `config.yaml`，自动重启升级前正在运行的 systemd 服务。
- root 使用系统级服务，普通用户使用用户级服务；旧的 root 用户服务会自动迁移。

## 配置与登录

```bash
cd ~/cliproxyapi
nano config.yaml

# 按需选择提供商登录
./cli-proxy-api --codex-login --no-browser
./cli-proxy-api --claude-login --no-browser
./cli-proxy-api --login --no-browser
```

首次安装会生成访问代理使用的 API key，可在 `config.yaml` 的 `api-keys` 中查看或修改。提供商认证及其他配置见 [上游文档](https://github.com/router-for-me/CLIProxyAPI)。

## 启动服务

**root 安装：**

```bash
systemctl enable --now cliproxyapi.service
systemctl status cliproxyapi.service
journalctl -u cliproxyapi.service -f
```

**普通用户安装：**

```bash
systemctl --user enable --now cliproxyapi.service
systemctl --user status cliproxyapi.service
journalctl --user -u cliproxyapi.service -f
```

普通用户需要退出登录后继续运行、开机无需登录即启动时，由管理员执行：

```bash
sudo loginctl enable-linger "$(id -un)"
```

## Alpine

先安装依赖，再执行上面的安装命令：

```sh
apk add bash curl tar
```

脚本会选择 musl 适用的 `no-plugin` 版本，不支持动态库插件。没有 `systemctl` 时跳过服务配置，**当前不提供 OpenRC 服务托管**，可直接运行：

```bash
cd ~/cliproxyapi
./cli-proxy-api
```