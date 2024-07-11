---
title: k8s开发环境minikube搭建
date: 2024-03-27 16:40:32
tags:
---

## 概要
+ 系统 - win10
+ 容器 - docker
+ 目标 - 编排一个web前端+web后端编排的服务，并使用minikube运行
## 步骤
### 开启hyper-V
可以通过任务管理器判断hyper-v是否开启

<img src="./images/taskmanager.png" style="width:50%">

部分主板需要去bios里面打开cpu的虚拟化功能，然后使用powershell管理员模式运行下列命令开启hyper-v
```shell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
### 安装docker
去官网 https://www.docker.com/get-started 下载windows的安装文件，进行安装
### 安装minikube
参考官方文档 https://minikube.sigs.k8s.io/docs/start/ 的1、2、3步，进行安装
### 部署应用
使用以下命令通过镜像创建deployment，你也可以使用kubectl apply -f xxx.yaml 的方式进行创建
```shell
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
```
暴露端口
```shell
kubectl expose deployment hello-minikube --type=LoadBalancer --port=8080
```
启动tunnel
```shell
minikube tunnel
```
用浏览器访问 http://127.0.0.1:8080/ ，可获得返回，部署完成