---
title: "[Ethereum] 以太坊系列（一）之以太坊介绍"
catalog: true
date: 2018-8-11 18:26:24
type: "tags"
subtitle:
header-img: "http://ozilwgpje.bkt.clouddn.com/scenery/building.jpg?imageslim"
tags:
- Blockchain
- Ethereum
catagories:
- Blockchain
- Ethereum
---

## 1. 以太坊的介绍

以太坊是一个开放的`区块链平台`，允许任何人在平台中建立和使用通过区块链技术运行的去中心化应用，同比特币一样，以太坊由全球范围的很多人共同创建，不受任何个人控制。

## 2. 下一代区块链

区块链技术是比特币的底层技术。在比特币中，分布式数据库被设想为一个`账户余额表（总账）`，交易通过比特币的转移来实现个体之间无需信任基础的金融活动。以太坊试图实现一个总体上完全无需信任基础的智能合约平台。

## 3. 以太坊虚拟机

以太坊是`可编程`的区块链，不会给用户一系列预先设置好的操作，允许用户自定义复杂的操作。以太坊狭义上是指一系列定义去中心化应用平台的协议，核心是`以太坊虚拟机（EVM）`，其可以执行任意复杂算法的编码。以太坊是`图灵完备`的。

以太坊也有一个点对点的网络协议，以太坊区块链数据库由各网络节点来维护和更新，每个节点运行以太坊虚拟机并执行相同的指令，保证区块链的一致性。

## 4. 以太坊工作原理

比特币可以看作是关于交易的列表，而以太坊的基础单元是`账户`。以太坊跟踪每个账户的状态，以太坊上所有状态转换都是账户之间价值和信息的转换。

账户分为两类：

- 外部账户（EOA）：由私人密钥控制
- 合约账户：由合约代码控制，只能由外部账户“激活”。

`智能合约`：指合约账户中的编码，即交易被发送给该账户时所运行的程序。用户可以通过在区块链中部署编码来创建新的合约。

只有当外部账户发出指令时，合约账户才会执行相应的操作，是为了保证正确执行所有操作。

和比特币一样，以太坊用户需要向网络支付少量的交易费用，通过`以太币`的形式支付。这样避免受到类似DDos的攻击。

交易费用由节点收集，节点使网络生效。“矿工”就是以太坊网络中收集、传播、确认和执行交易的节点。矿工将交易打包，打包的数据成为区块，打包的过程就是将以太坊中`账户的状态`更新的过程。矿工们相互竞争挖矿，成功挖矿的可以得到以太币的奖励。



参考：

- http://www.ethdocs.org/en/latest/introduction/what-is-ethereum.html