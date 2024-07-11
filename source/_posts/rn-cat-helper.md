---
title: 学习react native
date: 2024-03-10 13:47:56
tags:
---

## 简介

通过做一个app，来熟悉react native开发
github地址:  https://github.com/lvyangxu/rn-cat-helper

## 需求

家里养了猫，做一个猫猫助手，定期提醒给猫猫驱虫/打疫苗
业务需求如下：
+ 体外驱虫1个月/次
+ 体内驱虫3个月/次
+ 狂犬疫苗11个月/次
+ 猫三联11个月/次（第三次以后，不考虑幼猫，因为会引入猫的年龄）
+ 各种驱虫/疫苗行为需要间隔一周

## 开发环境搭建
1. 根据react native官网文档，有expo go和react native cli两种方案，这里我选择了后者，使用windows+android手机调试的方案
2. 安装node和jdk环境，我不太喜欢Chocolatey，使用的nvm安装node，java是直接去官网下的，设置好环境变量就行
3. 安装android studio，官网下载安装即可，推荐安装目录和android sdk目录都不要设置在C盘，不然后期会很大。我有自己的安卓手机，不需要安装虚拟device，这部分安装时也可以去掉。
4. 安装react native官网文档，使用脚手架初始化工程，并启动项目。这里会去下载诸如gradle相关的依赖，没有翻墙大概率会失败，需要一个梯子。等待了几个小时后，我安装完成了。

5. debug工具。launch.json配置如下：
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach to Hermes application - Experimental",
            "request": "attach",
            "type": "reactnativedirect",
            "cwd": "${workspaceFolder}"
        },
    ]
}
```

6. 调试demo，手机连上电脑开启usb调试，工程节目输入npm start，然后再输入a，等待电脑向手机安装app，成功完成搭建。

<img src="./images/demo-debug.jpg" style="width:50%">

## 第三方依赖库

本地存储： async-storage
日期控件： react-native-calendars 
常用组件： React Native Elements
图片裁剪： react-native-image-crop-picker
js工具:   lodash

## 学习总结
1.原生组件用的最多的是View和ScrollView，View类似于web里面的div。
2.布局方案为flex，且默认方向是竖向。不支持grid布局。
3.react api的使用基本和web一样，平移过来很快；我做完这个应用一共花了一周，主要时间消耗在寻找第三方库上面了。