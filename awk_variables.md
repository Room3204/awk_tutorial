# 3个例子理解Awk变量

&ensp;&ensp;&ensp;&ensp;和其他编程语言一样，Awk也有用户自定义的变量和内置变量，在这篇文章中，我们来看一下如何使用Awk变量。

- Awk变量应该以字母开头，紧跟着的可以包含数字或者下划线
- 关键字不能用作变量
- Awk不想其他编程语言那样支持变量声明
- 最好在BEGIN部分初始化变量，这样就只需要在开始时执行一次初始化操作
- 在Awk中没有数据类型，一个变量被当作数字还是字符串，取决于它被使用的上下文

现在，通过几个例子学习如何使用用户自定义的Awk变量。

### 例1 书单

在这个例子中，输入文件`bookdetails.tst`包含下面的域:
```
item number, Book name, Quantity, Rate per book
```

具体内容:
```
$ cat bookdetails.txt
1 Linux-programming 2 450
2 Advanced-Linux 3 300
3 Computer-Networks 4 400
4 OOAD&UML 3 450
5 Java2 5 200
```

下面的Awk脚本读取并处理上面的`bookdetails.txt`文件，然后产生输出———每本书的收入和所有书的收入。

我们已经知道Awk可以从命令行中读取命令，Awk也可以用`-f`选项从文件中读取命令。

#### 语法：
```
$ awk -f script-filename inputfilename
```

这个时计算书单的Awk脚本：
```
$ cat book-calculation.awk
BEGIN {
	total=0;
}
{
	itemno=$1;
	book=$2;
	bookamount=$3*$4;
	total=total+bookamount;
	print itemno," ", book,"\t","$"bookamount;
}
END {
	print "Total Amount = $"total;
}
```

在上面的脚本中：

* 在BEGIN部分初始化了`total`变量，`itemno`、`total`、`book`、`bookamount`是自定义的变量
* 在ACTION部分，`Quantity`*`bookprice`会被储存在`bookamount`变量中，每个`bookamount`都会被加到`total`中
* 最后的END部分，`total`是总金额

现在执行`book-calculation.awk`脚本，输出每本书的收入以及在最后的总收入：
```
$ awk -f book-calculation.awk bookdetails.txt
1   Linux-programming 	 $900
2   Advanced-Linux 	 $900
3   Computer-Networks 	 $1600
4   OOAD&UML 	 $1350
5   Java2 	 $1000
Total Amount = $5750
```

### 例2 计算学生分数

在这个例子中，输入文件`sudent-marks.txt`包含：
```
Student name, Roll Number, Test1 score, Test2 score， Test3 score
```

具体内容：
```
$ cat student-marks.txt
Jones 2143 78 84 77
Gondrol 2321 56 58 45
RinRao 2122 38 37 65
Edwin 2537 78 67 45
Dayan 2415 30 47 20
```

下面的Awk脚本将产生每个学生的平均成绩以及Test1、Test2、Test3的平均成绩
```
$cat student.awk

BEGIN {
	test1=0;
	test2=0;
	test3=0;
	print "Name\tRollNo\t Average Score";

}
{
	total=$3+$4+$5;
	test1=test1+$3;
	test2=test2+$4;
	test3=test3+$5;
	print $1"\t"$2"\t",total/3;
}
END{
	print "Average of Test1="test1/NR;
	print "Average of Test2="test2/NR;
	print "Average of Test3="test3/NR;

}
```

在上面的Awk脚本中：
* 在BEGIN部分，所有的变量都被初始化为0，test1、test2、test3、total是用户自定义的变量
* 在ACTION部分$3、$4、$5分别是Test1、Test2、Test3的分数，total是每个学生3此测试的总成绩，变量test1、test2、test3是对应测试的总成绩
* 在END部分，按照记录的总条数将每次测试的总成绩分开得到平均成绩。NR是Awk的内置变量，表示输入记录的总数

### 例3 用HTML报告学生的信息

在上面的两个例子中，我们看到Awk变量把数字作为它的值。这个例子展示了Awk脚本产生每个学生的姓名和学号(Roll Number)的HTML报告。
```
$ cat string.awk
BEGIN{
title="AWK";
print "<html>\n<title>"title"</title><body bgcolor=\"#ffffff\">\n<table border=1><th  colspan=2 align=centre>Student Details</th>";

}
{
name=$1;
rollno=$2;
print "<tr><td>"name"</td><td>"rollno"</td></tr>";
   
}
END {
    print "</table></body>\n</html>";
}
```

用例2的`student-marks.txt`作为输入文件，将产生下面的结果：
```
$ awk -f string.awk student-marks.txt
<html>
<title>AWK</title><body bgcolor="#ffffff">
<table border=1><th  colspan=2 align=centre>Student Details</th>
<tr><td>Jones</td><td>2143</td></tr>
<tr><td>Gondrol</td><td>2321</td></tr>
<tr><td>RinRao</td><td>2122</td></tr>
<tr><td>Edwin</td><td>2537</td></tr>
<tr><td>Dayan</td><td>2415</td></tr>
</table></body>
</html>
```

我们可以保存上面的输出文件，它将产生下面的HTML表单。在上面的脚本中，`name`变量和`rollno`变量都是字符串变量，因为它们都用于了字符串。

<pre>
<table border="1">
<tbody>
<tr>
<th colspan="2" align="centre">Student Details</th>
</tr>
<tr>
<td>Jones</td>
<td>2143</td>
</tr>
<tr>
<td>Gondrol</td>
<td>2321</td>
</tr>
<tr>
<td>RinRao</td>
<td>2122</td>
</tr>
<tr>
<td>Edwin</td>
<td>2537</td>
</tr>
<tr>
<td>Dayan</td>
<td>2415</td>
</tr>
</tbody></table>
</pre>

注：翻译自[原文链接](http://www.thegeekstuff.com/2010/01/awk-tutorial-understand-awk-variables-with-3-practical-examples/)