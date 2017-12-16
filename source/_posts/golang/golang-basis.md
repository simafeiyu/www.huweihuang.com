---
title: "[Golang] Golang系列（一）之基础篇"
catalog: true
date: 2017-09-16 10:50:57
type: "categories"
subtitle:
header-img: 
tags:
- Golang
catagories:
- Golang
---

## 一、初识Go语言

### （一）概述

一个在语言层面实现了并发机制的类C通用型编程语言。

### （二）Go关键字（25个）

| 类别     | 关键字                                     | 说明                         |
| ------ | --------------------------------------- | -------------------------- |
| 程序声明   | package，import                          | 包的声明和导入                    |
| 声明与定义  | var，const                               | 变量和常量的声明                   |
|        | type                                    | 用于定义类型                     |
| 复合数据类型 | struct                                  | 定义结构体，类似java中的class        |
|        | interface                               | 定义接口                       |
|        | map                                     | 定义键值对                      |
|        | func                                    | 定义函数和方法                    |
|        | chan                                    | 定义管道，并发中channel通信          |
| 并发编程   | go                                      | 并发编程                       |
|        | select                                  | 用于选择不同类型通信                 |
| 流程控制   | for；if，else；switch，case                 | 循环语句；条件语句；选择语句             |
|        | break，continue，fallthrough，default，goto | 跳转语句等                      |
|        | return                                  | 函数返回值                      |
|        | defer                                   | 延迟函数，用于return前释放资源         |
|        | range                                   | 用于读取slice，map，channel容器类数据 |

### （三）Go语言命令

 Usage：go command [arguments]

| 分类   | 命令       | 说明                                       |
| ---- | -------- | ---------------------------------------- |
|      | build    | compile packages and dependencies        |
|      | clean    | remove object files                      |
|      | doc      | show documentation for package or symbol |
|      | env      | print Go environment information         |
|      | fix      | run go tool fix on packages              |
|      | fmt      | run gofmt on package sources             |
|      | generate | generate Go files by processing source   |
|      | get      | download and install packages and dependencies |
|      | install  | compile and install packages and dependencies |
|      | list     | list packages                            |
|      | run      | compile and run Go program               |
|      | test     | test packages                            |
|      | tool     | run specified go tool                    |
|      | version  | print Go version                         |
|      | vet      | run go tool vet on packages              |

## 二、顺序编程

### 1.变量

#### 1.1变量声明

```go
//1、单变量声明,类型放在变量名之后，可以为任意类型
var 变量名 类型
var v1,v2,v3 string //多变量同类型声明
//2、多变量声明
var {
    v1 int
    v2 []int
}
```

#### 1.2变量初始化

```go
//1、使用关键字var，声明变量类型并赋值
var v1 int=10
//2、使用关键字var，直接对变量赋值，go可以自动推导出变量类型
var v2=10
//3、直接使用“：=”对变量赋值，不使用var，两者同时使用会语法冲突，推荐使用
v3:=10
```

#### 1.3变量赋值

```go
//1、声明后再变量赋值
var v int
v=10
//2、多重赋值，经常使用在函数的多返回值中，err,v=func(arg)
i，j=j,i  //两者互换，并不需要引入中间变量
```

#### 1.4匿名变量

```go
//Go中所有声明后的变量都需要调用到，当出现函数多返回值，并且部分返回值不需要使用时，可以使用匿名变量丢弃该返回值
func GetName()(firstName,lastName,nickName string){
  return "May","Chan","Make"
}
_,_,nickName:=GetName()  //使用匿名变量丢弃部分返回值
```

### 2.常量

​	Go语言中，常量是编译时期就已知且不可变的值，常量可以是数值类型（整型、浮点型、复数类型）、布尔类型、字符串类型。

#### 2.1字面常量

```go
//字面常量(literal)指程序中硬编码的常量
3.14
“foo”
true
```

#### 2.2常量定义

