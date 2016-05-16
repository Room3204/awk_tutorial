# 8个强大的Awk内置变量

Awk有一些强大的内置变量，在Awk中有两种类型的内置变量。

- 像域分割符和记录分割符那种值可变的变量
- 像域数量和记录数量那种用来处理和报告的变量

### `FS`例子：输入域分割符

Awk读取输入文件，并且默认以空白符分割文件的每一行，然后分别设置为变量$1,$2......Awk的`FS`变量用来为每条记录设置分割符。`FS`可以设置成任何单字符或者正则表达式，你可以使用下面的两种方式使用域分割符：

1. 使用`-F`命令行选项
2. 像设置其他变量那样设置`FS`

```
语法：
$ awk -F 'FS' 'commands' inputfilename
(or)
$ awk 'BEGIN{FS="FS";}'
```

- `FS`可以是任何你想用来作为域分割符的单字符或者正则表达式
- `FS`可以在任何时候改变，它的值会一直保留到被改变。如果你想改变域分割符，最好在读取行之前改变，这样改变就会影响读取的每一行。

这是一个`FS`的例子，读取以`':'`作为分割符的`/etc/passwd`文件：
```
$ cat etc_passwd.awk
BEGIN{
FS=":";
print "Name\tUserID\tGroupID\tHomeDirectory";}
{
	print $1"\t"$3"\t"$4"\t"$6;
}
END {
	print NR,"Records Processed";
}
```

```
$awk -f etc_passwd.awk /etc/passwd
Name    UserID  GroupID        HomeDirectory
gnats	41	41	/var/lib/gnats
libuuid	100	101	/var/lib/libuuid
syslog	101	102	/home/syslog
hplip	103	7	/var/run/hplip
avahi	105	111	/var/run/avahi-daemon
saned	110	116	/home/saned
pulse	111	117	/var/run/pulse
gdm	112	119	/var/lib/gdm
8 Records Processed
```

### `OF`例子：输出域分隔符

`OFS`是一个输出域分割符，默认是一个空格符，下面是一个例子：
```
$ awk -F':' '{print $3,$4;}' /etc/passwd
41 41
100 101
101 102
103 7
105 111
110 116
111 117
112 119
```

`print`语句中的`','`用`OFS`默认的空格符连接了两个参数。因此`OFS`将会被插入到输出中的两个域之间，看下面的例子：
```
$ awk -F':' 'BEGIN{OFS="=";} {print $3,$4;}' /etc/passwd
41=41
100=101
101=102
103=7
105=111
110=116
111=117
112=119
```

### `RS`例子：记录分割符

`RS`定义一行，Awk默认一行一行地读取输入文件。

按如下格式储存`student.txt`，每条记录以双换行符分割，每个域以换行符分割：
```
$cat student.txt
Jones
2143
78
84
77

Gondrol
2321
56
58
45

RinRao
2122
38
37
65

Edwin
2537
78
67
45

Dayan
2415
30
47
20
```

下面的Awk脚本打印出学生的名字和学号：
```
$cat student.awk
BEGIN {
	RS="\n\n";
	FS="\n";

}
{
	print $1,$2;
}

$ awk -f student.awk  student.txt
Jones 2143
Gondrol 2321
RinRao 2122
Edwin 2537
Dayan 2415
```

在上面的脚本中，读取每个学生的信息作为一条记录，因为Awk的`RS`已经被定义为了双换行符，并且每行作为一个记录，因为`FS`是一个换行符。

### `OFS`例子：输出记录分割符

`OFS`是输出记录分割符，输出中的每条记录将以`OFS`作为定界符打印，下面是`OFS`的一个例子：
```
$  awk 'BEGIN{ORS="=";} {print;}' student-marks
Jones 2143 78 84 77=Gondrol 2321 56 58 45=RinRao 2122 38 37 65=Edwin 2537 78 67 45=Dayan 2415 30 47 20=
```

在上面的例子中，`student-marks`中的每行都以`'='`作为定界符。

### `NR`例子：记录数量变量

`NR`返回记录总数或者已经处理的行数，在下面的例子中，`NR`储存行数，在`END`块中将返回文件的记录总数。
```
$ awk '{print "Processing Record - ",NR;}END {print NR, "Students Records are processed";}' student-marks
Processing Record -  1
Processing Record -  2
Processing Record -  3
Processing Record -  4
Processing Record -  5
5 Students Records are processed
```

### `NF`例子：一条记录中域的数量

`NF`返回一条记录中域的总数，`NF`变量在验证一条记录中是否所有的域都存在时非常有用。

看一下`student-marks`，有的学生的Test3分数不存在：
```
$cat student-marks
Jones 2143 78 84 77
Gondrol 2321 56 58 45
RinRao 2122 38 37
Edwin 2537 78 67 45
Dayan 2415 30 47
```

下面的脚本将会打印记录号(也就是行号啦)以及那条记录中域的数量，这样就很容易找出缺失Test3分数的记录：
```
$ awk '{print NR,"->",NF}' student-marks
1 -> 5
2 -> 5
3 -> 4
4 -> 5
5 -> 4
```

### `FILENAME`例子：当前输入文件的文件名

`FILENAME`返回正在读取的文件的文件名，Awk可以接受多个文件进行处理。
```
$ awk '{print FILENAME}' student-marks
student-marks
student-marks
student-marks
student-marks
student-marks
```

在上面的例子中，对于`student-marks`的每一条记录都会打印文件名。

### `FNR`例子：当前文件的记录数

当读取多个文件时，`NR`会返回所有文件的记录总数，`FNR`只会返回当前文件的记录数。
```
$ awk '{print FILENAME, FNR;}' student-marks bookdetails
student-marks 1
student-marks 2
student-marks 3
student-marks 4
student-marks 5
bookdetails 1
bookdetails 2
bookdetails 3
bookdetails 4
bookdetails 5
```

在上面的脚本中，如果使用的不是`FNR`，而是`NR`的话，对于`bookdetails`的每行将会返回6到10。

注：翻译自[原文链接](http://www.thegeekstuff.com/2010/01/8-powerful-awk-built-in-variables-fs-ofs-rs-ors-nr-nf-filename-fnr/)
