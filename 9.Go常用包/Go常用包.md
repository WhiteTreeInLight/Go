# Go常用包

## Go语言中的包

Go语言中包的本质：文件夹，不同的文件夹可以存放不同功能代码。

Go语言的源码复用就是建立在包机制上的

### main包

1. main函数所在的包，必须是main包，代表程序的入口
2. mian包中引入其他包，import "fmt"
3. main是程序的入口，其他包不能使用，尽量使用网址作为包名

### package包

src：保存我们项目的源码路径，所有的代码都以包的形式放在这里

package声明包在哪里，不需要和文件夹名一致，但是我们尽量使用文件夹的名字

1、**一个目录下所有的go文件的package必须同名**

2、package可以和文件夹不同名，但是不建议

3、同一个包下的所有go文件的函数，可以直接调用

4、导入包的时候要从src目录下开始导入

```go
package service

import "lesson10/pojo/user"

func userInfo() {
    user.UpdateUserInfo()
}
```

5、对于外包中的函数，我们需要使用包名.函数名来使用

```go
package user

import "fmt"

// 如果当前包的函数要给外面的函数使用，必须要函数名大写
func UpdateUserInfo() {
    fmt.Println("更新成功")
}
```

```go
package service

import "lesson10/pojo/user"

func userInfo() {
    user.UpdateUserInfo()
}
```

其他的包导入方式

1、可以批量导入包

```go
import(
	// 系统自带的包
	""
	// 自己写的包
	""
	// 网上下载的包-github
	""
)
```

2、相对路径导入包	../上一级

```go
import "../xxx"	//不建议这么使用
```

3、如果包名冲突解决方法

```go
package controllor

//import "math/rand"
//随机数生成

import (
    "math/big"
    R "math/cmplx" // 给包起别名
    // 简便模式
    . "math/bits"  //可以直接调用该包下的函数，不需要通过包名
    _ "math/big"   //匿名导入，只会执行这个包下的init
)

func test() {
    R.Acosh()
    big.ToNegativeInf
}
```

### init函数（重点）

Go语言中，除了main函数入口之外，还有一个十分特别的函数init()函数

**init：初始化，在main函数之前执行**

init：设置一些包...初始化一些全局变量...建立一些第三方的连接（数据库连接）、注册、检查、修复程序状态

init函数可以有多个（同名）

#### init函数的执行顺序问题

```go
package main

// 匿名导入包，会执行所有go文件下的init函数，单个init被多个地方导入，只会执行一次init
import (
    "fmt"
    // 1、先执行导入包的init函数，单个go文件中是顺序执行的
    // 所有go中的init函数执行完毕之后，才会到 main 包
    // 2、如果导入了多个匿名包，init是按照包的 文件排列顺序 自上而下执行
    // 3、子啊同一个包下的go文件如果有多个，都有init的情况下，按照 文件排放的顺序 来执行对应的init函数
    _ "lesson10/test"
    _ "lesson10/zzz"
)

func init() {
    fmt.Println("main-init")
}

func main() {
    // init函数不需要传入参数，也没有返回值，任何地方都不能被调用
    //fmt.Println("main-func")
}
```

注意：只有匿名导入才会执行这个包下的init函数，无法使用包内的其他函数

## strings包