```go
//1、可以限定常量类型，但非必需
const Pi float64 = 3.14
//2、无类型常量和字面常量一样
const zero=0.0
//3、多常量赋值
const(
  size int64=1024
  eof=-1
)
//4、常量的多重赋值，类似变量的多重赋值
const u,v float32=0,3
const a,b,c=3,4,"foo"    //无类型常量的多重赋值
//5、常量赋值是编译期行为，可以赋值为一个编译期运算的常量表达式
const mask=1<<3
```

#### 2.3预定义常量

```go
//预定义常量：true、false、iota
//iota：可修改常量，在每次const出现时被重置为0，在下一个const出现前，每出现一次iota，其代表的值自动增1。
const(          //iota重置为0
  c0=iota       //c0==0
  c1=iota       //c1==1
  c2=iota       //c2==2
)
//两个const赋值语句一样可以省略后一个
const(          //iota重置为0
  c0=iota       //c0==0
  c1            //c1==1
  c2            //c2==2
)
```

#### 2.4枚举

枚举指一系列相关常量。

```go
const(
  Sunday=iota    //Sunday==0,以此类推
  Monday
  Tuesday
  Wednesday
  Thursday
  Friday
  Saturday       //大写字母开头表示包外可见
  numberOfDays   //小写字母开头表示包内私有
)
```

### 3.类型

#### 3.1基础类型

##### 3.1.1布尔类型

```go
//布尔类型的关键字为bool,值为true或false，不可写为0或1
var v1 bool
v1=true
//接受表达式判断赋值，不支持自动或强制类型转换
v2:=(1==2)
```

##### 3.1.2整型

 ![整型](/img/article/golang/整型.png)

```go
//1、类型表示
//int和int32为不同类型，不会自动类型转换需要强制类型转换
//强制类型转换需注意精度损失（浮点数→整数），值溢出（大范围→小范围）
var v2 int32
v1:=64
v2=int32(v1)

//2、数值运算,支持“+,-,*,/和%”
5%3 //求余

//3、比较运算,“<,>,==,>=,<=,!=”
//不同类型不能进行比较例如int和int8，但可以与字面常量（literal）进行比较
var i int32
var j int64
i,j=1,2
if i==j  //编译错误，不同类型不能进行比较
if i==1 || j==2  //编译通过，可以与字面常量（literal）进行比较

//4、位运算
//Go(^x)取反与C语言(~x)不同，其他类似，具体见下表
```

  ![位运算](/img/article/golang/位运算.png)

##### 3.1.3浮点型

```go
//1、浮点型分为float32(类似C中的float)，float64(类似C中的double)
var f1 float32
f1=12     //不加小数点，被推导为整型
f2:=12.0  //加小数点，被推导为float64
f1=float32(f2)  //需要执行强制转换
//2、浮点数的比较
//浮点数不是精确的表达方式，不能直接使用“==”来判断是否相等，可以借用math的包math.Fdim 
```

##### 3.1.4复数类型

```go
//1、复数的表示
var v1 complex64
v1=3.2+12i
//v1 v2 v3 表示为同一个数
v2:=3.2+12i
v3:=complex(3.2,12)
//2、实部与虚部
//z=complex(x,y),通过内置函数实部x=real(z),虚部y=imag(z)
```

##### 3.1.5字符串

```go
//声明与赋值
var str string
str="hello world"
```

 ![字符串操作](/img/article/golang/字符串操作.png)

##### 3.1.6字符类型

```go
//1、byte，即uint8的别名
//2、rune，即Unicode
```

##### 3.1.7错误类型（error）

#### 3.2复合类型

##### 3.2.1数组(array)

数组表示同一类型数据，数组长度定义后就不可更改，长度是数组内的一个内置常量，可通过len()来获取。

