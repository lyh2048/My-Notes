# 快学Scala

## Scala简介

Scala是一门现代的多范式编程语言，集成了面向对象和函数式语言的特性。

Scala运行于Java平台上，并兼容现有的Java程序，Scala代码可以调用Java方法，访问Java字段，继承Java类和实现Java接口。

Scala是一门非常纯粹的面向对象编程语言，在Scala中，每个值都是对象，每个操作都是方法调用。

Scala的优点：

- 具备强大的并发性，支持函数式编程，可以更好地支持分布式系统
- 语法简洁，能提供优雅的API
- 兼容Java，运行速度快，且能融合到Hadoop生态圈中

## Scala基础

### 声明值和变量

Scala有两种类型的变量，一种是val，是不可变的，在声明时就必须被初始化，而且初始化以后不能再赋值；

另一种是var，是可变的，声明的时候需要进行初始化，初始化以后还可以再次对其赋值。

Scala具有“类型推断”能力，可以自动推断出变量的类型。

### 基本数据类型和操作

Scala的数据类型包括：Byte、Char、Short、Int、Long、Float、Double和Boolean。

和Java不同的是，在Scala中，这些类型都是“类”，并且都是包scala的成员。

对于字符串，Scala用java.lang.String类来表示字符串。

在Scala中，可以使用加（+）、减（-）、乘（*）、除（/）、余数（%）等操作符，而且，这些操作符都是方法。

和Java不同，在Scala中并没有提供++和--操作符，当需要递增和递减时，可以采用如下方式表达：

```scala
scala> var num = 10
var num: Int = 10

scala> num -= 1

scala> num += 1

scala> println(num)
10
```

### Range

在执行for循环时，我们经常会用到数值序列，这时就可以采用Range来实现。

Range支持创建不同数据类型的数值序列。

在创建Range时，需要给出区间的起点和终点以及步长（默认步长为1）

![image-20220104093318734](assets/image-20220104093318734.png)

### 打印语句

在Scala编程中，我们经常要打印输出结果，我们可以使用print、println来实现。

此外，Scala还带有C语言风格的格式化字符串的printf函数：

![image-20220104093806934](assets/image-20220104093806934.png)

### 读写文件



## 控制结构

## 数据结构

## 函数式编程

## 集合操作