```go
package stringsdemo

import (
    "fmt"
    "strings"
)

// 字符串常用操作包
func Test() {
    str := "Elm White"
    // 1、字符串是不能修改的
    fmt.Println(str[0])
    // strings下的常用方法
    // 1、判断某个字符是否包含了指定的内容
    fmt.Println(strings.Contains(str, "m"))
    fmt.Println(strings.Contains(str, "z"))

    // 2、判断某个字符串是否包含了多个字符串中的某一个，
    fmt.Println(strings.ContainsAny(str, "mz"))

    // 3、统计这个字符在指定字符串中出现的数量 Count()    计数
    fmt.Println(strings.Count(str, "e"))

    // 4、判断用什么开头的
    fileName := "20240523.mp4"
    if strings.HasPrefix(fileName, "2024") {
       fmt.Println("找到了2024开头的文件:", fileName)
    }
    // 5、判断用什么结尾的
    if strings.HasSuffix(fileName, "mp4") {
       fmt.Println("找到了MP4结尾的文件:", fileName)
    }
    // 6、寻找这个字符串第一次出现的位置
    fmt.Println(strings.Index(str, "e"))
    // 7、寻找这个字符串最后一次出现的位置
    fmt.Println(strings.LastIndex(str, "i"))
    // 8、拼接字符串，数组或者切片拼接，前端给了外面多个参数，保存为一个字符串
    str2 := []string{"a", "b", "c", "d", "e"}
    str3 := strings.Join(str2, "-")
    fmt.Println(str3)
    // 9、通过某个格式拆分字符串
    fmt.Println(strings.Split(str3, "-"))
    // 10、大小写ToUpper() ToLower()
    fmt.Println(strings.ToUpper(str))
    fmt.Println(strings.ToLower(str))
    // 11、替换字符串    -1改所有  1改一个   2改两个
    fmt.Println(strings.Replace(str, "E", "e", -1))
    // 12、截取某个字符串
    fmt.Println(str[0:5])
}
```

## strconv包

字符串转换

```go
package main

import (
    "fmt"
    "strconv"
)

// string convert = strconv

func main() {
    // bool
    s1 := "true"
    // 字符串转bool（解析：parse）、bool转字符串（格式化：format）
    // 源码：func ParseBool(str string) (bool, error)
    b1, err := strconv.ParseBool(s1)
    if err != nil {
       fmt.Println(err)
       return
    }
    fmt.Println(b1)
    fmt.Printf("%T\n", b1)
    // bool转字符串
    // 源码：func FormatBool(b bool) string
    s2 := strconv.FormatBool(b1)
    fmt.Println(s2)
    fmt.Printf("%T\n", s2)

    // 字符串转数字（各种进制）
    s3 := "010"
    // 参数：string、进制、大小
    // 源码：func ParseInt(s string, base int, bitSize int) (i int64, err error)
    // 转化为2进制
    num1, err := strconv.ParseInt(s3, 2, 64)
    if err != nil {
       fmt.Println(err)
       return
    }
    fmt.Println(num1)
    fmt.Printf("%T\n", num1)
    // 从10进制转化为2进制
    s4 := strconv.FormatInt(num1, 2)
    fmt.Println(s4)
    fmt.Printf("%T\n", s4)

    // 10进制转换字符串的简便方法  atoi   itoa
    // Ascii to Int
    // 源码：func Atoi(s string) (int, error)
    i, err := strconv.Atoi("-20")
    if err != nil {
       fmt.Println(err)
       return
    }
    fmt.Println(i)
    fmt.Printf("%T\n", i)
    // Int to Ascii
    // 源码：func Itoa(i int) string
    s5 := strconv.Itoa(10)
    fmt.Println(s5)
    fmt.Printf("%T\n", s5)
}
```

## time包

time包

- 获取当前时间
- 格式化时间

