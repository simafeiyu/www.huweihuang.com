---
title: "[Golang] Golang文本处理"
catalog: true
date: 2017-09-01 10:50:57
type: "categories"
subtitle:
header-img: 
tags:
- Golang
catagories:
- Golang
---

## 1. JSON处理

JSON是一种轻量级的数据交换语言。

### 1.1. 解析JSON[Unmarshal(data []byte, v interface{})]

#### 1.1.1. Unmarshal源码

**/src/encoding/json/decode.go**

```go
func Unmarshal(data []byte, v interface{}) error {
    // Check for well-formedness.
    // Avoids filling out half a data structure
    // before discovering a JSON syntax error.
    var d decodeState
    err := checkValid(data, &d.scan)
    if err != nil {
        return err
    }
    d.init(data)
    return d.unmarshal(v)
}
...
func (d *decodeState) unmarshal(v interface{}) (err error) {
    defer func() {
        if r := recover(); r != nil {
            if _, ok := r.(runtime.Error); ok {
                panic(r)
            }
            err = r.(error)
        }
    }()
    rv := reflect.ValueOf(v)
    if rv.Kind() != reflect.Ptr || rv.IsNil() {
        return &InvalidUnmarshalError{reflect.TypeOf(v)}
    }
    d.scan.reset()
    // We decode rv not rv.Elem because the Unmarshaler interface
    // test must be applied at the top level of the value.
    d.value(rv)
    return d.savedError
}
```

#### 1.1.2. 解析到结构体

```go
package main
import (
    "encoding/json"
    "fmt"
)
type Server struct {
    ServerName string
    ServerIP   string
}
type Serverslice struct {
    Servers []Server
}
func main() {
    var s Serverslice
    str := `{"servers":
    [{"serverName":"Shanghai_VPN","serverIP":"127.0.0.1"},
    {"serverName":"Beijing_VPN","serverIP":"127.0.0.2"}]}`
    err:=json.Unmarshal([]byte(str), &s)
    if err!=nil{
        fmt.Println(err)
    }
    fmt.Println(s)
}
```

**说明**

JSON格式与结构体一一对应，Unmarshal方法即将JSON文本转换成结构体。只会匹配结构体中的可导出字段，即首字母大写字段（类似java的public），匹配规则如下：json的key为Foo为例

1. 先查找struct tag中含有Foo的可导出的struct字段（首字母大写）
2. 其次查找字段名为Foo的可导出字段。
3. 最后查找类似FOO或者FoO这类除首字母外，其他大小写不敏感的可导出字段。

#### 1.1.3. 解析到interface

 

### 1.2. 生成JSON[Marshal(v interface{})]

#### 1.2.1. Marshal源码

**/src/encoding/json/encode.go**

```go
func Marshal(v interface{}) ([]byte, error) {
    e := &encodeState{}
    err := e.marshal(v)
    if err != nil {
        return nil, err
    }
    return e.Bytes(), nil
}
...
func (e *encodeState) marshal(v interface{}) (err error) {
    defer func() {
        if r := recover(); r != nil {
            if _, ok := r.(runtime.Error); ok {
                panic(r)
            }
            if s, ok := r.(string); ok {
                panic(s)
            }
            err = r.(error)
        }
    }()
    e.reflectValue(reflect.ValueOf(v))
    return nil
}
```

#### 1.2.2. 使用方法

```go
package main
import (
    "encoding/json"
    "fmt"
)
type Server struct {
    ServerName string `json:"serverName,string"`
    ServerIP   string `json:"serverIP,omitempty"`
}
type Serverslice struct {
    Servers []Server `json:"servers"`
}
func main() {
    var s Serverslice
    s.Servers = append(s.Servers, Server{ServerName: "Shanghai_VPN", ServerIP: "127.0.0.1"})
    s.Servers = append(s.Servers, Server{ServerName: "Beijing_VPN", ServerIP: "127.0.02"})
    b, err := json.Marshal(s)
    if err != nil {
        fmt.Println("JSON ERR:", err)
    }
    fmt.Println(string(b))
}
```

#### 1.2.3. 说明

Marshal方法将结构体转换成json文本，匹配规则如下：

1. 如果字段的tag是“-”，那么该字段不会输出到JSON。
2. 如果tag中带有自定义名称，那么该自定义名称会出现在JSON字段名中。例如例子中的“serverName”
3. 如果tag中带有“omitempty”选项，那么如果该字段值为空，就不会输出到JSON中。
4. 如果字段类型是bool,string,int,int64等，而tag中带有“，string”选项，那么这个字段在输出到JSON的时候会把该字段对应的值转换成JSON字符串。

注意事项：

1. Marshal只有在转换成功的时候才会返回数据，JSON对象只支持string作为key，如果要编码一个map,那么必须是map[string]T这种类型。（T为任意类型）
2. Channel,complex和function不能被编码成JSON。
3. 嵌套的数据不能编码，会进入死循环。
4. 指针在编码时会输出指针指向的内容，而空指针会输出null。



## 2. 文件操作

更多文件操作见Go的os包。

### 2.1. 目录操作

- **func Mkdir(name string, perm FileMode) error**  
  创建名称为 name 的目录，权限设置是 perm，例如 0777


- **func MkdirAll(path string, perm FileMode) error**
  根据 path 创建多级子目录，例如 astaxie/test1/test2。
- **func Remove(name string) error** 
  删除名称为 name 的目录，当目录下有文件或者其他目录是会出错
