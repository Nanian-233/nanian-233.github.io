---
title: 中国科学技术大学第十届信息安全大赛
description: Hackergame 2023 wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/03/19/773dfeee96fd40ba946f0b7f62454522.jpg'
abbrlink: Hackergame2023
date: 2024-03-07 20:22:00
---
## Hackergame 启动


点一下提交看到 url 处多了参数

这个是测试你的音频准度的，直接给值 100 就行

![](https://s2.loli.net/2024/03/07/pkHvSFQYhUgIT3o.png)

## 更深更暗

F12 搜索 flag 即可

![](https://s2.loli.net/2024/03/07/HJes6wp2D4VTx18.png)

## 赛博井字棋

题目考查和 ai 下棋，下赢得 flag

先下一步棋后 bp 抓包

x=0

![](https://s2.loli.net/2024/03/07/jT7P2esWNRCnfu4.png)

拿回显给的 session 继续下棋就行了，不用管 ai 下哪

x=1

![](https://s2.loli.net/2024/03/07/c8jWAoCv7EuTyOQ.png)

x=2 练成一条线就 win

![](https://s2.loli.net/2024/03/07/kA4JQBDgyl9YmMc.png)

## 组委会模拟器


![](https://s2.loli.net/2024/03/07/uWF87Tg2yDMs3KU.png)

```python
import time
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import NoSuchElementException

# 启动浏览器，这里使用Chrome
driver = webdriver.Chrome()

# 打开网页，替换URL为你的网页地址
driver.get("http://202.38.93.111:10021/")

# 设置隐式等待时间为0秒
driver.implicitly_wait(0)

# 定义一个函数，用于监控新消息并执行点击操作
def monitor_and_click_hack(message):
    try:
        message_text = message.find_element(By.TAG_NAME, "span").text
        if "hack[" in message_text.lower():
            message.click()
    except Exception as e:
        print("处理消息时出错:", e)

# 在网页上监测新消息并执行点击操作
def main():
    checked_messages = set()
    message_count = 0
    pause_script = False

    while True:
        try:
            if pause_script:
                # 暂停脚本执行100秒
                time.sleep(100)
                pause_script = False

            wait = WebDriverWait(driver, 3)
            messages = wait.until(EC.presence_of_all_elements_located((By.CLASS_NAME, "fakeqq-message__bubble")))

            new_messages = [message for message in messages if message not in checked_messages]
            for message in new_messages:
                checked_messages.add(message)

            hack_messages = [message for message in new_messages if "hack[" in message.find_element(By.TAG_NAME, "span").text.lower()]
            for message in hack_messages:
                monitor_and_click_hack(message)

            message_count += len(hack_messages)

        except Exception as e:
            print("执行过程中出错:", e)

        # 等待1.5秒，然后再次检查新消息
        time.sleep(1.5)

if __name__ == '__main__':
    main()
```

最后弹窗手快截图就行，实在不行就录屏，反正代码我不敢再改了（都是 gpt 弄的，我是废物 QAQ）

如果没成功就再来几次，这个 sleep 的时间也不是最准的

![](https://s2.loli.net/2024/03/07/TQHDmxn9f7d86EO.png)

## 奶奶的睡前 flag 故事

google 设备截图泄露（同之前的 win11 截图漏洞）


[https://github.com/frankthetank-music/Acropalypse-Multi-Tool](https://github.com/frankthetank-music/Acropalypse-Multi-Tool)

![](https://s2.loli.net/2024/03/07/vUqjtD4THkS51em.png)

## 虫

sstv



音频直接给工具梭哈

[https://www.cnblogs.com/wutou/p/16154582.html](https://www.cnblogs.com/wutou/p/16154582.html)

![](https://s2.loli.net/2024/03/07/uY7MBzSEW5Kq1me.png)

![](https://s2.loli.net/2024/03/07/xSbkD1CfyGoh8pl.png)

## Git? Git!

git 版本回退



拿到文件夹（git 的本地库）

直接在目录下 git log 查看历史版本

![](https://s2.loli.net/2024/03/07/PrD5n6gZlxceEbK.png)

git reset --hard xxxxxxxxxxxxxxx 回退版本

但是这里都不是的，实际上在.git 的 logs 里的 HEAD 文件中可以看到

![](https://s2.loli.net/2024/03/07/PSusUBDnrXOlIdg.png)

这个才是修改内容，一开始 000 是没内容的估计，然后给了内容后变成了 15f

15f 又更新为 505 的，但这个时候 505 又退成了 15f，也就是说这里就符合题目的情景

![](https://s2.loli.net/2024/03/07/Qk4Bd93YyFzW26i.png)

所以猜测 505 版本有 flag，git reset --hard 505e1a3f446c23f31807a117e860f57cb5b5bb79

![](https://s2.loli.net/2024/03/07/eckGAa8ro3zVZ6i.png)

此时再回到最外面的目录，打开 README.md 搜索 flag 即可得到

![](https://s2.loli.net/2024/03/07/NDuiqxV8r2WmhBG.png)

## HTTP 集邮册


无状态码

![](https://s2.loli.net/2024/03/07/wNslhdvQiR3g72E.png)

200

![](https://s2.loli.net/2024/03/07/TmVsgqd7rB59nO1.png)

405

![](https://s2.loli.net/2024/03/07/DEdcaoN6q7glFPT.png)

404

![](https://s2.loli.net/2024/03/07/qTb7I1w6OJ4m9Rs.png)

400

![](https://s2.loli.net/2024/03/07/QmK7pzFAOn2NDCV.png)

505

![](https://s2.loli.net/2024/03/07/zbR43pnKXNTtkwj.png)

## 猫咪小测


![](https://s2.loli.net/2024/03/07/dnOQR2BgJeFlvWk.png)

[西区图书馆简介 | 中国科学技术大学图书馆](https://lib.ustc.edu.cn/%E6%9C%AC%E9%A6%86%E6%A6%82%E5%86%B5/%E5%9B%BE%E4%B9%A6%E9%A6%86%E6%A6%82%E5%86%B5%E5%85%B6%E4%BB%96%E6%96%87%E6%A1%A3/%E8%A5%BF%E5%8C%BA%E5%9B%BE%E4%B9%A6%E9%A6%86%E7%AE%80%E4%BB%8B/) 去官网看到位置

![](https://s2.loli.net/2024/03/07/EI5pS6LoONs4jyF.png)

![](https://s2.loli.net/2024/03/07/JygtbUhjFB6S51C.png)

![](https://s2.loli.net/2024/03/07/9PQozNRTHLk1bZl.png)

搜这个英文

![](https://s2.loli.net/2024/03/07/Um35LcdPXHTiZth.png)

![](https://s2.loli.net/2024/03/07/Sclj6YJCPWZzAUF.png)

看到 23

![](https://s2.loli.net/2024/03/07/hkGK6x7y32D1AEV.png)

直接问 gpt 就可以了

![](https://s2.loli.net/2024/03/07/uUxkcPFjbWqMIlQ.png)

![](https://s2.loli.net/2024/03/07/f95ans7bPoLvlFM.png)

蒙就完了

![](https://s2.loli.net/2024/03/07/lGUghNqAkmVTpKQ.png)

## 小型大语言模型星球



> 你需要让这个语言模型分别说出 `you are smart`，`accepted`，`hackergame` 和 `🐮`，以获得四个 flag

![](https://s2.loli.net/2024/03/07/od5XsHwMcaPpGU6.png)

第一个问 Am I smart？即可

## 低带宽星球

压缩图片并保证像素极


[在线图片压缩 - docsmall 在线图片压缩工具,在线图片压缩软件](https://docsmall.com/image-compress)

能压到 1kb 拿到 flag1