```go
package main

import (
    "fmt"
    "time"
)

// time
func main() {
    // 输出时间
    time1()
    // 将字符串解析为时间
    timeStr := "2024-05-24 18:16:56"
    time2(timeStr)
    // 时间戳
    time3()
}

// 获取当前时间
func time1() {
    // 返回值为 Time 类型：常量：日月年时分秒  周日-周六
    // 使用time中的方法可以获取上述的常量
    now := time.Now()
    year := time.Now().Year()
    month := time.Now().Month()
    day := time.Now().Day()
    hour := time.Now().Hour()
    minute := time.Now().Minute()
    second := time.Now().Second()
    // Printf  整数补位--02如果不足两位，左侧用0补齐输出
    fmt.Printf("%d-%02d-%02d %02d:%02d:%02d\n", year, month, day, hour, minute, second)

    // 其他语言：
    // 时间格式化：2024-05-24 18:06:12
    // 格式化模板：yyyy-MM-dd HH:mm:ss
    // Go语言：
    // Go语言诞生的时间作为格式化模板：2006年1月2日下午3点4分5秒
    // Go语言格式化时间的代码：2006-01-02 15:04:05    (记忆方式：2006 12345)
    fmt.Println(now.Format("2006-01-02 15:04:05")) // 24小时制
    fmt.Println(now.Format("2006/01/02 15:04:05"))
    fmt.Println(now.Format("2006-01-02 03:04:05 PM")) // 12小时制
    fmt.Println(now.Format("2006/01/02 03:04:05 PM"))
}

// 将字符串转化为Time对象（获取从网页传递的时间字符串，需要转化为Time才能在代码中使用）
func time2(timeStr string) {
    // 参数：转换的格式，转换的字符串
    // 源码：func Parse(layout, value string) (Time, error)
    strToTime, err := time.Parse("2006-01-02 15:04:05", timeStr)
    if err != nil {
       fmt.Println(err)
       return
    }
    fmt.Println(strToTime)

    // 获取时间的时区
    // 中国时区位于 Asia/Shanghai
    now := time.Now()
    fmt.Println(now.Location().String())
    location, err := time.LoadLocation("Asia/Shanghai")
    if err != nil {
       fmt.Println(err)
    }
    fmt.Println(location.String())

    // 加上时区的解析方式
    timeObj, err := time.ParseInLocation("2006-01-02 15:04:05", timeStr, location)
    if err != nil {
       fmt.Println(err)
       return
    }
    fmt.Println(timeObj)
}

// 时间戳：更多时候和随机数进行结合
func time3() {
    // 时间戳 Unix 1970年1月1日上午8点到当下的一个毫秒数
    // Unix 时间戳是不会重复的
    now := time.Now()
    // 毫秒的时间戳
    unix := now.Unix()
    fmt.Println(unix)
    // 纳秒的时间戳
    unixNano := now.UnixNano()
    fmt.Println(unixNano)

    // 通过Unix时间戳转化为Time对象
    timeObj := time.Unix(unix, 0)
    fmt.Println(timeObj)
}
```

### 随机数

```go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

// 随机数：math/random

func main() {
    // 获取一个随机数
    num1 := rand.Int()
    fmt.Println(num1)
    // 随机数需要一个随机的种子，如果种子一样，那么结果一致
    // Intn中，n是一个范围
    num2 := rand.Intn(10)
    fmt.Println(num2)
    // 所以需要一个随时都在发生变化的量 时间戳
    timeStamp1 := time.Now().Unix()
    // 设置随机数种子，使用时间戳
    rand.Seed(timeStamp1)
    for i := 0; i < 5; i++ {
       // 20 - 29
       // 种子只需要设置一次即可
       num3 := rand.Intn(10) + 20
       fmt.Println(num3)
    }
}
```

### 定时器-时间操作

> 时间间隔常量：Duration

time.Duration是time包定义的一个类型，它代表两个时间点之间经过的时间

以纳秒为单位，可表示的最长时间段大约290年。

time包中定义的时间间隔类型的常量如下

```go
const (
	Nanosecond  Duration = 1
	Microsecond          = 1000 * Nanosecond
	Millisecond          = 1000 * Microsecond
	Second               = 1000 * Millisecond
	Minute               = 60 * Second
	Hour                 = 60 * Minute
)
```

time.Duration表示1纳秒，time.Second表示1秒

```go
package main

import (
    "fmt"
    "time"
)

// 定时器 - 本质是一个通道chan
func main() {
    // 定时器：每隔 xx 秒执行一次
    ticker := time.Tick(time.Second) // 每一秒都会触发
    // ticker 是一个通道，其余的关于定时器放到chan中了解
    for t := range ticker {
       fmt.Println(t)
    }
}
```

> 时间判断

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    // 加   减  比较（在xxx之前，在xxx之后）
    now := time.Now()
    fmt.Println(now)
    // 加
    later := now.Add(time.Hour)
    fmt.Println(later)
    // 减
    // 返回两个时间的差值
    subNow := now.Sub(later)
    fmt.Println(subNow)

    // 比较时间
    fmt.Println(now.Equal(later))
    fmt.Println(now.Before(later))
    fmt.Println(now.After(later))
}

