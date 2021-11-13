# Go语言基础

Go语言是Google开发的开源编程语言，其在多核并发上拥有原生的设计优势。

Go 语言被称为“互联网时代的C语言”。Go 语言的风格类似于C语言，其语法在C语言的基础上进行了大幅的简化。

## 学习路线图

![Roadmap](assets/luxian.png)

https://github.com/Alikhll/golang-developer-roadmap

## 基本类型

**整型**

按长度分为：`int8`、`int16`、`int32`、`int64`

对应的无符号整型：`uint8`、`uint16`、`uint32`、`uint64`

**浮点型**

Go语言支持两种浮点型整数：`float32`和`float64`

**复数**

`complex64`和`complex128`

复数有实部和虚部，`complex64`的实部和虚部为32位，`complex128`的实部和虚部为64位。

**布尔值**

Go语言中以`bool`类型进行声明布尔型数据，布尔型数据只有`true`和`false`两个值。

>注意：
>
>布尔类型变量的默认值为false
>
>Go语言中不允许将整型强制转换为布尔型
>
>布尔型无法参与数值运算，也无法与其他类型进行转换

**字符串**

Go语言里的字符串内部使用UTF-8编码，字符串的值为双引号中的内容。

```go
s1 := "hello"
s2 := "祖国"
```

Go语言中要定义一个多行字符串时，就必须使用`反引号`

```go
s1 := `这是第一行
这是第二行
这是第三行
`
```

反引号间换行被作为字符串中的换行，但是所有转义字符均无效，文本将会原样输出。

常用操作：

| 方法                                 | 介绍           |
| ------------------------------------ | -------------- |
| len                                  | 求长度         |
| + 或 fmt.Sprintf                     | 字符串拼接     |
| strings.Split                        | 字符串分割     |
| strings.Contains                     | 判断是否包含   |
| strings.HasPrefix, strings.HasSuffix | 前缀/后缀判断  |
| strings.Index, strings.LastIndex     | 子串出现的位置 |
| strings.Join                         | 拼接           |

**byte和rune**

组成每个字符串的元素叫做“字符”，字符要用单引号包裹起来。

```go
var c1 = '祖'
var c2 = '国'
```

Go语言的字符有以下两种：

> uint8类型，或者叫byte型，代表了ASCII码的一个字符
>
> rune类型，代表一个UTF-8字符

rune类型实际是一个int32

Go语言使用了特殊的rune类型来处理Unicode，让基于Unicode的文本处理更为方便，也可以使用byte类型进行默认字符串处理。

```go
package main

import "fmt"

func main() {
	s := "百度：https://www.baidu.com/"
	// 遍历字符串
	// byte
	for i := 0; i < len(s); i++ {
		fmt.Printf("%v(%c) ", s[i], s[i])
	}
	fmt.Println()
	// rune
	for _, r := range s {
		fmt.Printf("%v(%c) ", r, r)
	}
	fmt.Println()
}

```

![image-20211030172712795](assets/image-20211030172712795.png)

因为UTF8编码下一个中文汉字由`3~4`个字节组成，所以我们不能简单的按照字节去遍历一个包含中文的字符串，否则就会出现上面输出中第一行的结果。

字符串底层是一个byte数组，所以可以和[]byte类型相互转换。字符串是不能修改的 字符串是由byte字节组成，所以字符串的长度是byte字节的长度。 rune类型用来表示utf8字符，一个rune字符由一个或多个byte组成。

要修改字符串，需要先将其转换成`[]rune或[]byte`，完成后再转换为`string`。无论哪种转换，都会重新分配内存，并复制字节数组。

## 数组Array

数组是同一种数据类型的固定长度的序列。

数组长度必须是常量，而且是类型的组成部分，一旦定义，长度不能变。

如果下标在数组合法范围之外，则触发访问越界，会panic

数组是值类型，赋值和传参会复制整个数组，而不是指针。因此改变副本的值，不会改变本身的值。

值拷贝行为会造成性能问题，通常会建议使用slice，或数组指针。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

// 数组求和
func sumArr(a [10]int) int {
	var sum int = 0
	for i := 0; i < len(a); i++ {
		sum += a[i]
	}
	return sum
}

