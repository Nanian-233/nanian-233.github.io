---
title: 记一个ctf区块链的签到题
description: shctf 区块链签到 wp
tags:
  - CTF
  - 区块链
categories:
  - write up
cover: >-
  https://tuchuang.voooe.cn/images/2024/03/19/e0c61957014643e4afa7ebfc24a5129b.jpg
abbrlink: ca4bb937
date: 2024-03-08 09:09:00
---
**来自 SHCTF 2023 的区块链签到题**

**[WEEK2]blockchain signin**

>如果你是第一次做区块链，请先做好以下准备

## 准备部分：

![](https://s2.loli.net/2024/03/08/SV5Aeb7mYZGNBh1.png)

> [记一个 Blockchain 签到题目](https://0314valen.github.io/article/2023-01-07-%E8%AE%B0%E4%B8%80%E4%B8%AABlockchain%E7%AD%BE%E5%88%B0%E9%A2%98%E7%9B%AE/#Remix%E4%BA%A4%E4%BA%92)

> 具体参考本文，这里只做操作演示

**用 metamask 开一个账户测试水龙头**

RPC 填题目给的，链 ID 先随便填然后会报错给正确的再填，其余随意填

![](https://s2.loli.net/2024/03/08/4891TM6iwySrELF.png)

把账户地址给水龙头可以拿到测试币（下图所示）

![](https://s2.loli.net/2024/03/08/DNsy1TxkGWZgoSM.png)

**浏览器直接连接水龙头**

![](https://s2.loli.net/2024/03/08/H5lqiydx1EbfKPe.png)

[https://remix.ethereum.org/](https://remix.ethereum.org/)

Remix 在线地址，我这里和 metamask 一起放在 Chrome 上

**Remix 和 metamask 绑定**

![](https://s2.loli.net/2024/03/08/7hiBjnJDKVu1cYt.png)

**第四栏选择 Injected Provider - MetaMask,网页会自动弹出连接**

![](https://s2.loli.net/2024/03/08/W63Kwt1C4XxlViT.png)

**连接即可**

## 操作部分：
nc连地址，选1
得到：
token：v4.local.swG1Vyhm-j-8tm30R4uf2wWcEWoPl1bHhLwi-OUS6qeoNMmXxZm5c8XFTcr2ElZXtCD18iLQBeGD-XmOsQq98cLOYdTsD3WwcwxMkjKLgIDCf6TIBv2ltRbBpJgZAmzgvf_9-yGLyGHAGtmbpnfNeSnBSTjf3H5B_pcWIr3olxS3yQ.R3JlZXRlcg

账户：0xD1c924fc5D72b9c6346921f37E596cE2cB859404

![](https://s2.loli.net/2024/03/08/TMKCa6sh8cjP5X3.png)
账户给水龙头拿个测试币

![](https://s2.loli.net/2024/03/08/YPj5QGWRxc3S1qA.png)
再次nc，这次选择2

![](https://s2.loli.net/2024/03/08/uZ7CDp8TseK6RLi.png)
拿到：
contract address: 0x40C6BC645879BF27b6Fb1F5EC3503Ebd935bD8FF

transaction hash: 0xfc54f73720aa6b348495a17cfff45db2794873ab8362e06f43023296fc456f39
<br>

然后再选4，拿到题目源码：

![](https://s2.loli.net/2024/03/08/Kei4qZ6tmuzE7rS.png)
remix，在default_workspace 里创建一个新文件

![](https://s2.loli.net/2024/03/08/VWTje8KXCLEqmF6.png)
把源码放进去并作细微修改（适应版本）

![](https://s2.loli.net/2024/03/08/HwVUzMA4lxEK81o.png)
调到第三栏选好对应版本 compile一下

![](https://s2.loli.net/2024/03/08/BHWoI5wnMN3XgxA.png)
把contract address放到地址那里，然后点下at Address 按钮，打开下面的窗口

![](https://s2.loli.net/2024/03/08/TvkPCfjUne1poOc.png)
根据代码提示，我们发起一个问候，内容为"welC0meToSHCTF2023"
在setGreeting里添加并运行

![](https://s2.loli.net/2024/03/08/zKtpkNbY5CFLgHO.png)
刚才得到的测试币在这里派上用场，用于缴费gas
![](https://s2.loli.net/2024/03/08/IrQHRPFtenwWTGL.png)
然后回到nc环境，选3就可以得到flag了
![](https://s2.loli.net/2024/03/08/HQzLPjZAiqVsexp.png)

**SHCTF{1T_Ls_a_ESS3N7Iais_8IOCkCh4IN_ChaLLEn6E_Are_you_Ready_7O_DIve_In}**
