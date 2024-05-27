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
- 文档记录客户端注册、路由转发

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
安装后运行注册
```shell
# 这里推荐将 DNS 功能关闭，因为它会覆盖系统的默认 DNS
# 将 <HEADSCALE_PUB_IP> 换成你的 Headscale 公网 IP 或域名
tailscale up --login-server=http://<HEADSCALE_PUB_IP>:8080 --accept-routes=true --accept-dns=false
```

- pve lxc

1. 先在pve主机执行, 记录 10, 200 这两个数字
```shell
root@pve:~# ls -al /dev/net/tun
crw-rw-rw- 1 root root 10, 200 Jun 30 23:08 /dev/net/tun
```

2. 在 pve 宿主机中，修改 /etc/pve/lxc/200.conf 文件（此处我的 `lxc` ID是200）， 新增如下两行
```shell
# 此处10:200是从上面命令获取的
lxc.cgroup2.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
```

3. 重启 `lxc` 容器
4. 进入 lxc 容器， 修改 /etc/sysctl.conf 配置文件，开启  lxc 的转发功能
```shell
# 解除注释即可
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1
```
5. 修改生效：
```shell
sysctl -p /etc/sysctl.conf
```
6. 启动 `tailscale`

- 路由转发

实现 `任一节点` 可访问家里局域网内服务

1. 在lxc客户端注册时添加 `--advertise-routes` 参数

```shell
# 我家里局域网：192.168.2.0/24
tailscale up --login-server=http://<YOUR_PUBLIC_IP>:8080 --accept-routes=true --accept-dns=false --advertise-routes=192.168.2.0/24 
```

2. 在 `headscale` 服务端注册节点
3. 查看路由

```shell
headscale routes list
# Enabled=false
```

4. 开启路由

```shell
headscale routes enable -r 1
```

### LOGS

#### lxc

如果没有将宿主机的`tun`挂载给lxc，会有如下错误

```shell
Installing Tailscale for debian bullseye, using method apt
+ mkdir -p --mode=0755 /usr/share/keyrings
+ curl -fsSL https://pkgs.tailscale.com/stable/debian/bullseye.noarmor.gpg
+ tee /usr/share/keyrings/tailscale-archive-keyring.gpg
+ curl -fsSL https://pkgs.tailscale.com/stable/debian/bullseye.tailscale-keyring.list
+ tee /etc/apt/sources.list.d/tailscale.list
# Tailscale packages for debian bullseye
deb [signed-by=/usr/share/keyrings/tailscale-archive-keyring.gpg] https://pkgs.tailscale.com/stable/debian bullseye main
+ apt-get update
Hit:1 https://mirrors.ustc.edu.cn/debian bullseye InRelease
Hit:2 https://mirrors.ustc.edu.cn/debian-security bullseye-security InRelease
Get:3 https://mirrors.ustc.edu.cn/debian bullseye-updates InRelease [44.1 kB]
Hit:4 https://repo.huaweicloud.com/docker-ce/linux/debian bullseye InRelease
Get:5 https://mirrors.ustc.edu.cn/debian bullseye-backports InRelease [49.0 kB]
Get:6 https://mirrors.ustc.edu.cn/debian bullseye-backports/main Translation-en.diff/Index [63.3 kB]
Get:7 https://mirrors.ustc.edu.cn/debian bullseye-backports/main Translation-en T-2024-05-23-0834.25-F-2024-05-23-0834.25.pdiff [613 B]
Get:7 https://mirrors.ustc.edu.cn/debian bullseye-backports/main Translation-en T-2024-05-23-0834.25-F-2024-05-23-0834.25.pdiff [613 B]
Get:8 https://pkgs.tailscale.com/stable/debian bullseye InRelease                
Get:9 https://pkgs.tailscale.com/stable/debian bullseye/main amd64 Packages [10.9 kB]
Get:10 https://pkgs.tailscale.com/stable/debian bullseye/main all Packages [354 B]
Fetched 175 kB in 3s (51.2 kB/s)   
Reading package lists... Done
+ apt-get install -y tailscale tailscale-archive-keyring
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following package was automatically installed and is no longer required:
  libfuse2
Use 'apt autoremove' to remove it.
The following NEW packages will be installed:
  tailscale tailscale-archive-keyring
0 upgraded, 2 newly installed, 0 to remove and 6 not upgraded.
Need to get 27.3 MB of archives.
After this operation, 50.6 MB of additional disk space will be used.
Get:2 https://pkgs.tailscale.com/stable/debian bullseye/main all tailscale-archive-keyring all 1.35.181 [3082 B]
Get:1 https://pkgs.tailscale.com/stable/debian bullseye/main amd64 tailscale amd64 1.66.4 [27.3 MB] 
Fetched 27.3 MB in 10s (2686 kB/s)                                                                                                                                                       
Selecting previously unselected package tailscale.
(Reading database ... 23827 files and directories currently installed.)
Preparing to unpack .../tailscale_1.66.4_amd64.deb ...
Unpacking tailscale (1.66.4) ...
Selecting previously unselected package tailscale-archive-keyring.
Preparing to unpack .../tailscale-archive-keyring_1.35.181_all.deb ...
Unpacking tailscale-archive-keyring (1.35.181) ...
Setting up tailscale-archive-keyring (1.35.181) ...
Setting up tailscale (1.66.4) ...
Created symlink /etc/systemd/system/multi-user.target.wants/tailscaled.service -> /lib/systemd/system/tailscaled.service.
+ [ false = true ]
+ set +x
Installation complete! Log in to start using Tailscale by running:

tailscale up

root@docker:~# systemctl status tailscaled      
* tailscaled.service - Tailscale node agent
     Loaded: loaded (/lib/systemd/system/tailscaled.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2024-05-24 15:43:04 CST; 375ms ago
       Docs: https://tailscale.com/kb/
   Main PID: 933658 (tailscaled)
      Tasks: 7 (limit: 18933)
     Memory: 3.9M
        CPU: 110ms
     CGroup: /system.slice/tailscaled.service
             `-933658 /usr/sbin/tailscaled --state=/var/lib/tailscale/tailscaled.state --socket=/run/tailscale/tailscaled.sock --port=41641

May 24 15:43:04 docker tailscaled[933658]: dns: using "direct" mode
May 24 15:43:04 docker tailscaled[933658]: dns: using *dns.directManager
May 24 15:43:04 docker tailscaled[933658]: wgengine.NewUserspaceEngine(tun "tailscale0") ...
May 24 15:43:04 docker systemd[1]: Started Tailscale node agent.
May 24 15:43:04 docker tailscaled[933658]: Linux kernel version: 5.19.17-2-pve
May 24 15:43:04 docker tailscaled[933658]: is CONFIG_TUN enabled in your kernel? `modprobe tun` failed with: modprobe: FATAL: Module tun not found in directory /lib/modules/5.19.17-2-pve
May 24 15:43:04 docker tailscaled[933658]: tun module not loaded nor found on disk
May 24 15:43:04 docker tailscaled[933658]: wgengine.NewUserspaceEngine(tun "tailscale0") error: tstun.New("tailscale0"): CreateTUN("tailscale0") failed; /dev/net/tun does not exist
May 24 15:43:04 docker tailscaled[933658]: flushing log.
May 24 15:43:04 docker tailscaled[933658]: logger closing down
```

### 参考
[Tailscale 基础教程：Headscale 的部署方法和使用教程](https://icloudnative.io/posts/how-to-set-up-or-migrate-headscale)
[PVE LXC 安装 tailscale](https://acchw.top/posts/3940/index.html)