func main() {
	rand.Seed(time.Now().Unix())
	var b [10]int
	for i := 0; i < len(b); i++ {
		b[i] = rand.Intn(1000)
	}
	sum := sumArr(b)
	fmt.Printf("sum=%d\n", sum)
}

```



## 切片Slice

切片是数组的一个引用，因此切片是引用类型。

切片的长度可以改变，因此，切片是一个可变的数组。

![切片](assets/0.jpg)

使用make动态创建slice，避免了数组必须用常量做长度的麻烦。

切片追加：用append内置函数操作切片，向slice尾部添加数据，返回新的slice对象。当数据超出slice.cap限制时，切片就会重新分配底层数组，即便原数组并未填满。

copy ：函数 copy 在两个 slice 间复制数据，复制长度以 len 小的为准。两个 slice 可指向同一底层数组，允许元素区间重叠。

```go
package main

import "fmt"

func main() {
	s := make([]int, 0, 1)
	c := cap(s)

	for i := 0; i < 50; i++ {
		s = append(s, i)
		if n := cap(s); n > c {
			fmt.Printf("cap: %d -> %d\n", c, n)
			c = n
		}
	}
}

```

```bash
cap: 1 -> 2
cap: 2 -> 4
cap: 4 -> 8
cap: 8 -> 16
cap: 16 -> 32
cap: 32 -> 64
```



## 指针

区别于C/C++中的指针，Go语言中的指针不能进行偏移和运算，是安全指针。

Go语言中的函数传参都是值拷贝，当我们想要修改某个变量的时候，我们可以创建一个指向该变量地址的指针变量。传递数据使用指针，而无须拷贝数据。类型指针不能进行偏移和运算。

> new 与 make的区别
>
> 1、二者都是用来做内存分配的
>
> 2、make只用于slice、map以及channel的初始化，返回的还是这三个引用类型本身
>
> 3、而new用于类型的内存分配，并且内存对应的值为类型零值，返回的是指向类型的指针

```go
package main

import "fmt"

func main() {
	var a int
	fmt.Println(&a)
	var p *int
	p = &a
	*p = 20
	fmt.Println(a)
}

```

## Map

map是一种无序的基于key-value的数据结构，Go语言中的map是引用类型，必须初始化才能使用。

```go
package main

import (
	"fmt"
	"math/rand"
	"sort"
	"time"
)

func main() {
	// 初始化随机种子
	rand.Seed(time.Now().UnixNano())

	var scoreMap = make(map[string]int, 200)

	for i := 0; i < 100; i++ {
		key := fmt.Sprintf("student%02d", i)
		value := rand.Intn(100)
		scoreMap[key] = value
	}

	var keys = make([]string, 0, 200)
	for key := range scoreMap {
		keys = append(keys, key)
	}
	// 对切片进行排序
	sort.Strings(keys)
	for _, key := range keys {
		fmt.Println(key, scoreMap[key])
	}
}

```

## 结构体

Go语言中没有”类“的概念，也不支持”类“的继承等面向对象的概念。

Go语言中通过结构体的内嵌再配合接口比面向对象具有更高的扩展性和灵活性。

```go
// 类型定义
type NewInt int
// 类型别名
type MyInt = int
```

Go语言通过struct来实现面向对象。

```go
// 使用type和struct关键字来定义结构体
type 类型名 struct {
    字段名 字段类型
    字段名 字段类型
    ...
}
```

```go
package main

import "fmt"

type student struct {
	name string
	age  int
}

func main() {
	m := make(map[string]*student)
	stus := []student{
		{name: "p1", age: 12},
		{name: "p2", age: 30},
		{name: "p3", age: 26},
	}

	for _, stu := range stus {
		m[stu.name] = &stu
	}
	for k, v := range m {
		fmt.Println(k, "=>", v.name)
	}
}

```

```go
package main

import "fmt"

// Person结构体
type Person struct {
	name string
	age  int
}

// Person的构造函数
func NewPerson(name string, age int) *Person {
	return &Person{
		name: name,
		age:  age,
	}
}

// Person的做梦方法
func (p Person) Dream() {
	fmt.Printf("%s的梦想是成为一名科学家！\n", p.name)
}

func main() {
	p1 := NewPerson("小明", 18)
	p1.Dream()
}

```