// 定时器 - 本质是一个通道chan
func d1() {
    // 定时器：每隔 xx 秒执行一次
    ticker := time.Tick(time.Second) // 每一秒都会触发
    // ticker 是一个通道，其余的关于定时器放到chan中了解
    for t := range ticker {
       fmt.Println(t)
    }
}
```

## IO包

### 获取文件信息

计算机中的文件是存储在外部介质（通常是磁盘）上的数据集合，文件分为**文本文件**和**二进制文件**。

file类是在os操作系统包中的，封装了底层的文件描述符和相关信息，同时封装了Read和Write的实现

- 获取文件信息
- 创建目录、创建文件
- IO读写
- 文件复制
- 断点续传
- bufio
- 遍历文件夹

FileInfo接口，os.Stat()获取文件信息

```go
package main

import (
    "fmt"
    "os"
)

// file
/*
type FileInfo interface {
    Name() string       // base name of the file
    Size() int64        // length in bytes for regular files; system-dependent for others
    Mode() FileMode     // file mode bits
    ModTime() time.Time // modification time
    IsDir() bool        // abbreviation for Mode().IsDir()
    Sys() any           // underlying data source (can return nil)
}
*/
func main() {
    // 获取某个文件的状态
    fileInfo, err := os.Stat("F:\\code\\GoWork\\src\\lesson11\\demo01.go")
    if err != nil {
       fmt.Println(err)
       return
    }
    fmt.Println(fileInfo.Name())
    fmt.Println(fileInfo.Size())
    fmt.Println(fileInfo.ModTime())
    fmt.Println(fileInfo.IsDir())
    fmt.Println(fileInfo.Mode())
    fmt.Println(fileInfo.Sys())
}
```

### 创建文件、目录

电脑的可视化程序，底层其实都是命令

路径：

- 相对路径
  - 相对当前目录的路径
  - ./ 当前目录
  - ../ 上一级目录
- 绝对路径
  - 从盘符开始的路径

#### 创建目录

```go
package main

import (
    "fmt"
    "os"
)

// 创建目录
func main() {
    // 打开一个文件夹（存在就打开，不存在就创建）
    // 源码：func Mkdir(name string, perm FileMode) error
    // 参数：目录路径，目录权限
    err := os.Mkdir("F:\\code\\GoWork\\src\\lesson11\\file1", os.ModePerm)
    if err != nil {
       // Cannot create a file when that file already exists. 文件夹已存在
       fmt.Println(err)
       //return
    } else {
       fmt.Println("单个文件夹创建完毕")
    }

    // 创建层级文件夹
    err1 := os.MkdirAll("F:\\code\\GoWork\\src\\lesson11\\file2\\aa\\bb\\cc\\dd", os.ModePerm)
    if err1 != nil {
       fmt.Println(err1)
       //return
    } else {
       fmt.Println("层级文件夹创建完毕")
    }

    // 删除文件夹
    // 源码：func Remove(name string) error
    // remove 只能删除单个空文件夹
    re_err1 := os.Remove("F:\\code\\GoWork\\src\\lesson11\\file1")
    if re_err1 != nil {
       fmt.Println(re_err1)
    } else {
       fmt.Println("单个文件夹删除成功")
    }
    // 删除目录下的所有东西，包括目录
    re_err2 := os.RemoveAll("F:\\code\\GoWork\\src\\lesson11\\file2")
    if re_err2 != nil {
       fmt.Println(re_err2)
    } else {
       fmt.Println("层级文件夹删除完毕")
    }
}
```

#### 创建文件

文件就是一个类（结构体）---> 对象（方法和属性）

os.Create()，若存在就是打开，返回的这个file对象，若不存在，那么就创建和打开

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    // 创建文件    Create
    // 源码：func Create(name string) (*File, error)
    //file, err := os.Create("a.go")
    //if err != nil {
    // fmt.Println(err)
    //} else {
    // fmt.Println(file)
    //}
    // 删除文件
    // 源码：func Remove(name string) error
    err1 := os.Remove("a.go")
    if err1 != nil {
       fmt.Println(err1)
    } else {
       fmt.Println("文件删除完毕")
    }

}
```

### 读文件

#### 建立连接

