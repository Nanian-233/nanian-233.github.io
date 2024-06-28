---
title: FSCTF 2023
description: web&misc wp
tags:
  - CTF
  - MISC
  - WEB
categories:
  - write up
cover: 'https://tuchuang.voooe.cn/images/2024/03/19/e088f040cc4a42d2866bf23364767098.webp'
abbrlink: FSCTF2023
date: 2024-03-06 19:42:00
---

## MISC

### Simple Encryption

伪加密 01 02 后面的 09 00 改为 00 00 即可

### 萧河

补 png 头 89 50 4E 47

### base 套

```python
import base64

def recursive_base64_decode(data):
    decoded_data = base64.b64decode(data.encode()).decode('utf-8', 'ignore')

    # 寻找特定字词，例如 '是'
    keyword = '是'
    if keyword in decoded_data:
        index = decoded_data.index(keyword)
        decoded_data = decoded_data[index + len(keyword):]

    if '}' in decoded_data:
        return decoded_data
    else:
        return recursive_base64_decode(decoded_data)

base64_encoded_data = "初始Base64数据"  # 替换成您的初始Base64编码数据

final_data = recursive_base64_decode(base64_encoded_data)
print("解密后的数据：", final_data)
```

### Ez-osint

[https://www.bilibili.com/video/BV13K4y1s7qQ/?spm_id_from=333.999.0.0&vd_source=f7e76ebc6ca15fbb8c8dc4909fad4f40](https://www.bilibili.com/video/BV13K4y1s7qQ/?spm_id_from=333.999.0.0&vd_source=f7e76ebc6ca15fbb8c8dc4909fad4f40)

### 为什么不换换思路捏

![](https://s2.loli.net/2024/03/07/m5DEFBVXLzMNr6s.png)

### 最终试炼 hhh

文件逆转，然后 zip 伪加密，是一个 pdf，判断是 pdf 隐写，然后用 wbStego4.3open 解密，得出 flag

## WEB

### 源码！启动!

点下网址栏再 f12 查看源码即可

### webshell 是啥捏

passthru()

![](https://s2.loli.net/2024/03/07/GkgYubjmprBeNdi.png)

### Hello,you

![](https://s2.loli.net/2024/03/07/oGCIzqhZjaXktEA.png)

### 细狗 2.0

![](https://s2.loli.net/2024/03/07/9imDCWykKFJvcAu.png)

### ez_php1

![](https://s2.loli.net/2024/03/07/fYoNqjBITOPAuWH.png)

```php
<?php
$KEY = "YES I love";
echo serialize($KEY);
?>
```

![](https://s2.loli.net/2024/03/07/XFQCLqfmce3B9TZ.png)

```php
<?php
class Clazz
{
    public $a;
    public $b;
}

$a=new Clazz();
$a ->b = &$a ->a;
echo serialize($a);

?>
```

![](https://s2.loli.net/2024/03/07/Zvcqt1hXWAdm3TU.png)

解密 base64 即可

### EZ_eval

传入一句话木马后直接cat flag

![](https://s2.loli.net/2024/03/07/o6cCJhpwdyQXKbv.png)

