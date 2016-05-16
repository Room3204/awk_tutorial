# 9个强大的内置数学函数

&ensp;&ensp;&ensp;&ensp;和Awk内置变量相似，Awk也有一些数字、字符串、输入输出操作的内置函数。Awk有下面三种类型的高级内置函数。

- 数字操作的内置函数
- 字符串操作的内置函数
- 输入输出操作的内置函数

对于刚接触Awk的人来说，可以查阅之前的[Awk简介](./awk_print.md)、[Awk变量](./awk_variables.md)和[Awk操作](./awk_operator.md)。

在这篇文中，我们将学习Awk的内置数字函数。

## Awk的int(n)函数
`int()`函数返回给定参数的整数部分。它会产生给定的n的最小整数部分，n是任意有或没有小数点的数字，如果输入整数，它会返回相同的数，而对于有小数点的数字，它会截取整数部分(说白了就是不能四舍五入)。

#### 例子
```
$ awk 'BEGIN{
print int(3.534);
print int(4);
print int(-5.223);
print int(-5);
}'
3
4
-5
-5
```

## Awk的log(n)函数

`log()`函数产生给定参数n的自然对数(自然对数的底为e,其值为 2.718281828459...)。只有当n为正数时`log()`函数返回n的自然对数的值，如果n是一个不合法的数，它会抛出一个错误。

#### 例子
```
$ awk 'BEGIN{
print log(12);
print log(0);
print log(1);
print log(-1);
}'
2.48491
-inf
0
nan
```

在上面的输出中，可以看到`log(0)`是无穷的，用`-inf`表示，而`log(-1)`产生错误`nan`(不是一个数)。

## Awk的sqrt(n)函数

sqrt()函数产生给定数字n的正平方根，它接受正数，并且如果参数n是负数，则会产生nan错误。

#### 例子
```
$ awk 'BEGIN{
print sqrt(16);
print sqrt(0);
print sqrt(-12);
}'
4
0
nan
```

## Awk的exp(n)函数

`exp()`函数返回e的n次方

#### 例子
```
$ awk 'BEGIN{
print exp(123434346);
print exp(0);
print exp(-12);
}'
inf
1
6.14421e-06
```

在上面的输出中，exp(123434346)返回无穷大是因为溢出。

## Awk的sin(n)函数

`sin()`函数返回弧度n的正弦值

#### 例子
```
$ awk 'BEGIN {
print sin(90);
print sin(45);
}'
0.893997
0.850904
```

## Awk的cos(n)函数

`cos()`函数返回弧度n的余弦值

#### 例子
```
$ awk 'BEGIN {
print cos(90);
print cos(45);
}'
-0.448074
0.525322
```

## Awk的atan2(m,n)函数

`atan2`()函数返回弧度m/n的反正切值

#### 例子
```
$ awk 'BEGIN {
print atan2(30,45);

}'
0.588003
```

## Awk的rand()函数

`rand()`函数产生0到1之间(不包括0和1)的(伪)随机数。运行一次awk时产生的数字是随机的，但是连续运行时产生的数字时可预知的。因为Awk产生随机数的算法是固定的，所以，产生的数字是重复的。

#### 例子

下面的例子产生1000个0到100之间的随机数，并且统计了每个数字出现的次数。
```
$cat rand.awk
BEGIN {
while(i<1000)
{
	n = int(rand()*100);
	rnd[n]++;
	i++;
}
for(i=0;i<=100;i++) {
	print i,"Occured", rnd[i], "times";
	}
}
```

这是上面脚本的一些输出
```
$awk -f rand.awk
0 Occured 6 times
1 Occured 16 times
2 Occured 12 times
3 Occured 6 times
4 Occured 13 times
5 Occured 13 times
6 Occured 8 times
7 Occured 7 times
8 Occured 16 times
9 Occured 9 times
10 Occured 6 times
11 Occured 9 times
12 Occured 17 times
13 Occured 12 times
```

从上面的输出可以确信，`rand()`函数会经常产生重复数字

## Awk的srand(n)函数

`srand()`函数使用给定的参数n作为的产生随机数的种子，因此无论什么时候，当程序运行的时候，它都会从n开始产生随机数。当参数缺省的时候，使用当前时间作为随机数种子。

#### 例子：产生5个从5到50的随机数
```
$cat srand.awk
BEGIN {
#initialize the seed with 5.
srand(5);
# Totally I want to generate 5 numbers.
total=5;
#maximum number is 50.
max=50;
count=0;
while(count < total) {
	rnd = int(rand() * max);
	if ( array[rnd] == 0 ) {
		count++;
		array[rnd]++;
	}
}
for ( i=5; i<=max; i++) {
	if ( array[i] )
		print i;
	}
}
```

在`srand.awk`中，使用`rand()`函数产生随机数并乘最大值来产生小于50的数，并且查看产生的数是否已经在数组中，如果不在，将它作为下标添加到数组中，同时增加循环数，这样它就会产生5个数字。最后通过循环将有值的数组下标打印出来。

这是上面脚本的输出：
```
$ awk -f  srand.awk
9
15
26
37
39
```
注：翻译自[原文链接](http://www.thegeekstuff.com/2010/03/9-powerful-awk-numeric-built-in-functions/)