```go
package main

import (
    "fmt"
    "os"
)

// IO读取文件
func main() {
    // 找到文件的对象 Create or Open
    // 源码：func Open(name string) (*File, error)
    file1, err1 := os.Open("F:\\code\\GoWork\\src\\lesson11\\小说.txt")
    if err1 != nil {
       fmt.Println(err1)
    }
    fmt.Println(file1)
    // 打开文件的时候选定权限：可读可写的方式打开
    // OpenFile(文件名，打开方式：可读、可写...，FileMode：权限)
    // func OpenFile(name string, flag int, perm FileMode) (*File, error)
    /*
       const (
          // Exactly one of O_RDONLY, O_WRONLY, or O_RDWR must be specified.
          O_RDONLY int = syscall.O_RDONLY // open the file read-only.
          O_WRONLY int = syscall.O_WRONLY // open the file write-only.
          O_RDWR   int = syscall.O_RDWR   // open the file read-write.
          // The remaining values may be or'ed in to control behavior.
          O_APPEND int = syscall.O_APPEND // append data to the file when writing.
          O_CREATE int = syscall.O_CREAT  // create a new file if none exists.
          O_EXCL   int = syscall.O_EXCL   // used with O_CREATE, file must not exist.
          O_SYNC   int = syscall.O_SYNC   // open for synchronous I/O.
          O_TRUNC  int = syscall.O_TRUNC  // truncate regular writable file when opened.
       )
    */
    file2, err2 := os.OpenFile("F:\\code\\GoWork\\src\\lesson11\\小说.txt",
       os.O_RDONLY|os.O_WRONLY,
       os.ModePerm)
    if err2 != nil {
       fmt.Println(err2)
    }
    fmt.Println(file2)
    
}
```

#### 读取文件数据

file.Read([]byte)，将file中的数据读取到[]byte中

n，err：n代表读取到的行数，err代表错误，一旦出现EOF错误，就代表文件读取完毕了

一直调用Read就代表读取数据的光标一直往后移动

```go
package main

import (
    "fmt"
    "os"
)

// 读取文件数据
func main() {
    fileInfo, err := os.Stat("F:\\code\\GoWork\\src\\lesson11\\小说.txt")
    if err != nil {
       fmt.Println(err)
    }
    fmt.Println(fileInfo)
    // 建立连接
    file1, err1 := os.Open("F:\\code\\GoWork\\src\\lesson11\\小说.txt")
    if err1 != nil {
       fmt.Println(err1)
    }
    // 关闭连接
    defer file1.Close()
    fmt.Println(file1)

    // 1、创建一个容器（二进制文本 --> 01001010 --> 读取流到一个容器 --> 读取容器内的数据）
    bs := make([]byte, 2, 1024) // 缓冲区，可以接收我们读取的数据，一般我们要把缓冲区设置大一些，不然可能出现数据没有读完全的情况
    // 2、读取到缓冲区中，一个汉字占3个位置
    // 源码：func (f *File) Read(b []byte) (n int, err error)
    n, err_r := file1.Read(bs)
    if err_r != nil {
       fmt.Println(err_r)
    }
    fmt.Println(n)
    fmt.Println(string(bs))

    n, err_r = file1.Read(bs)
    fmt.Println(err_r)
    fmt.Println(n)
    fmt.Println(string(bs))

    n, err_r = file1.Read(bs)
    fmt.Println(err_r)
    fmt.Println(n)
    fmt.Println(string(bs))

    n, err_r = file1.Read(bs)
    fmt.Println(err_r) // EOF，读取到了文件末尾，就会返回EOF
    fmt.Println(n)
    fmt.Println(string(bs))
}
```

### 写文件（权限）

建立连接（设置权限）

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    // 建立连接
    fileName := "小说.txt"
    // 权限：如果我们要向一个文件中追加内容，O_APPEND，如果没有，就是从头开始覆盖
    file, err1 := os.OpenFile(fileName, os.O_RDONLY|os.O_WRONLY|os.O_APPEND, os.ModePerm)
    if err1 != nil {
       fmt.Println(err1)
    }
    defer file.Close()
    // 操作，使用byte写入
    bs := []byte{65, 66, 67, 68, 69} // A B C D E
    // 源码：func (f *File) Write(b []byte) (n int, err error)
    n1, err2 := file.Write(bs)
    if err2 != nil {
       fmt.Println(err2)
    }
    fmt.Println(n1)
    // 使用string写入
    n3, err3 := file.WriteString("abcdefghijklmnop")
    if err3 != nil {
       fmt.Println(err3)
    }
    fmt.Println(n3)

}
```

### 文件复制

```go
package utils

