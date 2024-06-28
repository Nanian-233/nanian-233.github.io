---
title: HgameCTF 2024
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/03/19/_20240308214158.jpg'
abbrlink: HgameCTF2024
date: 2024-03-08 21:48:00
---

## MISC

### **SignIn**

**PS**

**ctrl+T 出现调整框**
**按住 shift 然后上下拉窄，左右拉宽**

![](https://s2.loli.net/2024/03/08/1xilGPz2Mhs8J4c.png)

**hgame{WOW_GREAT_YOU_SEE_IT_WONDERFUL}**

### **simple_attack**

明文攻击
把给的图片用 bandizip 压缩成 zip 文件后作为明文文件
在 ARCHPR 中解密

![](https://s2.loli.net/2024/03/08/QZMgWYaCx3UXp4c.png)

解密后打开 txt 文件

![](https://s2.loli.net/2024/03/08/dX6qhgmnKaWHs5I.png)

[https://www.cnblogs.com/ECJTUACM-873284962/p/9245474.html](https://www.cnblogs.com/ECJTUACM-873284962/p/9245474.html)

得到的字符串直接放在 url 处就可以得到 flag 图片了

![](https://s2.loli.net/2024/03/08/igjodORFKz53JSw.png)

**hgame{s1mple_attack_for_zip}**

### **希儿希儿希尔**

![](https://s2.loli.net/2024/03/08/2l9vMiuKsT5wYe1.png)

不用修复图片

直接拉到 010 底部看到密文

![](https://s2.loli.net/2024/03/08/3ApHNESgnRGZc51.png)

结合题目标题可知是希尔解密

密钥使用 zsteg 查信道得到

![](https://s2.loli.net/2024/03/08/6VznFHO7JTKSqZ1.png)

密钥是 8 7 3 8

[http://www.metools.info/code/hillcipher243.html](http://www.metools.info/code/hillcipher243.html)

![](https://s2.loli.net/2024/03/08/okm2rdZixFRGq9X.png)

得到 flag
**hgame{DISAPPEARINTHESEAOFBUTTERFLY}**

### **来自星尘的问候**

![](https://s2.loli.net/2024/03/08/EinY8ygt56xeTZL.png)

stegseek 把题目的 png 跑了下

密码是 123456，解得新的一张 png

![](https://s2.loli.net/2024/03/08/oM8mkAQ4qIBtO5u.png)

星尘文字 [https://my1l.github.io/Ctrl/CtrlAstr.html](https://my1l.github.io/Ctrl/CtrlAstr.html)

> 看的眼睛都瞎了

**hgame{welc0me!}**

### **ek1ng_want_girlfriend**

流量签到，一打开就看到一个 jpg 的流量，dump 下来就是 flag

![](https://s2.loli.net/2024/03/08/vkoidnSt84qebZP.png)

### **ezWord**

拿到 word 文档，转 zip 后缀，子目录里看到两张一样的图和一个加密的 zip

![](https://s2.loli.net/2024/03/08/OgoEwkLanYQ4H51.png)

python3 盲水印解得 zip 的解压密码 T1hi3sI4sKey

![](https://s2.loli.net/2024/03/08/y2Ve1UTlnWH6qJC.png)

解压后得到一篇文章，结合提示（解密两次）

第一个是 spam

![](https://s2.loli.net/2024/03/08/SzpC4qUNc9s7vWM.png)

第二个需要先把这些汉字转 unicode 编码
[https://www.haomeili.net/HanZi/ZiFuBianMaZhuanHuan](https://www.haomeili.net/HanZi/ZiFuBianMaZhuanHuan)

![](https://s2.loli.net/2024/03/08/zAnND6pkMCdurfm.png)

去掉后缀 7C，通过观察以及 flag 头是 hgame 这一信息，将这一串 16 进制的字符向前移位 9 位

![](https://s2.loli.net/2024/03/08/mQ8iw364Dnp7JSt.png)

查 ascii 表发现 68 就是 h，67 就是 g，对照翻译即可

![](https://s2.loli.net/2024/03/08/h3E9QNqbvFVymju.png)

### 龙之舞

AU 打开音频在频谱图处看到 key

![](https://s2.loli.net/2024/03/08/1udN3BrhjqlS2ov.png)

水平翻转 + 旋转 180° 得到正确的 key

![](https://s2.loli.net/2024/03/08/AFp7bc4QIPxhZN5.png)

根据文件名 deepsound_of_dragon_dance.wav

将音频文件放入 deepsounde，使用上面的 key 得到一张 gif 图

![](https://s2.loli.net/2024/03/08/u7nIko4rASg9CeL.png)

逐帧观察得到四张二维码角，使用 ps 拼接在一起

![](https://s2.loli.net/2024/03/08/Ng53ZYFuUQzdRkO.png)

扫不了码，对应题目描述

![](https://s2.loli.net/2024/03/08/DVjA3I5JGhxXfPL.png)

使用 https://merri.cx/qrazybox/

![](https://s2.loli.net/2024/03/08/2M9BegazUC3RXdm.png)

![](https://s2.loli.net/2024/03/08/cUkKbGCTMwXAOER.png)

将 editor 切换为 decode 模式，点击 decode 得到 flag

![](https://s2.loli.net/2024/03/08/iq5Sacw87Fuvoth.png)

## WEB

### **ezHTTP**

**User-Agent: Mozilla/5.0 (Vidar; VidarOS x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36 Edg/121.0.0.0**

**x-real-ip: 127.0.0.1**

**referer: vidar.club**

![](https://s2.loli.net/2024/03/08/DqHN7MX8AKVsTRb.png)

### **Bypass it**

![](https://s2.loli.net/2024/03/08/ODnZl9dBXw1ythq.png)

注册不给，关掉 js 后发现可以注册了

随便弄用户名和密码后注册，再打开 js 显示注册成功，登录即可得到 flag

### Select **Courses**

仿真现实选课系统，时不时会出现空位，编写脚本实现当出现空位时选课即可

官方脚本：

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from time import sleep

driver = webdriver.Chrome()
driver.get("http://106.14.57.14:32274")
sleep(3)

courses_list = []

for i in range(1, 6):
    course = {
        'panel': f'//*[@id="selector-container"]/section[{i}]/div[1]',
        'status': f'//*[@id="selectorcontainer"]/section[{i}]/div[2]/table/tbody/tr/td[5]',
        'submit': f'//*[@id="selectorcontainer"]/section[{i}]/div[2]/table/tbody/tr/td[6]/button'
    }
    courses_list.append(course)

print(courses_list)

while courses_list:
    driver.refresh() 
    sleep(2)
    for course in courses_list:
        panel = driver.find_element(By.XPATH, course['panel'])
        panel.click() 
        status_element = driver.find_element(By.XPATH, course['status'])
        status_text = status_element.text 
        print(status_text)
        if status_text != "已满":
            submit_button = driver.find_element(By.XPATH, course['submit'])
            submit_button.click()
            WebDriverWait(driver, 5).until(EC.alert_is_present())
            alert = driver.switch_to.alert
        
            alert.accept()
            courses_list.remove(course)
            break
sleep(10) 
driver.quit()
```

![](https://s2.loli.net/2024/03/08/bJRclGTQpzXeK3v.png)

### **What the cow say?**

一打开是一个提交请求的框

随机发一些内容看看

![](https://s2.loli.net/2024/03/08/fiKduIHU8GWglZS.png)

经过多次尝试后

猜测后台是 eval(@_POST['user_input']) 并且带有 waf

分号被过滤，还过滤了许多关键词字

尝试使用 echo 配合反引号的命令执行，发现 echo 也被过滤，利用正则匹配[]来绕过

![](https://s2.loli.net/2024/03/08/acHDi9Mh4RbQG2T.png)

执行成功，继续 e[b-d]ho `tac /f*`

![](https://s2.loli.net/2024/03/08/DJvKUSp1VnO4ig7.png)

发现是文件夹，继续向下 ls 看看

![](https://s2.loli.net/2024/03/08/glPfev9nRd1ujzJ.png)

再 tac 一次得到 flag

![](https://s2.loli.net/2024/03/08/xP1frgH8C7GNKZX.png)
