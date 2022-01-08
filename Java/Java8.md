# Java 8

Java 8 把函数式编程里的一些思想融入到 Java 的语法中，让我们可以用更少的时间写出高效的代码。

Java 8的优势：

- 代码行更少
- Lambda表达式
- Stream API
- 便于并行
- 减少空指针
- JVM优化
- 兼容老版本

## Lambda

Lambda表达式是一段可以传递的代码，它的核心思想是将面向对象中的传递数据变成传递行为。

基础语法

```java
expression = (variable) -> action
```

- variable：这是一个变量，一个占位符，也可是多个变量
- action：实现代码的逻辑部分，它可以是一行代码也可以是一个代码片段

当一个动作实现无法用一行代码完成，可以编写一段代码用`{}`包裹起来。

函数式接口

函数式接口是只有一个方法的接口，用作Lambda表达式的类型。

函数式接口：输入一个人的年龄，判断这个人是否成年

```java
package org.example;

public class FunctionInterfaceDemo {
    @FunctionalInterface
    interface Predicate<T> {
        boolean test(T t);
    }

    public static boolean doPredicate(int age, Predicate<Integer> predicate) {
        return predicate.test(age);
    }

    public static void main(String[] args) {
        boolean isAdult = doPredicate(20, x -> x >= 18);
        System.out.println(isAdult);
    }
}

```

实际上诸如上述例子中的接口，JDK中有java.util.function包

| 接口      | 参数 | 返回值  | 类别       |
| --------- | ---- | ------- | ---------- |
| Consumer  | T    | void    | 消费型接口 |
| Supplier  | None | T       | 供给型接口 |
| Function  | T    | R       | 函数型接口 |
| Predicate | T    | Boolean | 断言型接口 |

## Stream API

Stream是Java8中处理集合的抽象概念，它可以指定你希望对集合的操作，但是执行操作的时间交给具体实现来决定。

Stream提供了对集合操作的增强，极大的提高了操作集合对象的便利性。

java.util.stream.Stream中定义了许多流操作的方法。

流操作其实可以分为两类：处理操作、聚合操作

- 处理操作：诸如filter、map等处理操作将Stream一层一层的进行抽离，返回一个流给下一层使用
- 聚合操作：从最后一次流中生成一个结果给调用方，foreach只做处理不做返回

filter：筛选数据时用到，参数是`Predicate<T> predicate`

map：将流中处理的数据类型进行转换，可以将流中的值转换为一个新的流

flatMap：用于提取子流的操作

max和min：查找Stream中的最大或最小元素

收集结果：调用Stream中的collect方法

## 日期和时间

新的日期API

- ZoneId：时区ID，用来确定Instant和LocalDateTime相互转换的规则
- Instant：用来表示时间线上的一个点
- LocalDate：表示没有时区的日期，不可变且线程安全
- LocalTime：表示没有时区的时间，不可变且线程安全
- LocalDateTime：表示没有时区的日期时间，不可变且线程安全
- Clock：用于访问当前时刻、日期、时间，用到时区
- Duration：用秒和纳秒表示时间的数量

```java
package org.example;

import java.time.DayOfWeek;
import java.time.LocalDate;
import java.time.temporal.ChronoUnit;

public class LocalDateTest {
    public static void main(String[] args) {
        // 获取当前日期
        LocalDate localDate = LocalDate.now();
        System.out.println(localDate);
        // 添加一天
        LocalDate tomorrow = localDate.plusDays(1);
        // 减去一个月
        LocalDate prevMonth = localDate.minus(1, ChronoUnit.MONTHS);
        // 判断闰年
        boolean leapYear = localDate.isLeapYear();
        System.out.println("是否闰年：" + leapYear);
        // 获取星期
        DayOfWeek week = localDate.getDayOfWeek();
        System.out.println(week);
    }
}

```

```java
package org.example;

import java.time.LocalTime;

public class LocalTimeTest {
    public static void main(String[] args) {
        // 当前时间
        LocalTime now = LocalTime.now();
        System.out.println(now);
        // 添加一个小时
        LocalTime nextHour = now.plusHours(1);
        System.out.println(nextHour);
        // 获取小时和分钟
        int hour = now.getHour();
        System.out.println("小时：" + hour);
        int minute = now.getMinute();
        System.out.println("分钟：" + minute);
    }
}

```

日期格式化

```java
package org.example;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class DateTimeFormatterTest {
    public static void main(String[] args) {
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        System.out.println(now);
        System.out.println(now.format(dateTimeFormatter));
    }
}

```

## 文件读写操作

```java
package org.example;

import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.List;

public class FilesTest {
    public static void main(String[] args) throws IOException {
        // 使用Files类快速实现文件读写操作
        // 写文件
        Files.write(Paths.get("test.txt"), "Hello Java\nHello World".getBytes(StandardCharsets.UTF_8));
        // 读文件
        byte[] data = Files.readAllBytes(Paths.get("test.txt"));
        String content = new String(data, StandardCharsets.UTF_8);
        System.out.println(content);
        // 按照行读取
        List<String> lines = Files.readAllLines(Paths.get("test.txt"));
        System.out.println(lines);
    }
}

```

## 参考资料

[跟上Java8 - 带你实战Java8](https://zhuanlan.zhihu.com/java8)

[Java8-explorer](https://winterbe.com/projects/java8-explorer/)

[30 seconds of java8](https://github.com/hellokaton/30-seconds-of-java8)