```go
//1、创建数组
var array1 [5]int    //声明：var 变量名 类型
var array2 [5]int=[5]int{1,2,3,4,5}   //初始化
array3：=[5]int{1,2,3,4,5}    //直接用“：=”赋值
[3][5]int  //二维数组
[3]*float  //指针数组

//2、元素访问
for i,v:=range array{
  //第一个返回值为数组下标，第二个为元素的值
}

//3、值类型
//数组在Go中作为一个值类型，值类型在赋值和函数参数传递时，只复制副本，因此在函数体中并不能改变数组的内容，需用指针来改变数组的值。
```

##### 3.2.2切片(slice)

​	数组在定义了长度后无法改变，且作为值类型在传递时产生副本，并不能改变数组元素的值。因此切片的功能弥补了这个不足，切片类似指向数组的一个指针。可以抽象为三个变量：指向数组的指针；切片中元素的个数(len函数)；已分配的存储空间(cap函数)。

```go
//1、创建切片
//a)基于数组创建
var myArray [5]int=[5]{1,2,3,4,5}
var mySlice []int=myArray[first:last]
slice1=myArray[:]   //基于数组所有元素创建
slice2=myArray[:3]  //基于前三个元素创建
slice3=myArray[3:]  //基于第3个元素开始后的所有元素创建
//b)直接创建
slice1:=make([]int,5)       //元素初始值为0，初始个数为5
slice2:=make([]int,5,10)    //元素初始值为0，初始个数为5，预留个数为10
slice3:=[]int{1,2,3,4,5}    //初始化赋值
//c)基于切片创建
oldSlice:=[]int{1,2,3,4,5}
newSlice:=oldSlice[:3]   //基于切片创建，不能超过原切片的存储空间(cap函数的值)

//2、元素遍历
for i,v:=range slice{
  //与数组的方式一致，使用range来遍历
  //第一个返回值(i)为索引，第二个为元素的值(v)
}

//3、动态增减元素
//切片分存储空间(cap)和元素个数(len)，当存储空间小于实际的元素个数，会重新分配一块原空间2倍的内存块，并将原数据复制到该内存块中，合理的分配存储空间可以以空间换时间，降低系统开销。
//添加元素
newSlice:=append(oldSlice,1,2,3)   //直接将元素加进去，若存储空间不够会按上述方式扩容。
newSlice1:=append(oldSlice1,oldSlice2...)  //将oldSlice2的元素打散后加到oldSlice1中，三个点不可省略。

//4、内容复制
//copy()函数可以复制切片，如果切片大小不一样，按较小的切片元素个数进行复制
slice1:=[]int{1,2,3,4,5}
slice2:=[]int{6,7,8}
copy(slice2,slice1)   //只会复制slice1的前三个元素到slice2中
copy(slice1,slice1)   //只会复制slice2的三个元素到slice1中的前三个位置
```

##### 3.2.3键值对(map)

map是一堆键值对的未排序集合。

```go
//1、先声明后创建再赋值
var map1 map[键类型] 值类型
//创建
map1=make(map[键类型] 值类型)
map1=make(map[键类型] 值类型 存储空间)
//赋值
map1[key]=value

// 直接创建
m2 := make(map[string]string)
// 然后赋值
m2["a"] = "aa"
m2["b"] = "bb"

// 初始化 + 赋值一体化
m3 := map[string]string{
    "a": "aa",
    "b": "bb",
}

//2、元素删除
//delete()函数删除对应key的键值对，如果key不存在，不会报错；如果value为nil，则会抛出异常(panic)。
delete(map1,key)  

//3、元素查找
value,ok:=myMap[key]
if ok{//如果找到
  //处理找到的value值
}

//遍历
for key,value:=range myMap{
    //处理key或value
}
```

##### 3.2.4指针(pointer)

##### 3.2.5结构体(struct)

##### 3.2.6接口(interface)

##### 3.2.7通道(chan)

### 4.流程语句

#### 4.1条件语句

