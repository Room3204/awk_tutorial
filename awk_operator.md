# 7个强大的运算符实例

&ensp;&ensp;&ensp;&ensp;和其他编程语言一样，Awk也有许多数字、字符串操作的运算符，在这片文章中，将会讨论所有的关键运算符。

在Awk中有两种运算符：

- 一元运算符——只接受一个运算对象
- 二元运算符——接受不止一个运算对象

### Awk的一元运算符

|运算符|描述|
|----|-----|
|+|正|
|-|负|
|++|自加|
|--|自减|

### Awk的二元运算符

Awk有许多不同种类的二元运算符，按照它们的用法分类：

#### 算数运算符

|运算符|描述|
|-|-|
|+|加|
|-|减|
|*|乘|
|/|除|
|%|取模|

#### 字符串运算符

|运算符|描述|
|-|-|
|（空格）|连接字符串|

#### 赋值运算符

|运算符|描述|
|-|-|
|=|赋值|
|+=|加后赋值|
|-=|减后赋值|
|*=|乘后赋值|
|/=|除后赋值|
|%=|取模后赋值|

#### 条件运算符

下面是一些在控制结构和循环语句中使用的运算符

|运算符|描述|
|-|-|
|>|大于|
|>=|大于等于|
|<|小于|
|<=|小于等于|
|==|等于|
|!=|不等于|
|&&|与|
| &#124; &#124;|或|

#### 正则表达式运算符

|运算符|描述|
|-|-|
|~|匹配|
|!~|不匹配|

### Awk运算符例子

以下面的`/etc/passwd`作为输入文件为例，看几个Awk运算符的例子
```
$ cat /etc/passwd
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
syslog:x:101:102::/home/syslog:/bin/false
hplip:x:103:7:HPLIP system user,,,:/var/run/hplip:/bin/false
saned:x:110:116::/home/saned:/bin/false
pulse:x:111:117:PulseAudio daemon,,,:/var/run/pulse:/bin/false
gdm:x:112:119:Gnome Display Manager:/var/lib/gdm:/bin/false
```

#### 计算文件中域的总数

在下面的脚本中，读取每一行，并且对于每一行中的每个域都用`+=`运算符使数量增加，同时保存在变量`total`中。一旦输入处理结束，`END`块就会执行，打印出域的总数。
```
$ awk -F ':' '{ total += NF }; END { print total }' /etc/passwd
49
```

统计使用`/bin/sh`shell的用户数量

在下面的脚本中，它匹配最后一个域中包含`/bin/sh`的所有行。正则表示式应该包含在`//`中，所以`/`必须要被转义。每当匹配成功时，变量`n`都会加一，最后在`END`块打印出`n`的值。

```
$ awk -F ':' '$NF ~ /\/bin\/sh/ { n++ }; END { print n }' /etc/passwd
2
```

#### 找出`USER ID`最大的用户的信息

在下面的脚本中，用变量`maxuid`追踪并保存最大的`$3`，并用`maxline`保存对应的行的内容，当循环结束后，打印出结果。
```
$ awk -F ':' '$3 > maxuid { maxuid=$3; maxline=$0 }; END { print maxuid,
maxline }' /etc/passwd
112 gdm:x:112:119:Gnome Display Manager:/var/lib/gdm:/bin/false
```

#### 打印偶数行

在下面的脚本中，读取每一行，并检查`NR % 2 == 0`，如果`NR`能被2整除，就会执行默认的打印操作。
```
$ awk 'NR % 2 == 0' /etc/passwd
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
hplip:x:103:7:HPLIP system user,,,:/var/run/hplip:/bin/false
pulse:x:111:117:PulseAudio daemon,,,:/var/run/pulse:/bin/false
```

#### 打印`USER ID`和`GROUP ID`相同的行

下面的脚本只会打印`$3(USER ID)`和`$4(GROUP ID)`相同的行。他会检查输入的所有行，如果匹配就会打印整行。
```
$awk -F ':' '$3==$4' passwd.txt
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
```

#### 打印处`USER ID`大于等于100并且使用`/bin/sh`shell的用户数据

在下面的Awk脚本中，有两个条件表达式，一个是`$3(UDER ID)`大于等于100，另一个最后一个域匹配`/bin/sh`，`&&`使得只有同时满足两个条件时才会打印。
```
$ awk -F ':' '$3>=100 && $NF ~ /\/bin\/sh/' passwd.txt
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
```

#### 打印在`/etc/passwd`中没有用户信息`($5)`的用户数据

在下面的脚本中，读取每一行，并检查`$5`是否为空，如果是空，打印处这一行。
```
$awk -F ':' '$5 == "" ' passwd.txt
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
syslog:x:101:102::/home/syslog:/bin/false
saned:x:110:116::/home/saned:/bin/false
```

注：最后三个例子中`passwd.txt`应该是前面的`/etc/passwd`文件，尊重原著，这里并没有修改，本文翻译自[原文链接](http://www.thegeekstuff.com/2010/02/unix-awk-operators/)