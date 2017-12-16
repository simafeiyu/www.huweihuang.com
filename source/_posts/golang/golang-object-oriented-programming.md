---
title: "[Golang] Golang系列（二）之面向对象编程"
catalog: true
date: 2017-09-15 10:50:57
type: "categories"
subtitle:
header-img: 
tags:
- Golang
catagories:
- Golang
---

- **面向对象编程**

​       把一组数据结构和处理它们的方法组成对象（object），把相同行为的对象归纳为类（class），通过类的封装（encapsulation）隐藏内部细节，通过继承（inheritance）实现类的特化（specialization）[方法的重写，子类不同于父类的特性]／泛化（generalization）[共性，子类都拥有父类的特性]，通过多态（polymorphism）实现基于对象类型的动态分派（dynamic dispatch）。

- **面对对象思想**

​       面向对象思想是对现实世界事物的抽象，系统中一切事物皆为对象；对象是属性及其操作的封装体；对象可按其性质划分为类，对象成为类的实例；实例关系和继承关系是对象之间的静态关系；消息传递是对象之间动态联系的唯一形式，也是计算的唯一形式；方法是消息的序列。

## （一）类型系统[类的声明]

类型系统：

- 一组基本类型构成的“基本类型集合”；
- “基本类型集合”上定义的一系列组合、运算、转换方法。

类型系统包括基础类型（byte、int、bool、float等）；复合类型（数组、结构体、指针等）；可以指向任何对象的类型（Any类型，类似Java的Object类型）；值语义和引用语义；面向对象类型；接口。Go大多数类型为值语义，可以给任何类型添加方法（包括内置类型，不包括指针类型）。Any类型是空接口即interface{}。

### 1.方法

1、为类型添加方法[类方法声明]，方法即为有接收者的函数
func (对象名 对象类型) 函数名(参数列表) (返回值列表)
可随时为某个对象添加方法即为某个方法添加归属对象（receiver），以方法为中心
在Go语言中没有隐藏的this指针，即显示传递，形参即为this，例如以下的形参为a。

```go
type Integer int
func (a Integer) Less(b Integer) bool{  //表示a这个对象定义了Less这个方法，a可以为任意类型
  return a<b                           
}
//类型基于值传递，如果要修改值需要传递指针
func (a *Integer) Add(b Integer){
  *a+=b    //通过指针传递来改变值
}
```

### 2.值语义和引用语义

值类型：b的修改并不会影响a的值

引用类型：b的修改会影响a的值

Go大多类型为值语义，包括基本类型：byte，int，string等；复合类型：数组，结构体(struct)，指针等

```go
//2、值语义和引用语义
b=a
b.Modify()
 
//值类型
var a=[3]int{1,2,3}
b:=a
b[1]++
fmt.Println(a,b)   //a=[1,2,3]  b=[1,3,3]
//引用类型
a:=[3]int{1,2,3}
b:=&a              //b指向a,即为a的地址，对b指向的值改变实际上就是对a的改变（数组本身就是一种地址指向）
b[1]++
fmt.Println(a,*b)  //a=[1,3,3]  b=[1,3,3]   //*b,取地址指向的值
```

### 3.结构体

3、结构体[类属性的声明]
struct的功能类似Java的class，可实现嵌套组合(类似继承的功能)
struct实际上就是一种复合类型，只是对类中的属性进行定义赋值，并没有对方法进行定义，方法可以随时定义绑定到该类的对象上，更具灵活性。可利用嵌套组合来实现类似继承的功能避免代码重复。

```go
type Rect struct{   //定义矩形类
  x,y float64       //类型只包含属性，并没有方法
  width,height float64
}
func (r *Rect) Area() float64{    //为Rect类型绑定Area的方法，*Rect为指针引用可以修改传入参数的值
  return r.width*r.height         //方法归属于类型，不归属于具体的对象，声明该类型的对象即可调用该类型的方法
}
```

## （二）初始化[实例化对象]

 数据初始化的内建函数new()与make()，二者都是用来分配空间。区别如下:

