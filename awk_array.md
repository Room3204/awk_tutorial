# 5个例子讲解Awk数组

&ensp;&ensp;&ensp;&ensp;Awk编程语言支持数组。数组是变量的一种扩展，数组可以保存多个值，和变量一样，数组也有名字，在一些编程语言中，数组必须先声明，这样内存才会给它分配空间，并且数组的下标必须是整数，就像array[1]、array[2]等等。

## Awk关联数组

&ensp;&ensp;&ensp;&ensp;Awk只支持关联数组，关联数组除了用字符串而不是数字作为下标外和传统的数组一样，当使用关联数组时，你可以用数字字符串作为下标来模仿传统的数组。

### 语法：

`arrayname[string]=value`

在上面的Awk语法中：
* arrayname是数组的名字
* string是数组的下标
* alue是数组中任何元素的值

## 访问数组元素

&ensp;&ensp;&ensp;&ensp;如果你想访问数组中的值，可以通过它的下标访问，就像arrayname[index]，这样会返回给你数组中那个下标对应的值。如果你想访问数组中所有的元素，可以使用循环访问数组的所有的下标，就像下面：

### 语法：

```
for (var in arrayname)
actions
```
在上面的Awk语法中：
* var是任何变量的名字
* in是一个关键字
* arrayname是数组的名字
* actions是要执行的语句列表。如果你想执行多条语句，必须放在大括号中。


这个循环对每一个数组中的元素执行acctions列表中的操作。

## 删除数组元素

&ensp;&ensp;&ensp;&ensp;如果你想删除数组中特定下标的元素，使用Awk删除语句。一旦删除了Awk数组中的元素，就不能再恢复。

### 语法：

`delete arrayname[index]`

&ensp;&ensp;&ensp;&ensp;下面的循环语句从数组中删除所有的元素。没有能删除数组中所有元素的单条语句。你不得不通过循环和使用Awk的删除语句删除每个元素。

### 语法：

```
for (var in arrayname)
delete arrayname[var]
```

## 实际的Awk数组例子

&ensp;&ensp;&ensp;&ensp;所有的例子都是用这个Iplogs.txt文件，这个文件包括了网关服务器所需要的ip地址列表，文件中的数据符合下面的格式：

`[date] [time] [ip-address] [number-of-websites-accessed]`

```
$ cat Iplogs.txt
180607 093423	123.12.23.122 133
180607 121234	125.25.45.221 153
190607 084849   202.178.23.4 44
190607 084859   164.78.22.64 12
200607 012312	202.188.3.2 13
210607 084849   202.178.23.4 34
210607 121435	202.178.23.4 32
210607 132423	202.188.3.2 167
```

### 例1 列出所有的IP地址和它被请求的次数

```
$ awk '{
> Ip[$3]++;
> }
> END{
> for (var in Ip)
> print var, "access", Ip[var]," times"
> }
> ' Iplogs.txt
125.25.45.221 access 1  times
123.12.23.122 access 1  times
164.78.22.64 access 1  times
202.188.3.2 access 2  times
202.178.23.4 access 3  times
```

在上面的脚本中：
* 第三个域($3)是一个IP地址，用来作为Ip数组的下标
* 对于每一行，会增加对应IP地址下标的值
* 最后在END部分，所有的下标就是唯一的IP地址，它对应的值就是请求的次数

### 例2 列出所有的IP地址并且计算出它访问了多少网站

&ensp;&ensp;&ensp;&ensp;Iplogs.txt的最后一个域是每个IP地址访问的网站个数，下面的脚本生成IP地址的列表和它请求的网关次数以及它访问的网站总数。

```
$cat ex2.awk
BEGIN {
print "IP Address\tAccess Count\tNumber of sites";
}
{
Ip[$3]++;
count[$3]+=$NF;
}
END{
for (var in Ip)
	print var,"\t",Ip[var],"\t\t",count[var];
}

$ awk -f ex2.awk Iplogs.txt
IP Address	Access Count	Number of sites
125.25.45.221 	 1 		 153
123.12.23.122 	 1 		 133
164.78.22.64 	 1 		 12
202.188.3.2 	 2 		 180
202.178.23.4 	 3 		 110
```

在上面的例子中：
* 它有两个数组，两个数组的下标相同，都是IP地址（第三个域）
* 第一个IP数组有唯一的IP地址和它出现的次数，第二个count数组使用IP地址作为下标，并且是最后一个域，因此无论什么时候出现IP地址，最后一个域都会增加
* 在END部分，检查所有的IP地址并且打印出IP地址和IP数组中的访问次数，以及count数组中访问网站的次数

### 例3 查找最大的访问日期

```
$ cat ex3.awk
{
date[$1]++;
}
END{
for (count in date)
{
	if ( max < date[count] ) {
		max = date[count];
		maxdate = count;
	}

}
print "Maximum access is on", maxdate;
}

$ awk -f ex3.awk Iplogs.txt
Maximum access is on 210607
```
	
在这个例子中：
* 'date'数组将日期作为下标，出现次数作为值
* max是一个计数变量用来找到拥有最大值的日期
* maxdate是拥有最大值日期的变量

### 例4 翻转文件中行的顺序

```
$ awk '{ a[i++] = $0 } END { for (j=i-1; j>=0;) print a[j--] }' Iplogs.txt
210607 132423	202.188.3.2 167
210607 121435	202.178.23.4 32
210607 084849   202.178.23.4 34
200607 012312	202.188.3.2 13
190607 084859   164.78.22.64 12
190607 084849   202.178.23.4 44
180607 121234	125.25.45.221 153
180607 093423	123.12.23.122 133
```
	
在这个例子中：
* 开始时记录所有的行到数组a
* 当程序处理完所有的行后，Awk执行END{}块
* END块循环数组a的所有元素并且以反序打印出记录的行

### 例5 用Awk删除重复和不相邻的行

```
$ cat > temp
foo
bar
foo
baz
bar

$ awk '!($0 in array) { array[$0]; print }' temp
foo
bar
baz
```
	
在这个例子中：
* Awk从temp文件中读取所有行，使用'in'运算符检查当前行是否在数组a中存在
* 如果不存在，它会储存并打印当前行

注：翻译自[原文连接](http://www.thegeekstuff.com/2010/03/awk-arrays-explained-with-5-practical-examples/)