- **func RemoveAll(path string) error** 
  根据 path 删除多级子目录，如果 path 是单个名称，那么该目录不删除



### 2.2. 文件操作

#### 2.2.1. 建立与打开文件

 **新建文件：** 

- **func Create(name string) (file \*File, err Error)**
  根据提供的文件名创建新的文件，返回一个文件对象，默认权限是 0666 的文件，返回的文件对象是可读写的。
- ** func NewFile(fd uintptr, name string) \*File** 
  根据文件描述符创建相应的文件，返回一个文件对象

**打开文件：** 

-  **func Open(name string) (file \*File, err Error)**
   该方法打开一个名称为 name 的文件，但是是只读方式，内部实现其实调用了 OpenFile。
-  **func OpenFile(name string, flag int, perm uint32) (file \*File, err Error)**
    打开名称为 name 的文件，flag 是打开的方式，只读、读写等，perm 是权限

#### 2.2.2. 写文件

**写文件函数：**

- **func (file \*File) Write(b []byte) (n int, err Error)** 
  写入 byte 类型的信息到文件 
- **func (file \*File) WriteAt(b []byte, off int64) (n int, err Error)**
  在指定位置开始写入 byte 类型的信息 
- **func (file \*File) WriteString(s string) (ret int, err Error)**
  写入 string 信息到文件

```go
package main
import (
    "fmt"
    "os"
)
func main() {
    userFile := "test.txt"
    fout, err := os.Create(userFile)
    defer fout.Close()
    if err != nil {
        fmt.Println(userFile, err)
        return
    }
    for i := 0; i < 10; i++ {
        fout.WriteString("Just a test!\r\n")
        fout.Write([]byte("Just a test!\r\n"))
    }
}
```

#### 2.2.3. 读文件  

**读文件函数：**

- func (file *File) Read(b []byte) (n int, err Error) 
  读取数据到 b 中 


- func (file *File) ReadAt(b []byte, off int64) (n int, err Error)
   从 off 开始读取数据到 b 中

```go
package main
import (
    "fmt"
    "os"
)
func main() {
    userFile := "text.txt"
    fl, err := os.Open(userFile)
    defer fl.Close()
    if err != nil {
        fmt.Println(userFile, err)
        return
    }
    buf := make([]byte, 1024)
    for {
        n, _ := fl.Read(buf)
        if 0 == n {
            break
        }
        os.Stdout.Write(buf[:n])
    }
}
```

#### 2.2.4. 删除文件

Go 语言里面删除文件和删除文件夹是同一个函数

- func Remove(name string) Error
  调用该函数就可以删除文件名为 name 的文件


------

## 3. 字符串处理

字符串操作涉及的标准库有**strings**和**strconv**两个包

### 3.1. 字符串操作

| 函数                                       | 说明                                       |
| ---------------------------------------- | ---------------------------------------- |
| func Contains(s, substr string) bool     | 字符串 s 中是否包含 substr，返回 bool 值             |
| func Join(a []string, sep string) string | 字符串链接，把 slice a 通过 sep 链接起来              |
| func Index(s, sep string) int            | 在字符串 s 中查找 sep 所在的位置，返回位置值，找不到返回-1       |
| func Repeat(s string, count int) string  | 重复 s 字符串 count 次，最后返回重复的字符串              |
| func Replace(s, old, new string, n int) string | 在 s 字符串中，把 old 字符串替换为 new 字符串，n 表示替换的次数，小于 0 表示全部替换 |
| func Split(s, sep string) []string       | 把 s 字符串按照 sep 分割，返回 slice                |
| func Trim(s string, cutset string) string | 在 s 字符串中去除 cutset 指定的字符串                 |
| func Fields(s string) []string           | 去除 s 字符串的空格符，并且按照空格分割返回 slice            |

### 3.2. 字符串转换

1、Append 系列函数将整数等转换为字符串后，添加到现有的字节数组中

```go
package main
import (
    "fmt"
    "strconv"
)
func main() {
    str := make([]byte, 0, 100)
    str = strconv.AppendInt(str, 4567, 10)
    str = strconv.AppendBool(str, false)
    str = strconv.AppendQuote(str, "abcdefg")
    str = strconv.AppendQuoteRune(str, '单')
    fmt.Println(string(str))
}
```

2、Format 系列函数把其他类型的转换为字符串

```go
package main
import (
    "fmt"
    "strconv"
)
func main() {
    a := strconv.FormatBool(false)
    b := strconv.FormatFloat(123.23, 'g', 12, 64)
    c := strconv.FormatInt(1234, 10)
    d := strconv.FormatUint(12345, 10)
    e := strconv.Itoa(1023)
    fmt.Println(a, b, c, d, e)
}
```

3、Parse 系列函数把字符串转换为其他类型

```go
package main
import (
    "fmt"
    "strconv"
)
func main() {
     
    a, err := strconv.ParseBool("false")
    if err != nil {
        fmt.Println(err)
    }
    b, err := strconv.ParseFloat("123.23", 64)
    if err != nil {
        fmt.Println(err)
    }
    c, err := strconv.ParseInt("1234", 10, 64)
    if err != nil {
        fmt.Println(err)
    }
    d, err := strconv.ParseUint("12345", 10, 64)
    if err != nil {
        fmt.Println(err)
    }
    e, err := strconv.Itoa("1023")
    if err != nil {
        fmt.Println(err)
    }
    fmt.Println(a, b, c, d, e)
}
```

