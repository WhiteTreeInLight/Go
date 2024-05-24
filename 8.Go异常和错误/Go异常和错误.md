# Go异常和错误

## 错误与异常的区别

**错误：指的是程序中预期会发生的结果，属于预料之中的**

例如：打开一个文件，提示文件正在被占用，这就是预料之中可能出现的结果

**异常：指的是不该出现问题的地方出现了问题，属于预料之外的**

例如：调用一个对象，本来不应该是nil类型的对象却返回了nil，这就是预料之外的结果

**错误是业务的一部分，而异常不是**

Go语言开发过程中遇到最多的代码就是Error

需要将所有的错误情况都考虑到，然后写入到代码中

## 错误

**鼓励工程师在代码中显式的检查错误，而非忽略错误**

好处就是避免漏掉本来应该处理的错误，但是带来一个弊端，让代码更加繁琐了

```go
package main

import (
    "fmt"
    "os"
)

// 错误是开发中必须要思考的问题
// 某些系统错误，文件被占用，网络有延迟
// 人为错误：核心就是一些不正常的用户会怎么来给你传递参数

func main() {
    // 打开一个文件 os 系统包
    // 所有可以用用鼠标和键盘能执行的事件都可以用程序实现
    // func Open(name string) (*File, error)
    file, err := os.Open("aaa.txt")
    // 在开发中，我们需要思考这个错误的类型
    // 1、文件不存在 err
    // 2、文件被占用 err
    // 3、文件被损坏 err
    // 调用方法后，出现错误，需要进行解决
    if err != nil {
       fmt.Println(err)
       // open aaa.txt: The system cannot find the file specified.
       return
    }

    fmt.Println(file.Name())
}

// 在实际工程项目中
// 我们希望通过程序的错误信息快速定位问题，但是又不喜欢错误处理代码写的冗余又啰嗦
// Go语言没有提供像Java的try...catch异常处理方式，而是通过函数返回值逐层向上抛
// 如果没有人处理这个错误，程序就会终止 panic
```

### 定义一个错误

```go
package main

import (
	"errors"
	"fmt"
)

// 自己定义一个错误
// 1、errors.New(string)
// 2、fmt.Errorf()
// 都会返回一个error对象，本身也是一个类型
func main() {
	age_err := setAge(-1)
	if age_err != nil {
		fmt.Println(age_err)
	}
	fmt.Printf("%T\n", age_err) // *errors.errorString

	// 方式二
	errInfo1 := fmt.Errorf("我是一个错误信息：%d\n", 500)
	errInfo2 := fmt.Errorf("我是一个错误信息：%d\n", 404)
	if errInfo1 != nil {
		fmt.Println(errInfo1)
	}
	if errInfo2 != nil {
		fmt.Println(errInfo2)
	}
}

// 设置年龄的函数，一定需要处理一些非正常用户的请求
// 返回值为error类型
func setAge(age int) error {
	if age < 0 {
		// 给出一个默认值
		age = 3
		// 通过errors包抛出一个错误
		return errors.New("年龄不合法")
	}
	// 程序正常的结果，给这个用户赋值
	fmt.Println("年龄设置成功，age=", age)
	return nil
}
```

### 自定义一个错误类型

```go
package main

import "fmt"

type ElmError struct {
    msg  string
    code int
}

// Error() string
func (e *ElmError) Error() string {
    // fmt.Sprintf() 返回string
    return fmt.Sprintf("错误信息: %s, 错误代码: %d", e.msg, e.code)
}

// 使用错误测试
func test(i int) (int, error) {
    if i != 0 {
       return i, &ElmError{msg: "非预期数据", code: 500}
    }
    return i, nil
}

func main() {
    i, err := test(1)
    if err != nil {
       fmt.Println(err)
       elm_err, ok := err.(*ElmError)
       if ok {
          fmt.Println(elm_err.msg)
       }
    }
    fmt.Println(i)
}
```

## 异常 panic

```go
package main

import "fmt"

// panic

func main() {
    // 什么时候会发生panic 恐慌，我们不知道什么时候会报错
    // 程序运行的时候会发生panic

    // 手动抛出panic。
    // 我们在一些能够预知到危险的情况下，可以主动抛出
    // 如果有panic发生，我们尽可能的去接收它，并进行处理
    // func panic(v any)   使用panic()程序就会终止
    panic("程序panic了")
    // 不执行print
    fmt.Println("hello world")
}
```

如果有些情况必须要处理异常就需要使用panic抛出异常，recover可以接收这个异常来处理

```go
package main

import "fmt"

// panic
// defer: 延迟函数，倒序执行，处理收尾问题
// 出现了panic之后，如果有defer语句在panic的前面，那么就会先执行所有的defer语句
func main() {
    defer fmt.Println("main---1")
    defer fmt.Println("main---2")
    fmt.Println("main---3")
    // 抛出panic之后外部函数也不会继续向下执行了
    testPanic(1)
    defer fmt.Println("main---4")
    defer fmt.Println("main---5")
}

func testPanic(num int) {
    defer fmt.Println("testPanic---1")
    defer fmt.Println("testPanic---2")
    fmt.Println("testPanic---3")
    // 如果在函数中一旦触发了panic，就会终止后面要执行的代码
    // 如果前面存在defer，就正常按照defer逻辑执行
    if num == 1 {
       panic("出现预定的异常----panic")
    }
    defer fmt.Println("testPanic---4")
    defer fmt.Println("testPanic---5")
}
```

### recover结合defer处理panic

```go
package main

import "fmt"

// panic
// defer: 延迟函数，倒序执行，处理收尾问题
// 出现了panic之后，如果有defer语句在panic的前面，那么就会先执行所有的defer语句
func main() {
    defer fmt.Println("main---1")
    defer fmt.Println("main---2")
    fmt.Println("main---3")
    // 抛出panic之后没有处理的话，外部函数也不会继续向下执行了
    // 如果在函数内部已经处理了panic，那么程序会继续向下执行
    testPanic(1)
    defer fmt.Println("main---4")
    defer fmt.Println("main---5")
}

func testPanic(num int) {
    // 出去函数的时候处理这里面可能出现的panic
    // func recover() any
    // recover返回panic传递的值
    defer func() {
       msg := recover()
       if msg != nil {
          fmt.Println("recover执行了...panic msg:", msg)
          fmt.Println("-------程序已恢复------")
       }
    }()
    defer fmt.Println("testPanic---1")
    defer fmt.Println("testPanic---2")
    fmt.Println("testPanic---3")
    // 如果在函数中一旦触发了panic，就会终止后面要执行的代码
    // 如果前面存在defer，就正常按照defer逻辑执行
    if num == 1 {
       panic("出现预定的异常----panic")
    }
    defer fmt.Println("testPanic---4")
    defer fmt.Println("testPanic---5")
}
```

执行逻辑：

1. panic触发
2. 触发panic当前函数的所有defer语句，倒序执行
3. 直到遇到了recover处理这个panic，函数结束
4. 回到main函数继续向下执行
