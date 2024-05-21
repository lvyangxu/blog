---
title: 使用frp搭建内网http穿透
date: 2024-03-27 14:38:07
tags:
---

## 背景
开发过程中经常会遇到第三方http回调无法请求到内网地址的问题，本篇文章使用开源软件frp来解决该问题

## 什么是frp
1. 官方介绍：frp 是一个专注于内网穿透的高性能的反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等多种协议，且支持 P2P 通信。可以将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网。
2. github地址   https://github.com/fatedier/frp
3. 个人总结：http的反向代理功能，用起来感受和nginx差不多，都可以根据域名和路径来进行反向代理

## 准备工作
+ 一台公网linux服务器，作为frp的服务端（本篇以centos7作为示例，以下简称服务端）
+ 一台内网机器，作为frp的客户端（本篇以centos7作为示例，以下简称客户端）
+ 从该项目的github上release页面 https://github.com/fatedier/frp/releases 下载安装包，分别解压到服务端和客户端

## 服务端配置
+ 服务端使用根目录的frps.ini作为配置（注意和客户端的frpc.ini是2个文件），示例配置如下，其中
bind_port表示frp服务端监听端口
vhost_http_port表示第三方http回调的接收端口
authentication_method表示frp连接时使用token进行认证
token表示令牌，客户端和服务器必须统一

```shell
[common]
bind_port = 7000
vhost_http_port = 7001
authentication_method = token
token = yourfrplogintoken
```

+ 配置成systemctl服务，新建文件 /usr/lib/systemd/system/frps.service，示例中的安装目录为/root/frp，内容如下：
```shell
[Unit]
Description=frp server
After=syslog.target network.target

[Service]
WorkingDirectory=/root/frp
Type=simple
KillMode=mixed
Restart=on-abnormal
ExecStart=/root/frp/frps -c /root/frp/frps.ini
User=root

[Install]
WantedBy=multi-user.target
```

+ 重载配置，启动服务，并设置开机启动
```shell
systemctl daemon-reload
systemctl start frps
systemctl enable frps
```
查看服务日志和状态，以下服务日志表示启动成功
```shell
[root.go:213] frps started successfully
```
客户端配置
+ 客户端使用根目录的frpc.ini作为配置（注意和服务端的frps.ini是2个文件），示例配置如下，其中
server_addr是frp服务端监听地址
server_port是frp服务端监听端口
token表示令牌，客户端和服务器必须统一
yourconfigname表示你的配置名称
type表示反向代理的协议
local_ip和local_port表示代理的ip和端口，类似于nginx反代的proxy_pass参数
custom_domains表示反向代理的规则，请求时根据不同的域名分发到不同的客户端，类似于nginx站点的servername
location表示反向代理的规则，请求时根据不同的路径分发到不同的客户端，类似于nginx的location
```shell
[common]
server_addr = frpserverip
server_port = frpserverport
token = yourfrplogintoken

[yourconfigname]
type = http
local_ip = frpclientip
local_port = frpclientport
custom_domains = httpdomain
location = /yourlocation
```
+ 配置成systemctl服务，新建文件 /usr/lib/systemd/system/frpc.service，示例中的安装目录为/root/frp，内容如下：
```shell
[Unit]
Description=frp client
After=syslog.target network.target

[Service]
WorkingDirectory=/root/frp
Type=simple
KillMode=mixed
Restart=on-abnormal
ExecStart=/root/frp/frpc -c /root/frp/frpc.ini
User=root

[Install]
WantedBy=multi-user.target
```
+ 重载配置，启动服务，并设置开机启动
```shell
systemctl daemon-reload
systemctl start frps
systemctl enable frps
```
+ 查看服务日志和状态，以下服务日志表示启动成功，并且连上了服务端
```shell
[root.go:220] start frpc service for config file [/root/frp/frpc.ini]
[service.go:301] [19bec3545387f817] login to server success, get run id [19bec3545387f817]
[proxy_manager.go:150] [19bec3545387f817] proxy added: [yourconfigname]
[control.go:172] [19bec3545387f817] [yourconfigname] start proxy success
```
## 测试
推荐在frps的外层再套一层nginx，将站点的请求反代到frps
外网请求：http://custom_domains/location ，即可转发到内网  http://local_ip:local_port/location