- **new()**

1. func new(Type) *Type
2. 内置函数 `new` 分配空间。传递给`new` 函数的是一个**类型**，不是一个值。返回值是指向这个新分配的零值的**指针**

- **make()**

1. func make(Type, size IntegerType) Type 
2. 内建函数 `make` 分配并且初始化 一个 slice, 或者 map 或者 chan 对象。 并且只能是这三种对象。 和 `new` 一样，第一个参数是 类型，不是一个值。 但是`make` 的返回值就是这个类型（即使一个引用类型），而不是指针。 具体的返回值，依赖具体传入的类型。

```go
//创建实例
rect1:=new(Rect)   //new一个对象
rect2:=&Rect{}     //为赋值默认值，bool默认值为false，int默认为零值0，string默认为空字符串
rect3:=&Rect{0,0,100,200}     //取地址并赋值,按声明的变量顺序依次赋值
rect4:=&Rect{width:100,height:200}    //按变量名赋值不按顺序赋值
//构造函数：没有构造参数的概念，通常由全局的创建函数NewXXX来实现构造函数的功能
func NewRect(x,y,width,height float64) *Rect{
  return &Rect{x,y,width,height}     //利用指针来改变传入参数的值达到类似构造参数的效果
}
//方法的重载,Go不支持方法的重载（函数同名，参数不同）
//v …interface{}表示参数不定的意思，其中v是slice类型，及声明不定参数，可以传入任意参数，实现类似方法的重载
func (poem *Poem) recite(v ...interface{}) {
    fmt.Println(v)
}
```

## （三）匿名组合[继承]

​       组合，即方法代理，例如A包含B，即A通过消息传递的形式代理了B的方法，而不需要重复写B的方法。

​       继承是指这样一种能力：它可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。继承主要为了代码复用，继承也可以扩展已存在的代码模块（类）。

​       严格来讲，继承是“a kind of ”，即子类是父类的一种，例如student是person的一种；组合是“a part of”，即父类是子类中的一部分，例如眼睛是头部的一部分。

```go
//1、匿名组合的方式实现了类似Java继承的功能，可以实现多继承
type Base struct{
  Name string
}
func (base *Base) Foo(){...}    //Base的Foo()方法
func (base *Base) Bar(){...}    //Base的Bar()方法
type Foo struct{  
  Base                         //通过组合的方式声明了基类，即继承了基类
  ...
}
func (foo *Foo) Bar(){
  foo.Base.Bar()               //并改写了基类的方法，该方法实现时先调用基类的Bar()方法
  ...                          //如果没有改写即为继承，调用foo.Foo()和调用foo.Base.Foo()的作用的一样的
}
//修改内存布局
type Foo struct{
  ...   //其他成员信息
  Base
}
//以指针方式组合
type Foo struct{
  *Base   //以指针方式派生，创建Foo实例时，需要外部提供一个Base类实例的指针
  ...
}
//名字冲突问题,组合内外如果出现名字重复问题，只会访问到最外层，内层会被隐藏，不会报错，即类似java中方法覆盖/重写。
type X struct{
  Name string
}
type Y struct{
  X             //Y.X.Name会被隐藏，内层会被隐藏
  Name string   //只会访问到Y.Name，只会调用外层属性
}
```

## （四）可见性[封装]

​       封装，也就是把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。

​       **封装的本质或目的其实程序对信息(数据)的控制力。封装分为两部分：该隐藏的隐藏，该暴露的暴露。封装可以隐藏实现细节，使得代码模块化。**

​       Go中用大写字母开头来表示public，可以包外访问；小写字母开头来表示private，只能包内访问；访问性是包级别非类型级别
​       如果可访问性是类型一致的，可以加friend关键字表示朋友关系可互相访问彼此的私有成员(属性和方法)

```go
type Rect struct{
  X,Y float64
  Width,Height float64           //字母大写开头表示该属性可以由包外访问到
}
func (r *Rect) area() float64{   //字母小写开头表示该方法只能包内调用
  return r.Width*r.Height
}
```

