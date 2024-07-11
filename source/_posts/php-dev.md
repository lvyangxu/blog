---
title: php开发环境搭建
date: 2024-03-27 17:05:23
tags: 程序语言
---

## 概要
+ 系统 - win10 
+ ide  - vscode 
+ 目标 - 能启动应用，并进行断点调试 

## 步骤
### 下载php
在官网 https://windows.php.net/downloads/releases/archives/ 选择你想要的版本，我选择的是7.2
### 解压并配置环境变量
解压zip文件，并将解压后的目录添加到环境变量path中，然后打开命令行进行验证
```shell
php -v
```
如果配置正确，会输出版本信息
```shell
PHP 7.2.34 (cli) (built: Oct  1 2020 10:24:14) ( ZTS MSVC15 (Visual C++ 2017) x64 )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
```
### 下载xdebug
在官网 http://xdebug.org/download 选择和php版本对应xdebug，我选择的是php 7.2 vc15的ts版本，下载后将dll文件拷贝到php目录的ext下
### 修改php配置
进入php目录，拷贝php.ini-development为php.ini，打开php.ini，在末尾追加xdebug配置
```shell
[Xdebug]
zend_extension=D:\php-7.2.34-Win32-VC15-x64\ext\php_xdebug-3.1.3-7.2-vc15-x86_64.dll
xdebug.mode=develop,debug
xdebug.start_with_request=yes
```
注意：需要将zend_extension的值设置为xdebug.dll的路径，上面是我自己的路径，可通过以下命令来判断版本是否匹配，如果不匹配会报错；另外我使用的是xdebug3，与2的配置有一些区别
```shell
php -ini | findstr xdebug
```
### 安装composer
在官网下载安装文件 https://getcomposer.org/Composer-Setup.exe ，直接进行安装。安装最后一步等待时间有点长，如果失败了多试几次
### 测试
先启动你的php应用，我用的是laravel框架，使用vscode的任务调用命令
```shell
php artisan serve
```
配置应用根目录.vscode/launch.json
```shell
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9003
        },
    ]
}
```
vscode安装扩展PHP Debug后，开启调试进程，即可进行断点调试，示例图如下：

<img src="./images/php-dev/breakpoint.png" style="width:50%">
