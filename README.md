### headscale-tools

一个部署headscale服务的脚本

```
wget -N --no-check-certificate https://raw.githubusercontent.com/sducxh/headscale-tools/main/main.sh && chmod +x main.sh && bash main.sh
```

### 修改版

fork 自 [yuehen7/headscale-tools](https://github.com/yuehen7/headscale-tools)

- 修改 `启动 headscale` sleep 时间：30s
- 最新版 (v0.22.3) 以 `user` 替代 `namespace`
- 修改注册、查看节点

### 客户端

[官网-下载页](https://tailscale.com/download)

- Android

> Google Play 下载 `tailscale`即可，支持自定义server

- Windows

> [Download for Windows](https://pkgs.tailscale.com/stable/tailscale-setup-latest.exe)

- linux

一键脚本
```shell
curl -fsSL https://tailscale.com/install.sh | sh
```

### 服务端命令

#### 查看端口

```shell
ss -tulnp|grep headscale
```

#### 查看运行状态

```shell
systemctl status headscale
```

#### SystemD 重新加载配置

```shell
systemctl daemon-reload
```

#### 查看user：新版以user代替namespace

```shell
headscale user list
```

#### 已注册节点

```shell
headscale nodes list
```

#### 修改节点名称

```shell
Usage:
  headscale nodes rename NEW_NAME [flags]
Flags:
-i, --identifier uint   Node identifier (ID)
```

```shell
# 将ID=1的节点重命名为 redmi-10x
headscale nodes rename redmi-10x -i 1
```

### 客户端命令

#### Windows

```shell
C:\Users\Administrator>tailscale
The easiest, most secure way to use WireGuard.

USAGE
  tailscale [flags] <subcommand> [command flags]

For help on subcommands, add --help after: "tailscale status --help".

This CLI is still under active development. Commands and flags will
change in the future.

SUBCOMMANDS
  up          Connect to Tailscale, logging in if needed
  down        Disconnect from Tailscale
  set         Change specified preferences
  login       Log in to a Tailscale account
  logout      Disconnect from Tailscale and expire current node key
  switch      Switches to a different Tailscale account
  configure   [ALPHA] Configure the host to enable more Tailscale features
  netcheck    Print an analysis of local network conditions
  ip          Show Tailscale IP addresses
  status      Show state of tailscaled and its connections
  ping        Ping a host at the Tailscale layer, see how it routed
  nc          Connect to a port on a host, connected to stdin/stdout
  ssh         SSH to a Tailscale machine
  funnel      Serve content and local servers on the internet
  serve       Serve content and local servers on your tailnet
  version     Print Tailscale version
  web         Run a web server for controlling Tailscale
  file        Send or receive files
  bugreport   Print a shareable identifier to help diagnose issues
  cert        Get TLS certs
  lock        Manage tailnet lock
  licenses    Get open source license information
  exit-node   Show machines on your tailnet configured as exit nodes
  update      Update Tailscale to the latest/different version
  whois       Show the machine and user associated with a Tailscale IP (v4 or v6)
  drive       Share a directory with your tailnet
  completion  Shell tab-completion scripts

FLAGS
  --socket value
        path to tailscaled socket
```

### 参考
[Tailscale 基础教程：Headscale 的部署方法和使用教程](https://icloudnative.io/posts/how-to-set-up-or-migrate-headscale)