## （五）接口[多态]

​       多态性（polymorphisn）是允许你将父对象设置成为和一个或更多的他的子对象相等的技术，赋值之后，父对象就可以根据当前赋值给它的子对象的特性以不同的方式运作。

​       简而言之，就是允许将子类类型的指针赋值给父类类型的指针。

​       即一个引用变量倒底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在由程序运行期间才能决定。不修改程序代码就可以改变程序运行时所绑定的具体代码，让程序可以选择多个运行状态，这就是多态性。多态分为编译时多态（静态多态）和运行时多态（动态多态），编译时多态一般通过方法重载实现，运行时多态一般通过方法重写实现。

### 5.1接口概念

​      接口即一组方法的集合，定义了对象的一组行为，方法包含实际的代码。换句话说，一个接口就是定义（规范或约束），而方法就是实现，接口的作用应该是将定义与实现分离，降低耦合度。习惯用“er”结尾来命名，例如“Reader”。接口与对象的关系是多对多，即一个对象可以实现多个接口，一个接口也可以被多个对象实现。

​      接口是Go语言整个类型系统的基石，其他语言的接口是不同组件之间的契约的存在，对契约的实现是强制性的，必须显式声明实现了该接口，这类接口称之为“侵入式接口”。而Go语言的接口是隐式存在，只要实现了该接口的所有函数则代表已经实现了该接口，并不需要显式的接口声明。

- **接口的比喻**

​     你的电脑上只有一个USB接口。这个USB接口可以接MP3，数码相机，摄像头，鼠标，键盘等。。。所有的上述硬件都可以公用这个接口，有很好的扩展性，该USB接口定义了一种规范，只要实现了该规范，就可以将不同的设备接入电脑，而设备的改变并不会对电脑本身有什么影响（低耦合）。

- **面向接口编程**

​      接口表示调用者和设计者的一种约定，在多人合作开发同一个项目时，事先定义好相互调用的接口可以大大提高开发的效率。接口是用类来实现的，实现接口的类必须严格按照接口的声明来实现接口提供的所有功能。有了接口，就可以在不影响现有接口声明的情况下，修改接口的内部实现，从而使兼容性问题最小化。
​      当其他设计者调用了接口后，就不能再随意更改接口的定义，否则项目开发者事先的约定就失去了意义。但是可以在类中修改相应的代码，完成需要改动的内容。

### 5.2非侵入式接口

非侵入式接口：一个类只需要实现了接口要求的所有函数就表示实现了该接口，并不需要显式声明

```go
type File struct{
  //类的属性
}
//File类的方法
func (f *File) Read(buf []byte) (n int,err error)
func (f *File) Write(buf []byte) (n int,err error)
func (f *File) Seek(off int64,whence int) (pos int64,err error)
func (f *File) Close() error
//接口1：IFile
type IFile interface{
  Read(buf []byte) (n int,err error)
  Write(buf []byte) (n int,err error)
  Seek(off int64,whence int) (pos int64,err error)
  Close() error
}
//接口2：IReader
type IReader interface{
  Read(buf []byte) (n int,err error)
}
//接口赋值,File类实现了IFile和IReader接口，即接口所包含的所有方法
var file1 IFile = new(File)
var file2 IReader = new(File)
```

### 5.3接口赋值

只要类实现了该接口的所有方法，即可将该类赋值给这个接口，接口主要用于多态化方法。即对接口定义的方法，不同的实现方式。

接口赋值：
**1）将对象实例赋值给接口**

```go
type IUSB interface{
    //定义IUSB的接口方法
}
//方法定义在类外，绑定该类，以下为方便，备注写在类中
type MP3 struct{
    //实现IUSB的接口，具体实现方式是MP3的方法
}
type Mouse struct{
    //实现IUSB的接口，具体实现方式是Mouse的方法
}
//接口赋值给具体的对象实例MP3
var usb IUSB =new(MP3)
usb.Connect()
usb.Close()
//接口赋值给具体的对象实例Mouse
var usb IUSB =new(Mouse)
usb.Connect()
usb.Close()
```

