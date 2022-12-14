---
title: go语言基础
date: 2022-11-13 01:47:52
tags:
---

# Go语言

## 编译打包（生成exe文件）
```
go build
```

## 运行
```
go run ****.go
```

## 引入包
```
import (
	v2 "github.com/YFJie96/wx-mall/controller/api/v2" // 别名
	_ "github.com/YFJie96/wx-mall/docs" // 只执行该库的 init 函数而不对其它导出对象进行真正地导入。因为 Go 语言的数据库驱动都会在 init 函数中注册自己，所以我们只需要进行上述操作即可；否则的话，Go 语言的编译器会提示导入了包却没有使用的错误。
	. "fmt" // 导入之后在你调用这个包的函数时，你可以省略前缀的包名
	"github.com/gin-gonic/gin"
	"github.com/swaggo/gin-swagger"
	"github.com/swaggo/gin-swagger/swaggerFiles"
)
```

## 写一个基本的go文件(helloWorld)
```
//导入语句 双引号包裹
import "fmt"

//函数外只能放置标识符(变量，常量，函数，类型)的声明；不能放置语句

//程序的入口函数
func main() {
    fmt.Println("hello world")
}
```

## 变量

**go语言中推荐使用驼峰命名**

**函数外的每个声明语句必须以关键字开始（var、const、func等）, := 声明方式（同时声明和初始化）只能用于函数内**

**支持括号包裹进行批量声明**

**匿名变量 是一个特殊的变量： _**（任何赋给这个标识符的值都将被抛弃，匿名变量不占用内存空间，不会分配内存。匿名变量与匿名变量之间也不会因为多次声明而无法使用）
```
var (
	name string // ""
	age  int    // 0
	isOk bool   // false
)

func main() {
    v3 := "huihui"
}
```
### nil
看作其他语言的null
### iota

在const关键字出现时将被重置为0，const中每增加一行常量声明，将使 iota 计数一次，我iota的理解就是类似枚举。

```
const (
   a1 = iota //0
   a2 //1
   a3 //2
)


const (
   c1 = iota //0
   c2 = 100  //100
   c3        //100
   c4        //100
)

const (
   d1 = iota //0
   d2 = 100  //100
   d3 = iota //2
   d4        //3
)

const (
   d1, d2 = iota + 1, iota + 2 //1 2
   d3, d4 = iota + 1, iota + 2 //2 3
)
```


### 数据类型

#### 1.基本数据类型

##### 数值型

###### 整数类型（int，int8，int16，int32，int64，uint，uint8，uint16，uint32，uint64，uintptr）int8: 8位，相当于一个字节（1byte=8bit）8位二进制最大值相当于十进制的255，由于int是有符号的整形，所以正负各取一半，即：-128 < int8 < 127 int、uint、uintptr类型根据计算机操做系统决定，如果是32位的操作系统，则为32位宽；如果是64位操作系统，则64位宽
###### 浮点类型（float32，float64）字符型（int8的别名，没有专门的字符型char、使用byte来保存单个字母字符）在utf8字符集中一个中文字符占3个字节，在gbk字符集中一个中文字符占2个字节，由于Go语言采用utf8编码，所以一个字符byte是无法存储一个汉字的
###### 布尔型（bool）默认是false
###### 字符串（string）
###### 复数（complex64, complex128）
###### rune（int32的别名，表示一个Unicode码点，字符串中如果有中文的话一般使用该类型保存）rune它是int32的别名（-2147483648~2147483647），相比于byte（-128～127），可表示的字符更多。由于rune可表示的范围更大，所以能处理一切字符，当然也包括中文字符。特别是在平时计算中文字符，可用rune。
#### 2.派生、复杂数据类型

##### 指针（Pointer）
##### 数组（Array）
```
var a1 [3]bool 
var a2 [4]int

// 如果不初始化：默认元素都是零值（布尔值：false 整型和浮点类型：0 字符串：""）
fmt.Println(a1, a2)
```
##### 结构体（struct）相当于其他语言的类class
##### 管道（Channel）
##### 函数
```
// 函数的定义(从声明开始即拥有参数名和返回名)
func f1(x int, y int) (ret int) {
   return x + y
}
```
##### 方法
方法一般是面向对象编程(OOP)的一个特性,Go语言的方法是关联到类型的，这样可以在编译阶段完成方法的静态绑定。
```
// 文件对象
type File struct {
    fd int
}

// 关闭文件
func (f *File) CloseFile() error {
    // ...
}

// 读文件数据
func (f *File) ReadFile(offset int64, data []byte) int {
    // ...
}
// 这两个函数就成了File类型独有的方法
```
##### 切片（slice）类似动态数组，切片区别于数组，是引用类型， 不是值类型。数组是固定长度的，而切片长度是可变的，我的理解是：切片是对数组一个片段的引用。声明成功不会开辟内存空间，初始化时才会。
```
var s1 []int    //定义一个存放int类型元素的切片
var s2 []string //定义一个存放string类型元素的切片
fmt.Println(s1, s2)
fmt.Println(s1 == nil) //true  为空  没有开辟内存空间
fmt.Println(s2 == nil) //true

var s3 = []int{1, 2, 3}
var s4 = []string{"北苑", "长阳", "望京"}
fmt.Println(s3, s4)
fmt.Println(s3 == nil) //false
fmt.Println(s4 == nil) //false
```
##### 接口（interface）
##### map （集合）

### 查变量类型

```
var int1 = 1
fmt.Printf("int1类型：%T\n", int1) //int1类型：int
```

### 进制转换
```
var i = 1
i4 := int8(i)
i5 := int16(i)
i6 := int32(i)
i7 := int64(i)
```

