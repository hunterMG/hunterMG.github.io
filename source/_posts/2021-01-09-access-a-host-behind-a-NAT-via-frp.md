---
title: 使用frp进行内网穿透
date: 2021-01-09 11:09:02
categories: note
tags:
- Linux
---

使用frp进行内网穿透，实现在外网访问位于内网没有公有IP的主机。
<!-- more -->

需要：有公有IP的外网主机一个, 作为 Server 转发内网主机的流量，可以自己买个配置低一点的VPS。

### 在公网主机上配置 Server 端：
- 从GitHub下载程序
```shell
wget https://github.com/fatedier/frp/releases/download/v0.34.3/frp_0.34.3_linux_amd64.tar.gz
tar xvzf frp_0.34.3_linux_amd64.tar.gz
cd frp_0.34.3_linux_amd64 
vim frps.ini
```

- Server 端配置(tcp模式)：  
（可以使用 stcp 模式给连接添加密钥，还有一种是 xtcp 通过 P2P 的方式连接，连接成功后不走服务器流量。但这两种方式需要在自己的机器上也运行一个 frpc，考虑到有时候可能需要用平板连，遂放弃。具体参考：[stcp](https://github.com/fatedier/frp#expose-your-service-privately), [xtcp](https://github.com/fatedier/frp#p2p-mode)）  
```shell
# frps.ini
[common]
bind_port = 7000
#dashboard_port = 7500
```
- 启动 Server 端:
```shell
./frps -c ./frps.ini
```

### 在内网主机上配置 Client 端
- 从github下载程序
```shell
wget https://github.com/fatedier/frp/releases/download/v0.34.3/frp_0.34.3_linux_amd64.tar.gz
tar xvzf frp_0.34.3_linux_amd64.tar.gz
cd frp_0.34.3_linux_amd64 
vim frpc.ini
```

- Client 端配置
```shell
# frpc.ini
[common]
server_addr = server.ip
server_port = 7000
login_fail_exit = false
[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000
```

- 启动 Client 端：
```shell
./frpc -c ./frpc.ini
```
可能需要在 VPS 的控制台上允许流量通过指定的端口(如该例中为 7000,6000)。 
### 在自己的机器上连接
```shell
ssh -oPort=6000 user@server.ip
```

### 开机自动启动服务
上面启动服务端和客户端的方式随着 Terminal 的关闭进程会停止，当然可以用 nohup 的方式在后台运行，但如果重启机器就不会自动启动。所以根据具体的 OS 配置开机自启服务。

#### Server(Debian 10)
- 安装supervisor
```shell
sudo apt install supervisor
sudo vim /etc/supervisor/conf.d/frp.conf
```

- frp.conf :
```shell
[program:frp]
command = /path/to/frps -c /path/to/frps.ini
autostart = true
```
- 查看状态
```shell
sudo systemctl restart supervisor
sudo supervisorctl status
```

#### Client(CentOS 7)
- 拷贝程序和配置文件并编辑service文件
```shell
sudo cp frpc /usr/bin/frpc
sudo cp frpc.ini /etc/frp/frpc.ini
sudo vim /usr/lib/systemd/system/frpc.service
```

- frpc.service :
```shell
[Unit]
Description=Frp Client Service
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frpc -c /etc/frp/frpc.ini
ExecReload=/usr/bin/frpc reload -c /etc/frp/frpc.ini
ExecStop=/bin/kill $MAINPID

[Install]
WantedBy=multi-user.target
```

- 启动frpc：
```shell
sudo systemctl enable frpc
sudo systemctl start frpc
sudo systemctl status frpc
```

### Reference
[https://github.com/fatedier/frp](https://github.com/fatedier/frp)  
[frp自动启动](https://free-e.net/109)  
