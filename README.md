# Lion

## 介绍
Lion 是 JumpServer 图形协议的连接组件，支持 RDP、VNC 协议。

Lion 使用 Golang 和 Vue 重构 Guacamole-client，名字来源于 Dota 英雄 [Lion](https://www.dota2.com/hero/lion)。

该仓库主要用于配置介绍和 Release 发布。

## 配置

Lion 的启动配置，参考 [config_example](config_example.yml)

## Docker 镜像 (推荐)

可根据 JumpServer 版本获取对应的镜像，例如：获取 v2.10.0 版本的镜像
```shell
docker pull jumpserver/lion:v2.10.0
```

docker 启动

```shell
docker run -d --name jms_lion -p 8081:8081 \
-v $(pwd)/data:/opt/lion/data \
-v $(pwd)/config.yml:/opt/lion/config.yml \
jumpserver/lion:v2.10.0
```

## 原生安装

### 安装 Guacamole Server
Lion 是基于 [Apache Guacamole](http://guacamole.apache.org/) 开发，原生部署 Lion 需要有 [Guacamole Server](https://github.com/apache/guacamole-server) （版本 >= 1.3.0）。
[Guacamole Server](https://github.com/apache/guacamole-server) 安装部署可参考 [Guacamole 官网](https://guacamole.apache.org/doc/gug/installing-guacamole.html)

### 安装 Lion（以 v2.10.0 为例）
从 Release 页面下载 JumpSever 对应版本的 Lion。下载 `lion-v2.10.0-linux-amd64.tar.gz` 到服务器，并解压到 `/opt` 目录下

```shell
tar -zxvf lion-v2.10.0-linux-amd64.tar.gz -C /opt/
```

执行 `cd /opt/lion-v2.10.0-linux-amd64`  进入 Lion 目录，`touch config.yml` 创建文件，添加必要的参数配置
```shell
CORE_HOST: http://127.0.0.1:8080 # JumpServer 的 API 地址

BOOTSTRAP_TOKEN: <PleasgeChangeSameWithJumpserver> # 注册使用的预共享秘钥
```

启动
```shell
./lion
```

#### 使用系统服务方式启动

在 `/etc/systemd/system` 目录创建 `lion-v2.10.0.service` 文件并配置以下内容
```shell
[Unit]
Description=JumpServer Lion Service
After=network.target

[Service]
Type=simple
User=root
Group=root

WorkingDirectory=/opt/lion-v2.10.0-linux-amd64
ExecStart=/opt/lion-v2.10.0-linux-amd64/lion -f config.yml
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

重载系统服务 `systemctl daemon-reload`

启动 Lion 服务 `systemctl start lion-v2.10.0`

查看 Lion 服务状态 `systemctl status lion-v2.10.0`

#### 注意事项

如果 Guacamole server 以 docker 方式启动，需要挂载 Lion 目录下的data路径

```shell
docker run -d -p 4200:4200 -v /opt/lion-v2.10.0-linux-amd64/data:/opt/lion-v2.10.0-linux-amd64/data guacamole/guacd:1.3.0
```

## 致谢

感谢以下项目，带来的启发
- [guacamole-client](https://github.com/apache/guacamole-client)
- [next-terminal](https://github.com/dushixiang/next-terminal)
