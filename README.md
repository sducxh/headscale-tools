# headscale-tools

一个部署headscale服务的脚本

```
wget -N --no-check-certificate https://raw.githubusercontent.com/yuehen7/headscale-tools/main/main.sh && chmod +x main.sh && bash main.sh
```

## 修改版

fork 自 [yuehen7/headscale-tools](https://github.com/yuehen7/headscale-tools)

- 修改 `启动 headscale` sleep 时间：30s
- 最新版以 `user` 替代 `namespace`

## 查看端口

```shell
ss -tulnp|grep headscale
```

## 查看运行状态

```shell
systemctl status headscale
```

## SystemD 重新加载配置

```shell
systemctl daemon-reload
```

## 查看user：新版以user代替namespace

```shell
headscale user list
```

## 已注册节点

```shell
headscale nodes list
```