import (
    "fmt"
    "io"
    "os"
)

// Copy 方法需要参数：source 源文件, destination 目标文件
func Copy(source, destination string, bufferSize int) {
    // 连接源文件
    sourceFile, source_err := os.Open(source)
    if source_err != nil {
       fmt.Println("Open Error:", source_err)
    }
    defer sourceFile.Close()
    // 连接目的文件，O_CREATE如果不存在，那么就创建一个目的文件
    destinationFile, des_err := os.OpenFile(destination, os.O_WRONLY|os.O_RDONLY|os.O_APPEND|os.O_CREATE, os.ModePerm)
    if des_err != nil {
       fmt.Println("Open Error:", des_err)
    }
    defer destinationFile.Close()
    // 创建缓冲区
    buffer := make([]byte, bufferSize)
    // 通过for不断读取缓冲区中的数据
    for true {
       // 从缓冲区读取文件
       n, readSourceErr := sourceFile.Read(buffer)
       if n == 0 || readSourceErr == io.EOF {
          fmt.Println("源文件读取完毕，复制完成")
          break
       } else if readSourceErr != nil {
          fmt.Println("Read Error:", readSourceErr)
          return
       }
       // 向目的文件写入
       _, writeDesErr := destinationFile.Write(buffer[:n])
       if writeDesErr != nil {
          fmt.Println("Write Error:", writeDesErr)
          return
       }
    }
    fmt.Println("复制完成")
}
```

```go
package main

import "lesson11/utils"

func main() {
    sourFile := "C:\\Users\\cyqw\\Pictures\\mmexport1693489761436.jpg"
    destinationFile := "F:\\code\\GoWork\\src\\lesson11\\tupian.jpg"
    utils.Copy(sourFile, destinationFile, 1024)

}
```

> 系统给出的Copy方法：io.Copy()

```go
// 调用系统的方法
func Copy2(source, destination string) {
    // 连接源文件
    sourceFile, source_err := os.Open(source)
    if source_err != nil {
       fmt.Println("Open Error:", source_err)
    }
    defer sourceFile.Close()
    // 连接目的文件，O_CREATE如果不存在，那么就创建一个目的文件
    destinationFile, des_err := os.OpenFile(destination, os.O_WRONLY|os.O_RDONLY|os.O_APPEND|os.O_CREATE, os.ModePerm)
    if des_err != nil {
       fmt.Println("Open Error:", des_err)
    }
    defer destinationFile.Close()

    // 具体的实现
    written, err := io.Copy(destinationFile, sourceFile)
    if err != nil {
       fmt.Println("Copy Error:", err)
    }
    fmt.Println("文件的字节大小：", written)
}
```

> 简便使用方法

```go
func Copy3(source, destination string) {
    fileBuf, err := os.ReadFile(source)
    if err != nil {
       fmt.Println("Read Error:", err)
    }
    os.WriteFile(destination, fileBuf, 0777)

}
```

### Seeker接口

可以设置光标的位置，根据位置来读写文件

```go
type Seeker interface {
    // 1、offset偏移量
    // 2、whence如何设置，当前光标的位置
	Seek(offset int64, whence int) (int64, error)
}
// 如何让光标在第三个位置
// 1、找到当前光标在哪里
//	a)文件的头部	0
//	b)文件的尾部	end
//	c)任意地方，相对位置

// Seek whence values.
const (
	SeekStart   = 0 // seek relative to the origin of the file
	SeekCurrent = 1 // seek relative to the current offset
	SeekEnd     = 2 // seek relative to the end
)
```

> 关于Seek的使用

```go
package main

import (
    "fmt"
    "io"
    "os"
)