**2）将接口赋值给另一个接口**

1. 只要两个接口拥有相同的方法列表（与次序无关），即是两个相同的接口，可以相互赋值
2. 接口赋值只需要接口A的方法列表是接口B的子集（即假设接口A中定义的所有方法，都在接口B中有定义），那么B接口的实例可以赋值给A的对象。反之不成立，即子接口B包含了父接口A，因此可以将子接口的实例赋值给父接口。
3. 即子接口实例实现了子接口的所有方法，而父接口的方法列表是子接口的子集，则子接口实例自然实现了父接口的所有方法，因此可以将子接口实例赋值给父接口。

```go
type Writer interface{    //父接口
    Write(buf []byte) (n int,err error)
}
type ReadWriter interface{    //子接口
    Read(buf []byte) (n int,err error)
    Write(buf []byte) (n int,err error)
}
var file1 ReadWriter=new(File)   //子接口实例
var file2 Writer=file1           //子接口实例赋值给父接口
```

### 5.4接口查询

若要在 switch 外判断一个接口类型是否实现了某个接口，可以使用“逗号 ok ”。

value, ok := Interfacevariable.(implementType)

其中 Interfacevariable 是接口变量（接口值），implementType 为实现此接口的类型，value 返回接口变量实际类型变量的值，如果该类型实现了此接口返回 true。

```go
//判断file1接口指向的对象实例是否是File类型
var file1 Writer=...
if file5,ok:=file1.(File);ok{  
  ...
}
```

### 5.5接口类型查询

在 Go 中，要判断传递给接口值的变量类型，可以在使用 type switch 得到。(type)只能在 switch 中使用。

```go
// 另一个实现了 I 接口的 R 类型
type R struct { i int }
func (p *R) Get() int { return p.i }
func (p *R) Put(v int) { p.i = v }
 
func f(p I) {
    switch t := p.(type) { // 判断传递给 p 的实际类型
        case *S: // 指向 S 的指针类型
        case *R: // 指向 R 的指针类型
        case S:  // S 类型
        case R:  // R 类型
        default: //实现了 I 接口的其他类型
    }
}
```

### 5.6接口组合

```go
//接口组合类似类型组合，只不过只包含方法，不包含成员变量
type ReadWriter interface{  //接口组合，避免代码重复
  Reader      //接口Reader
  Writer      //接口Writer
}
```

### 5.7Any类型[空接口]

每种类型都能匹配到空接口：interface{}。空接口类型对方法没有任何约束（因为没有方法），它能包含任意类型，也可以实现到其他接口类型的转换。如果传递给该接口的类型变量实现了转换后的接口则可以正常运行，否则出现运行时错误。

```go
//interface{}即为可以指向任何对象的Any类型，类似Java中的Object类
var v1 interface{}=struct{X int}{1}
var v2 interface{}="abc"
 
func DoSomething(v interface{}) {   //该函数可以接收任何类型的参数，因为任何类型都实现了空接口
   // ...
}
```

### 5.8接口的代码示例

```go
//接口animal
type Animal interface {
    Speak() string
}
//Dog类实现animal接口
type Dog struct {
}
 
func (d Dog) Speak() string {
    return "Woof!"
}
//Cat类实现animal接口
type Cat struct {
}
 
func (c Cat) Speak() string {
    return "Meow!"
}
//Llama实现animal接口
type Llama struct {
}
 
func (l Llama) Speak() string {
    return "?????"
}
//JavaProgrammer实现animal接口
type JavaProgrammer struct {
}
 
func (j JavaProgrammer) Speak() string {
    return "Design patterns!"
}
//主函数
func main() {
    animals := []Animal{Dog{}, Cat{}, Llama{}, JavaProgrammer{}}  //利用接口实现多态
    for _, animal := range animals {
        fmt.Println(animal.Speak())  //打印不同实现该接口的类的方法返回值
    }
}
```