# Awk循环语句

&ensp;&ensp;&ensp;&ensp;在这篇教程中，我们将通过7个实例来看一下Awk的`while`、`do while`、`for`、`break`、`continue`、`exit`语句。

&ensp;&ensp;&ensp;&ensp;Awk循环语句用来重复执行一系列指令，只要条件为真，指令就会重复执行。Awk的许多循环指令和C语言的很像。

### `while`循环
```
语法：
while(condition)
	actions
```

- `while`是一个关键字
- `condition`是判断的条件
- `actions`是循环体，可以包含一条或多条指令，如果包含多条指令，需要用花括号括起来

**它是怎么工作的：** 首先`while`循环会检查条件，如果条件为真，就会执行循环体中的指令，当这些指令执行完后，会再一次检查条件，如果仍为真，循环体会再一次被执行，这个过程会一直循环，直到条件为假。如果第一次检查条件为假，那么循环体永远不会被执行。

#### `while`循环例子

```
$awk 'BEGIN { while (count++<50) string=string "x"; print string }'
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

在上面的例子中，用`BEGIN{}`使它在其他任何语句(然而这里并没有其他语句......)前执行。在这个块中，`while`循环向变量`string`中追加`'x'`50次。`count`是一个自增变量，检查是否小于50，因此当循环50次后，条件判断为假。当循环结束后，变量`string`打印出来，这个程序没有其他语句，执行完`BEGIN`块后就退出。

### `do while`循环

**它是怎么工作的：** `do while`循环被称为*退出控制循环*，对应的，`while`被称为*进入控制循环*。因为`while`首先检查条件，然后再决定是否执行循环体，但是`do while`循环先执行一次循环体，然后在进行判断，只要条件为真，就会重复执行循环体。
```
语法：
do
	action
while(condition)
```

即使条件为假，指令也会先执行一次。

#### `do while`循环例子：打印信息至少一次
```
$ awk 'BEGIN{
count=1;
do
	print "This gets printed at least once";
while(count!=1)
}'
This gets printed at least once
```

在上面的脚本中，打印语句至少执行一次。如果使用`while`循环，`count`被初始化为1后首先检查条件，第一次循环时条件判断为假，因此打印语句不会执行，但是`do while`会先执行一次循环体，因此它执行了打印语句。

### `for`循环语句
`for`循环和`while`一样，但是它的语法使用起来更简单。
```
for(initialization;condition;increment/decrement)
	actions
```

**它是怎么工作的：** `for`循环开始先执行初始化，然后检查条件，如果条件为真，就会执行循环体，然后自加或自减，只要条件为真，就会重复执行循环体，并自加或自减。

#### `for`例子：将每一行的域反序打印
```
$ awk 'BEGIN{ORS="";}{ for (i=NF; i>0; i--) print $i," "; print "\n"; }' student-marks
77  84  78  2143  Jones
45  58  56  2321  Gondrol
37  38  2122  RinRao
95  97  87  2537  Edwin
47  30  2415  Dayan
```

我们在[Awk内置变量](./)中谈论了`NF`变量，在处理每行时，Awk将`NF`设置为那一行域的数量。在上面的脚本中，将域从`NF`到1一个一个的以反序打印出来，它从`$NF`开始，然后是`$(NF-1)`......直到`$1`，执行完这些后，打印出一个换行符。

接下来是一些能和循环语句一起用的其他语句。

### `break`语句

`break`语句用来跳出包含它的内层循环。

#### `break`例子：只执行10此循环
```
$ awk 'BEGIN{while(1) print "forever"}'
```

上面的`while`循环会一直打印字符串`forever`，因为条件永远不会为假。现在，如果你想循环10次后结束，看下面的脚本：
```
$ awk 'BEGIN{
x=1;
while(1)
{
	print "Iteration";
	if ( x==10 )
		break;
	x++;
}}'
Iteration
Iteration
Iteration
Iteration
Iteration
Iteration
Iteration
Iteration
Iteration
Iteration
```

在上面的脚本中会检查变量`x`的值，等它等于10的时候，就会用`break`语句跳出循环。

### `continue`语句

`continue`语句会跳过后面的循环体，并立即开始下一次循环。

#### `continue`例子：不执行第五次循环
```
$ awk 'BEGIN{
x=1;while(x<=10)
{
	if(x==5){
	x++;
	continue;
	}
print "Value of x",x;x++;
}
}'
Value of x 1
Value of x 2
Value of x 3
Value of x 4
Value of x 6
Value of x 7
Value of x 8
Value of x 9
Value of x 10
```

在上面的脚本中，每次循环都会打印`x`的值，但是当`x`等于5时，`x`自加，然后执行`continue`语句进入下一次循环，它不会执行循环的剩余部分，因此不会打印`Value of x 5`。`continue`语句只有用在循环中才有意义。

### `exit`语句

`exit`语句会使脚本立刻停止执行输入，并且停止处理输入，所有剩余的输入都会被忽略。

`exit`接受任何整数参数作为awk程序的退出码，如果没有提供参数，`exit`返回状态`0`。

#### `exit`例子：在第五次循环时退出
```
$ awk 'BEGIN{
x=1;
while(x<=10)
{
if(x==5){
	exit;
	}
print "Value of x",x;x++;
}
}'
Value of x 1
Value of x 2
Value of x 3
Value of x 4
```

在上面的脚本中，一旦`x`的值等于5，就会调用`exit`结束awk程序的执行，因此只会打印到`Value of x 4`。

注：翻译自[原文链接](http://www.thegeekstuff.com/2010/02/unix-awk-do-while-for-loops/)，其中，关于`for`循环还有一个例子，不过依在下之愚见，这个例子是错的，所以翻译时并没有加上，有兴趣者可以自己看一下，如果可以，我们可以再交流一下。