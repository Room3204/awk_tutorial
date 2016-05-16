# 4个Awk的If语句实例

&ensp;&ensp;&ensp;&ensp;在这篇教程中，我们通过实例来看一下Awk的条件判断语句—if(和其他语言的基本一样，浏览一下就可以啦)。

&ensp;&ensp;&ensp;&ensp;Awk支持许多条件判断语句来控制程序执行的流程，大多数Awk的条件控制语句语法和C语言的很像。

&ensp;&ensp;&ensp;&ensp;通常，条件判断语句在程序执行操作前先检查条件。如果条件为真，操作就会执行，同样的，如果条件为假，操作也能执行。(反正就是和C语言的一样啦，原文直译就是这样的呀)

&ensp;&ensp;&ensp;&ensp;条件判断语句用关键字`'if'`开始，Awk支持两种不同的`if`语句。

- 简单的`if`语句
- `if-else`语句
- `if-else if`语句

### 简单的`if`语句

**单指令:** 简单的`if`语句用来检查条件，如果条件返回`真`，就会执行对应的操作
```
语法：
if (conditional-expression)
	action
```

- `if`是一个关键字
- `conditional-expression`是条件表达式，检查条件是否正确
- `action`是任何Awk的执行语句

**多指令:** 如果条件表达式返回`真`，操作就会执行。如果不止一条指令需要执行，那么这些指令应该放到花括号中，每条指令一行或者用分号分开，就像下面：
```
语法：
if (conditional-expression)
{
	action1;
	action2;
}
```

如果条件为真，花括号中的所有指令都会按顺序执行，当所有这些指令都执行完后，就会继续执行下面的语句。

### `if else`语句

在上面简单的`if`语句中，没有条件为假时执行的指令，在`if else`语句中，可以设置条件为假时要执行的指令，如果条件为真，指令1执行，如果条件为假，指令2执行。
```
语法：
if (conditional-expression)
	action1
else
	action2
```

Awk也有条件运算符，就是三元操作符—`？：`，它的功能就像是`if else`语句，如果条件判断为真，指令1执行，如果条件判断为假，指令2执行。
```
语法：
conditional-expression ? action1 : action2 ;
```

### `if else if`语句

```
语句：
if(conditional-expression1)
	action1;
else if(conditional-expression2)
	action2;
else if(conditional-expression3)
	action3;
	  .
	  .
else
	action n;
```

- 如果条件表达式1为真，指令1就会被执行
- 如果条件表达式1为假，就会检查条件表达式2，指令2就会被执行，就这样一直进行下去；如果没有条件表达式为真，`else`部分的指令就会执行

----
----

&ensp;&ensp;&ensp;&ensp;现在，我们创建一个学生分数的输入文件：
```
$cat student-marks
Jones 2143 78 84 77
Gondrol 2321 56 58 45
RinRao 2122 38 37
Edwin 2537 87 97 95
Dayan 2415 30 47
```

### `if`例子：检查所有的分数是否都存在
```
$ awk '{
if ($3 =="" || $4 == "" || $5 == "")
	print "Some score for the student",$1,"is missing";
}' student-marks
Some score for the student RinRao is missing
Some score for the student Dayan is missing
```

$3、$4、$5是学生的检测成绩，如果成绩为空，就会打印出消息。`||`运算符是为了当其中的任何一个成绩不存在时，就会打印消息。(就是`或`啦)

### `if else`例子：在学生每科分数的基础上产生是否通过的报告
```
$ awk '{
if ($3 >=35 && $4 >= 35 && $5 >= 35)
	print $0,"=>","Pass";
else
	print $0,"=>","Fail";
}' student-marks
Jones 2143 78 84 77 => Pass
Gondrol 2321 56 58 45 => Pass
RinRao 2122 38 37 => Fail
Edwin 2537 87 97 95 => Pass
Dayan 2415 30 47 => Fail
```

通过的条件是所有的测试分数都大于等于35，因此程序会检查所有的测试分数，如果大于等于35，就会打印出整行信息和`Pass`字符串，否则，任何一科的测试分数没有满足条件，就会打印出整行和`Fail`字符串。

### `if else if`例子：找出每个学生的平均成绩和等级
```
$ cat grade.awk
{
total=$3+$4+$5;
avg=total/3;
if ( avg >= 90 ) grade="A";
else if ( avg >= 80) grade ="B";
else if (avg >= 70) grade ="C";
else grade="D";
print $0,"=>",grade;
}
$ awk -f grade.awk student-marks
Jones 2143 78 84 77 => C
Gondrol 2321 56 58 45 => D
RinRao 2122 38 37 => D
Edwin 2537 87 97 95 => A
Dayan 2415 30 47 => D
```

在上面的Awk脚本中，`avg`变量存放三次测试分数的平均成绩。如果平均成绩大于等于90，等级是`A`，如果成绩大于等于80，等级是`B`，如果平均成绩大于等于70，等级是`C`，否则，等级是`D`。

### 三元运算符`？：`例子：每三行用逗号连成一行
```
$ awk 'ORS=NR%3?",":"\n"' student-marks
Jones 2143 78 84 77,Gondrol 2321 56 58 45,RinRao 2122 38 37
Edwin 2537 87 97 95,Dayan 2415 30 47,
```

在这个例子中，每三行用逗号连成了一行，1、2行后面都用逗号隔开，第三行后面是一个新行；4、5行后面都用逗号隔开，第六行后面是一个新行(这里没有第六行)......

注：翻译自[原文链接](http://www.thegeekstuff.com/2010/02/awk-conditional-statements/)