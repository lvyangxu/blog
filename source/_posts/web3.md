---
title: web3.0学习
date: 2024-05-09 12:03:51
tags:
---

## 背景
现在越来越多的地方都在提web3.0，本篇文章非常粗糙的介绍下概念，重点放在智能合约的开发上。

## 什么是web3.0
web3.0的概念在2021年被广泛使用，它指用户不但可以在互联上读取（web1.0，称为静态互联网）、交互信息(web2.0，称为平台互联网)，还可以传递资产和通过通证token拥有互联网本身，web3.0称为价值互联网。

## 底层技术总结
+ web3.0的底层技术是区块链，它是一种块链式存储、不可篡改、安全可信的去中心化分布式账本。它的去中心化、透明性、不可篡改性确认了用户对数据的权利。
+ 然而区块链体系有几个缺点：图灵完备性缺失、价值盲点、状态缺失、区跨链盲点。为了解决这些缺点，出现了以太坊平台，平台主要包括【去中心化应用程序DApp】、【编程语言Solidity】、【以太坊虚拟机】、【以太币】这四个部分。
+ 以太坊运行原理和区块链类似，通过【工作量证明机制】或【权益证明机制】来维系交易。程序员通过solidity等编程语言编写智能合约，安装和运行在各节点与客户端上。以太坊也有缺点，随着用户越来越多，以太坊变得拥挤，当前架构支撑不了如此数量的用户。现在解决方案多为使用Layer2方案，不过也有一些其他缺点，这块需要观望技术的后续发展。

## 开发实战
### 目标
使用Solidity语言来开发一个投票系统

### 架构分析
+ 去中心化应用程序DApp，可以理解为仅后端是去中心化的，对标的是传统的web服务器，也是提供接口给页面调用。页面的工作方式还是和web2.0没有任何区别。
+ 在这次实战中，我们本地通过vite启动一个页面，hardhat启动一个本地节点，将合约部署到本地节点。页面通过web3js库对合约发起jsonrpc调用，进行投票和获取数据

### 发布到以太坊或者测试链Sepolia
以太坊的工作方式决定了发布DApp需要支付以太币。而测试链Sepolia虽然免费，但相关的几个水龙头都要求你的以太坊账号至少有0.001 ETH。本着白嫖的原则，这里只好点到为止。

### 技术点及地址
+ vue 前端框架，地址 https://cn.vuejs.org/
+ vuetify vue的组件库，地址 https://vuetifyjs.com/en/
+ solidity 以太坊的合约开发语言，地址 https://soliditylang.org/
+ hardhat solidity的开发框架，继承了合约测试、本地测试链等功能，地址 https://hardhat.org/
+ web3js web前端的合约库，可以通过jsonrpc对合约api进行调用 ，地址 https://docs.web3js.org/

### 开发环境搭建

#### ide及插件
这里使用vscode，需要安装【Solidity】扩展，来对合约语法进行提示

#### nodejs
使用nvm安装一个最新稳定版即可，hardhat虽然会有警告说最新版本可能会有问题，实际上不影响

#### 初始化hardhat
+ 使用npm全局安装hardhat

```shell
npm i hardhat -g
```

+ 使用hardhat命令初始化一个项目，跟着提示走就行，这里我取名叫【kunvote-hardhat】，代码地址 https://github.com/lvyangxu/kunvote-hardhat

```shell
npx hardhat init
```

+ hardhat常用命令详解
| 命令 | 作用 |
| -- | -- |
| hardhat test | 测试合约，会跑test目录下的测试用例，测试合约无需启动本地节点和部署合约 |
| hardhat node | 启动本地节点，模拟一个测试链，启动会生成20个测试账号，并在控制台打印 |
| hardhat compile | 编译合约，它会在artifacts\contracts目录下生成json文件，里面有ABI接口的信息 |
| hardhat ignition deploy ./ignition/modules/xxxx.ts --network localhost | 部署合约到本地节点，需要先启动本地节点，注意修改合约名称，部署成功后会打印合约地址 |

#### 初始化前端
+ 使用vue脚手架新建一个vue项目，这里我取名叫【kunvote-page】，代码地址 https://github.com/lvyangxu/kunvote-page
+ 安装依赖库vuetify和web3
+ 初始化vuetify，按文档进行即可，参考src/main.ts和vite.config.ts里面的代码
+ 合约初始化关键代码如下

```ts
import Web3 from 'web3'
const apiUrl = 'http://127.0.0.1:8545/'
const httpProvider = new Web3.providers.HttpProvider(apiUrl)
const web3 = new Web3(httpProvider)
const contractAddress = 'xxxxx'
const contractAbi = [ xxxxx ]
const contractInstance = new web3.eth.Contract(contractAbi, contractAddress)
contractInstance.contractDataInputFill = 'data'

// apiUrl指的区块链的地址，这里的值是hardhat启动的本地节点地址
// contractAddress指的是合约地址，hardhat部署合约后命令行会有打印
// contractAbi指的是合约的ABI接口，hardhat编译合约后会生成，目录为 artifacts\contracts\xxxx.sol\xxxx.json，里面的abi字段就是它的值

```

+ 合约API调用关键代码如下

```ts

await contractInstance.methods.xxxx(xxx).send({ from: xxx })

const xxxx = await contractInstance.methods
      .xxx()
      .call({ from: xxx })

// methods.xxx对于的是合约的xxx方法，from参数当前用户的账号，hardhat部署本地节点后会生成20个测试账号，拿来用就行了（注意是Account，而不是Private Key）
// 返回的值就是合约方法的返回值，如果是struct类型，js里面会转为一个json，key从0开始，可以参考src\components\TheWelcome.vue里面的代码
// send和call的区别为，send会创建事务并改变区块链上的数据，而call调用的是pure和view函数，不会创建事务

```

#### 开始实战
+ 技术准备就绪后，就可以开始写业务代码了。这个学习项目我做了一个最简单的投票需求，每个账号都可以投一次。大家都可以看到每个选项的得票数
+ 新建一个合约，路径为 contracts\KunVote.sol，内容参考示例工程
+ 编写合约的部署代码，在ignition\modules目录下面，照着脚手架创建的Lock.ts拷贝一个就行
+ 编写合约的测试用例，在test目录下面，照着脚手架创建的Lock.ts拷贝一个就行
+ 依次执行测试、编译、启动本地节点、部署
+ 切换到前端工程，拿到hardhat生成的合约地址、ABI接口，使用web3js库就可以进行调用了
+ 项目动图如下

<img src="images/web3demo.gif" style="width:50%">