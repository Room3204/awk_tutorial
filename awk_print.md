# 7个Awk打印的例子

在这片文章中，将用7个打印的例子来看一下Awk基本的工作方式。

Awk是一种处理结构化数据和产生格式化报告的编程语言，Awk代表的是它的作者——**A**ho、**W**einberger和**K**ernighan。

### Awk的一些关键特点：

1. Awk把文件看作记录和域
2. 和通常的编程语言一样，Awk也有变量、条件和循环
3. Awk拥有算数运算符和字符串运算符
4. Awk可以产生格式化输出

Awk从文件或标准输入读取，输出到标准输出。Awk不会处理空白文件。
```
语法：
awk '/search pattern1/ {actions}
	/search pattern2/ {actions}' file
```

在上面的Awk语法中：
1. `search pattern`是正则表达式
2. `actions`语句将会被执行
3. Awk中可以有多个`search pattern`和`actions`
4. `file`是输入文件
5. 将程序放在单引号中是为了避免shell的影响

### Awk工作方式

1. Awk一次读取输入文件中的一行
2. 对于每一行，他会按所给正则表达式的顺序进行匹配，如果匹配成功，就会执行对应的操作
3. 如果没有正则表示式匹配成功，就不会有任何操作执行
4. 在上面的语法中，`search pattern`和`actions`是可选的，但二者必须要有其一
5. 如果没有`search pattern`，Awk就会对每一行执行`actions`操作
6. 如果没有`actions`，Awk会默认打印出`search pattern`匹配成功的所有行
7. 如果是空白的大括号，Awk不会执行默认的打印操作
8. 大括号中每一条`actions`语句之间应该用分号隔开

后面的例子将以下面的`employee.txt`作为输入文件：
```
$cat employee.txt
100  Thomas  Manager    Sales       $5,000
200  Jason   Developer  Technology  $5,500
300  Sanjay  Sysadmin   Technology  $7,000
400  Nisha   Manager    Marketing   $9,500
500  Randy   DBA        Technology  $6,000
```

### 例1 Awk的默认操作

Awk默认会打印文件中的每一行
```
$ awk '{print;}' employee.txt
100  Thomas  Manager    Sales       $5,000
200  Jason   Developer  Technology  $5,500
300  Sanjay  Sysadmin   Technology  $7,000
400  Nisha   Manager    Marketing   $9,500
500  Randy   DBA        Technology  $6,000
```

在上面的例子中，没有`search pattern`，因此Awk会对所有行进行`actions`操作，这里Awk会默认地原封不动地打印出整行，因此打印出了文件中的所有行，`actions`必须被包含在大括号中。

### 例2 打印匹配成功的行
```
$ awk '/Thomas/
> /Nisha/' employee.txt
100  Thomas  Manager    Sales       $5,000
400  Nisha   Manager    Marketing   $9,500
```

在上面的例子中，Awk打印出来了所有匹配到`Thomas`和`Nisha`的行。这里有两个`pattern`，Awk可以接受任意数量的`pattern`，但是每个`pattern`和对应的`actions`必须用换行符隔开。

### 例3 打印特定的域

Awk有大量的内置变量，对于每条记录，也就是每一行，它会默认以空格符分割并保存到`$n`变量中。假如一行有4个单词，那么就会被保存到`$1`、`$2`、`$3`和`$4`中，`$0`代表整行，`NF`是一个代表一条记录中域的总数的内置变量。
```
$ awk '{print $2,$5;}' employee.txt
Thomas $5,000
Jason $5,500
Sanjay $7,000
Nisha $9,500
Randy $6,000

$ awk '{print $2,$NF;}' employee.txt
Thomas $5,000
Jason $5,500
Sanjay $7,000
Nisha $9,500
Randy $6,000
```

在上面的例子中，`$2`和`$5`分别表示名字和薪水，也可以用`$NF`获得薪水，因为`$NF`表示最后一个域，在打印语句中，逗号是一个连接符。

### 例4 初始化和最后的操作

Awk有两个重要的模式——`BEGIN`和`END`
```
语法：
BEGIN { Actions}
{ACTION} # Action for everyline in a file
END { Actions }

# is for comments in Awk
```

在`BEGIN`块中的`actions`将会在读取文件之前执行；`END`块会在完成对所有行的读取和处理后执行。
```
$ awk 'BEGIN {print "Name\tDesignation\tDepartment\tSalary";}
> {print $2,"\t",$3,"\t",$4,"\t",$NF;}
> END{print "Report Generated\n--------------";
> }' employee.txt
Name	Designation	Department	Salary
Thomas 	 Manager 	 Sales 	         $5,000
Jason 	 Developer 	 Technology 	 $5,500
Sanjay 	 Sysadmin 	 Technology 	 $7,000
Nisha 	 Manager 	 Marketing 	 $9,500
Randy 	 DBA 	 	 Technology 	 $6,000
Report Generated
--------------
```

在上面的例子中，它会打印标题和结尾

### 例5 找出`ID`大于200的员工信息
```
$ awk '$1 >200' employee.txt
300  Sanjay  Sysadmin   Technology  $7,000
400  Nisha   Manager    Marketing   $9,500
500  Randy   DBA        Technology  $6,000
```

在上面的例子中，第一个域`($1)`是员工`ID`，如果`$1`大于200，就会默认打印出整行。

### 例6 打印出在技术部门工作的员工信息

部门名字是第四个域(`$4`)，因此需要检查`$4`是否匹配字符串`"Technology"`，如果匹配就打印出这行。
```
$ awk '$4 ~/Technology/' employee.txt
200  Jason   Developer  Technology  $5,500
300  Sanjay  Sysadmin   Technology  $7,000
500  Randy   DBA        Technology  $6,000
```

运算符`~`表示和正则表达式进行比较，如果匹配就会执行默认的操作，也就是打印出整行。

### 例7 打印出在技术部门工作的员工人数

在下面的例子中，检查部门是否是技术部门，如果是，在`BEGIN`块初始化为0的`count`变量就会自加一
```
$ awk 'BEGIN { count=0;}
$4 ~ /Technology/ { count++; }
END { print "Number of employees in Technology Dept =",count;}' employee.txt
Number of employees in Tehcnology Dept = 3
```

最后打印出记录着在技术部门工作的员工人数的`count`变量的值。

注：文章翻译自[原文链接](http://www.thegeekstuff.com/2010/01/awk-introduction-tutorial-7-awk-print-examples/)