func main() {
    // 读取文件
    file1, err1 := os.OpenFile("F:\\code\\GoWork\\src\\lesson11\\小说.txt", os.O_RDONLY|os.O_APPEND, 0666)
    if err1 != nil {
       fmt.Println("err1:", err1)
    }
    // defer close
    defer file1.Close()
    // 测试Seek  相对于开始位置
    //file1.Seek(2, io.SeekStart)
    // 相对于末尾位置
    file1.Seek(-2, io.SeekEnd)
    // 相对于当前位置
    file1.Seek(1, io.SeekCurrent)
    // 每次只读一个
    buf := make([]byte, 1)
    file1.Read(buf)
    fmt.Println(string(buf))

    // 在结尾追加内容
    file1.Seek(0, io.SeekEnd)
    file1.WriteString("test")

}
```

### 断点续传

思考几个问题：
1、如果你要传输的文件很大，70G，是否有方法可以缩短耗时？

- 将文件进行拆分
- 同时多个进程进行下载

2、如果在文件传递过程中，程序被迫中断（断电、断网、内存满了...），下次重启之后，文件是否还需要从头再进行传输？

- 希望能够继续上传或者下载

3、传递文件的时候，支持暂停和恢复吗？假设这两个操作分布在重启前后



思路：

1、需要记住上次传递了多少数据，temp.txt=>记录数据

2、如果被暂停或者中止了，我们就可以读取这个temp.txt的记录，恢复上传

3、删除temp.txt

```go
package main

import (
    "fmt"
    "io"
    "os"
    "strconv"
    "time"
)

// 断点续传
func main() {
    // 传输源文件地址
    srcFile := "F:\\code\\GoWork\\src\\lesson11\\client\\tupian.jpg"

    // 传输的目标地址
    destFile := "F:\\code\\GoWork\\src\\lesson11\\server\\tupian-server.jpg"

    // 临时的记录文件
    tempFile := "F:\\code\\GoWork\\src\\lesson11\\temp.txt"

    // 创建对应的file对象，连接起来
    // src
    src_file, src_err := os.Open(srcFile)
    if src_err != nil {
       fmt.Println(src_err)
       return
    }
    defer src_file.Close()
    // dest
    dest_file, dest_err := os.OpenFile(destFile, os.O_CREATE|os.O_RDWR, os.ModePerm)
    if dest_err != nil {
       fmt.Println(dest_err)
       return
    }
    defer dest_file.Close()
    // temp
    temp_file, temp_err := os.OpenFile(tempFile, os.O_CREATE|os.O_RDWR, os.ModePerm)
    if temp_err != nil {
       fmt.Println("打开temp.txt出错：", temp_err)
       return
    }
    // temp 这个临时文件要在确认上传完毕之后来关闭删除

    // 1、读取temp.txt
    temp_file.Seek(0, io.SeekStart)
    // 设置缓冲区
    buf := make([]byte, 1024, 1024)
    n, read_temp_err := temp_file.Read(buf)
    // 这边需要处理一下刚开始传输的时候temp为空，无数据的情况
    if read_temp_err == io.EOF {
       fmt.Println("temp.txt为空", read_temp_err)
    } else if read_temp_err != nil {
       fmt.Println("读取temp.txt出错", read_temp_err)
       return
    }
    // 2、转化成string
    countStr := string(buf[:n])
    // 转化为数字
    count, _ := strconv.ParseInt(countStr, 10, 64)
    fmt.Println("temp.txt中记录的值为：", count)

    // 3、设置读写的偏移量
    src_file.Seek(count, io.SeekStart)
    dest_file.Seek(count, io.SeekStart)

    // 4、开始读写（复制 or 上传）
    bufData := make([]byte, 1024, 1024)
    // 5、需要记录读取了多少个字符
    total := int(count)
    for true {
       // 读取数据
       upload_count, upload_err := src_file.Read(bufData)
       if upload_err == io.EOF {
          fmt.Println("文件传输完毕")
          // 关闭临时文件
          temp_file.Close()
          // 删除临时文件
          os.Remove(tempFile)
          break
       } else if upload_err != nil {
          fmt.Println("源文件读取失败", upload_err)
          return
       }
       // 向目标文件写入数据
       write_count, write_err := dest_file.Write(bufData[:upload_count])
       if write_err != nil {
          fmt.Println("向目标文件写入出错", write_err)
          return
       }
       // 将写入的数据加入我们的total，total就是传输的进度
       total = total + write_count
       // temp.txt存放临时记录数据
       temp_file.Seek(0, io.SeekStart) // 将光标重置到开头
       temp_file.WriteString(strconv.Itoa(total))
       // 加个延时来测试断点重传
       time.Sleep(time.Millisecond)
    }

}
```

### 遍历文件夹

```go
package main