```go
//在if之后条件语句之前可以添加变量初始化语句，用;号隔离
if <条件语句> {    //条件语句不需要用括号括起来，花括号必须存在
  //语句体
}else{
  //语句体
}

//在有返回值的函数中，不允许将最后的return语句放在if...else...的结构中，否则会编译失败
//例如以下为错误范例
func example(x int) int{
  if x==0{
    return 5
  }else{
    return x  //最后的return语句放在if-else结构中，所以编译失败
  }
}
```

#### 4.2选择语句

```go
//1、根据条件不同，对应不同的执行体
switch i{
  case 0:
  	fmt.Printf("0")
  case 1:                //满足条件就会退出，只有添加fallthrough才会继续执行下一个case语句
  	fmt.Prinntf("1")
  case 2,3,4:            //单个case可以出现多个选项
  	fmt.Printf("2,3,4")
  default:               //当都不满足以上条件时，执行default语句
  	fmt.Printf("Default")
}

//2、该模式等价于多个if-else的功能
switch {
  case <条件表达式1>:
  	语句体1
  case <条件表达式2>:
  	语句体2
}
```

#### 4.3循环语句

```go
//1、Go只支持for关键字，不支持while，do-while结构
for i,j:=0,1;i<10;i++{    //支持多个赋值
  //语句体
}

//2、无限循环
sum:=1
for{  //不接条件表达式表示无限循环
  sum++
  if sum > 100{
    break   //满足条件跳出循环
  }
}

//3、支持continue和break，break可以指定中断哪个循环，break JLoop(标签)
for j:=0;j<5;j++{
  for i:=0;i<10;i++{
    if i>5{
      break JLoop   //终止JLoop标签处的外层循环
  }
  fmt.Println(i)
}
JLoop:    //标签处
...
```

#### 4.4跳转语句

```go
//关键字goto支持跳转
func myfunc(){
  i:=0
  HERE:           //定义标签处
  fmt.Println(i)
  i++
  if i<10{
    goto HERE     //跳转到标签处
  }
}
```

### 5.函数

#### 5.1函数定义与调用

```go
//1、函数组成：关键字func ,函数名，参数列表，返回值，函数体，返回语句
//先名称后类型
func 函数名(参数列表)(返回值列表){  //参数列表和返回值列表以变量声明的形式，如果单返回值可以直接加类型
  函数体
  return    //返回语句
}
//例子
func Add(a,b int)(ret int,err error){
  //函数体 
  return   //return语句
}

//2、函数调用
//先导入函数所在的包，直接调用函数
import "mymath"
sum,err:=mymath.Add(1,2)   //多返回值和错误处理机制
//可见性，包括函数、类型、变量
//本包内可见(private)：小写字母开头
//包外可见(public)：大写字母开头
```

#### 5.2不定参数

```go
//1、不定参数的类型
func myfunc(args ...int){   //...type不定参数的类型，必须是最后一个参数，本质是切片
  for _,arg:=range args{
    fmt.Println(arg)
  }
}
//函数调用,传参可以选择多个，个数不定
myfunc(1,2,3)
myfunc(1,2,3,4,5)

//2、不定参数的传递，假如有个变参函数myfunc2(args ...int)
func myfunc1(args ...int){
  //按原样传递
  myfunc2(args...)
  //传递切片
  myfunc2(args[1:]...)
}

//3、任意类型的不定参数，使用interface{}作为指定类型
func Printf(format string,args ...interface{}){   //此为标准库中fmt.Printf()函数的原型
  //函数体
}
```

#### 5.3多返回值

```go
//多返回值
func (file *File) Read(b []byte) (n int,err error)
//使用下划线"_"来丢弃返回值
n,_:=f.Read(buf)
```

#### 5.4匿名函数与闭包

```go
//1、匿名函数：不带函数名的函数，可以像变量一样被传递
func(a,b int,z float32) bool{  //没有函数名
  return a*b<int(z)
}
f:=func(x,y int) int{
  return x+y
}

//2、闭包
```