import (
    "fmt"
    "log"
    "os"
)

// 遍历文件夹 - 递归
// 1、读取当前文件夹下的所有文件
// 2、如果是文件夹，进入文件夹，继续读取里面的所有文件
// 3、设置一些结构化代码
func main() {
    // 获取目录
    dir := "F:\\code\\GoWork\\src\\lesson11"
    tree(dir, 0)
}

func tree(dir string, level int) {
    // 编写层级
    tabString := "|--"
    for i := 0; i < level; i++ {
       tabString = "|     " + tabString
    }
    // 获取目录
    // 源码：func ReadDir(name string) ([]DirEntry, error)    返回多个文件信息
    fileInfos, err := os.ReadDir(dir)
    if err != nil {
       // 日常调试测试使用fmt，而工作或项目中经常使用log
       log.Println(err)
    }
    // 遍历出来所有文件之后，获取里面单个的文件
    for _, file := range fileInfos {
       // 文件夹中的路径展示
       //fileName := dir + "\\" + file.Name()
       fileName := file.Name()
       fmt.Println(tabString + fileName)
       // 如果是文件夹，那么就要再次遍历
       if file.IsDir() {
          tree(fileName, level+1)
       }
       //fmt.Println(fileName)
    }

}
```

### bufio

Go语言自带的IO操作包。bufio，使用这个包可以大幅提升文件的读写效率

buf：缓冲区

io操作效率本身是还可以的，但是频繁访问本地磁盘文件（效率低）

所以说bufio，提供了一个缓冲区，读和写都先在缓冲区中，最后再一次性读取或者写入到文件里，降低访问本地磁盘的次数

#### bufio读取

```go
package main

import (
    "bufio"
    "fmt"
    "log"
    "os"
)

// bufio 的应用
func main() {
    file, err := os.Open("F:\\code\\GoWork\\src\\lesson11\\demo01.go")
    if err != nil {
       log.Println(err)
       return
    }
    defer file.Close()
    // 读取文件
    // 创建一个bufio包下的一个Reader对象
    reader := bufio.NewReader(file)
    buf := make([]byte, 1024, 1024)
    n, read_err := reader.Read(buf)
    if read_err != nil {
       log.Fatal(read_err)
       return
    }
    fmt.Println("读取到的字节数：", n)
    fmt.Println("读取到的内容：", string(buf[:n]))

    // 自己的read方法
    // 读取一行
    s, _, _ := reader.ReadLine()
    fmt.Println(string(s))

    // 读取键盘的输入
    // 键盘的输入实际上也是流
    inputReader := bufio.NewReader(os.Stdin)
    // 源码：func (b *Reader) ReadString(delim byte) (string, error)
    // delim：指的是到哪里结束读取
    readString, _ := inputReader.ReadString('\n')
    fmt.Println("键盘输入的信息：", readString)

}
```

#### bufio写入

```go
package main

import (
    "bufio"
    "fmt"
    "os"
)

// 写入
func main() {
    // bufio
    file, err := os.OpenFile("a.txt",
       os.O_RDWR|os.O_CREATE, os.ModePerm)
    if err != nil {
       fmt.Println(err)
       return
    }
    defer file.Close()
    fileWriter := bufio.NewWriter(file)
    // 源码：func (b *Writer) WriteString(s string) (int, error)

    n, write_err := fileWriter.WriteString("just a test")
    if write_err != nil {
       fmt.Println(write_err)
       return
    }
    // 到这边是看不见写入的字符的，这些内容写入到缓冲区当中了，需要我们手动刷新文件，才会将缓冲区中的内容写入文件
    fmt.Println("写入的字符个数：", n)
    // 刷新文件，写入数据
    fileWriter.Flush()

}
```