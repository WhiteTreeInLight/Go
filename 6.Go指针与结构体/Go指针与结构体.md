# Go指针与结构体

Go语言中的指针是非常容易学习的，Go语言中使用指针可以更简单的执行一些任务

变量是一种占位符，底层指向一个内存地址

& 取地址符，拿到一个变量 a ，&a取出这个变量的地址

## 指针

### 指针的概念

```
b的内存地址0x111111	值: 200
a是指针变量, 指向一个内存地址: 0x111111

那么指针变量a持有变量b的地址, 这个就是a指向b
```

```go
package main

import "fmt"

// 指针
func main() {
    var a int = 100
    // b存储的是a的地址, b也有自己的地址
    var b = &a

    fmt.Println("a变量的地址:", &a)  // 0xc00000e0a8
    fmt.Println("b变量储存的地址:", b) // 0xc00000e0a8
    fmt.Println("b变量的地址:", &b)  // 0xc00000a028

    // b 指向的地址, * 取出b变量所指向的地址中的值
    fmt.Println("b变量指向的地址中的值:", *b)

    *b = 200
    fmt.Println(a) // 通过指针变量b, 操控了a的值
}
```

### 指针如何使用

```go
package main

import "fmt"

// 指针的使用
/*
1. 定义指针变量
2. 为指针变量赋值  &
3. 访问指针变量中地址所指向的值   *
    * :在指针类型前面加上 * 号就是来获取指针所指向的地址的值

*/

func main() {
    // 声明普通变量
    var a int = 100
    fmt.Println("a变量的值:", a)
    fmt.Println("a变量的地址:", &a)

    // 声明指针变量, 指向a, 指针其实就是一个特殊的变量而已, 一般使用 ptr 或者 p 命名指针变量
    // 定义变量格式 var ptr *类型
    var p *int
    p = &a
    fmt.Println("p变量存储的指针地址:", p)      // a 的地址
    fmt.Println("p变量自己的指针地址:", &p)     // p变量自己的地址
    fmt.Println("p变量存储的指针地址指向的值:", *p) // a的值

    // 改变 a 的值
    a = 20
    fmt.Printf("a:%d\n", a)
    fmt.Printf("*p的值:%d\n", *p)

    // 通过指针改变 a 的值
    *p = 40
    fmt.Printf("a:%d\n", a)   // a:40
    fmt.Printf("*p:%d\n", *p) // *p:40

    // 指针的套娃, 指针指向指针
    // 指针的类型: 第一个 * 代表是指针类型, 后面的 *int 是这个指针对应的类型*(*(int))
    var ptr **int
    ptr = &p
    fmt.Printf("ptr变量存储的指针的地址:%p\n", ptr)
    fmt.Printf("ptr变量自己的地址:%p\n", &ptr)
    fmt.Printf("*ptr变量存储的地址:%p\n", *ptr)
    fmt.Printf("*ptr变量存储的地址中的值:%d\n", **ptr)
    // 修改后的值
    **ptr = 111
    fmt.Println(a)
    fmt.Println(**ptr)

}
```

### 数组指针

- 数组**指针**，首先应该是一个指针，指向一个数组

```go
package main

import "fmt"

// 数组指针
func main() {
    // 创建数组
    arr1 := [4]int{1, 2, 3, 4}
    fmt.Println("arr1:", arr1)
    fmt.Printf("arr1指向的地址:%p\n", &arr1)

    // 创建一个指针, 指向这个数组的地址, 通过指针来操作数组
    var p1 *[4]int = &arr1
    fmt.Printf("p1指向的地址:%p\n", p1)
    fmt.Printf("p1自己的地址:%p\n", &p1)
    fmt.Println("p1指向地址的值:", *p1)

    // 操作数组指针来修改数组
    (*p1)[0] = 100 // 原生写法
    fmt.Println("arr1:", arr1)
    fmt.Println("p1指向地址的值:", *p1)
    // 语法糖: 由于p1指向了arr1这个数组, 所以可以直接用p1来操控 ===> p1 = arr1
    // 指针指向了谁, 就可以代表谁
    p1[0] = 200 // 在程序中我们, 我们更多时候是这样在使用指针的
    fmt.Println("arr1:", arr1)
    fmt.Println("p1指向地址的值:", *p1)
    
}
```

- 指针**数组**，首先应该是一个数组，保存的是指针

```go
package main

import "fmt"

func main() {

    a := 1
    b := 2
    c := 3
    d := 4

    // 创建一个指针数组
    arr1 := [4]*int{&a, &b, &c, &d}
    fmt.Println(arr1)

    // 通过指针修改a的值
    *arr1[0] = 100
    fmt.Println(a)
    fmt.Println(*arr1[0])
}
```



### 指针函数

首先是一个函数，这个函数的返回值是一个指针

```go
package main

import "fmt"

// 指针函数: 指针可以用作函数的返回值的
func main() {
    ptr := f5()
    fmt.Println("ptr:", ptr)
    fmt.Printf("ptr的类型:%T\n", ptr)
    fmt.Printf("ptr的地址:%p\n", &ptr)
    fmt.Println("ptr存储的地址中的值:", *ptr)
    // 使用
    fmt.Println((*ptr)[0])
    fmt.Println(ptr[0])
}

// 返回一个数组指针
func f5() *[4]int {
    arr := [4]int{1, 2, 3, 4}
    return &arr
}
```

### 指针作为函数参数（常用）

```go
package main

import "fmt"

func main() {
    a := 10
    fmt.Println(a)
    fmt.Println("a addr:", &a)
    f6(&a)
    fmt.Println(a)
    fmt.Println("a addr:", &a)
}

// 指针当作函数的参数
func f6(ptr *int) {
    fmt.Println("ptr =", ptr)
    fmt.Printf("ptr addr = %p\n", &ptr)
    fmt.Printf("ptr 指向的地址中的值 = %d\n", *ptr)
    *ptr = 20
}
```

补充：什么时候定义的变量会销毁，没有任何东西再指向它的时候，垃圾回收（GC）会回收

## 结构体

### 定义一个结构体	type name struct 

```go
package main

import "fmt"

// 定义一个结构体  type User struct{}
type User struct {
    name string
    age  int
    sex  string
}

func main() {
    // 通过结构体创建对象, 以前的类型都是用的基本类型, 自己定义类型 type struct
    // 定义了结构体对象, 不赋值, 默认都是这个结构体的零值 {"", 0, ""}
    var user1 User
    // 给结构体对象赋值, xxx.属性 = 值
    user1.name = "张三"
    user1.age = 18
    user1.sex = "male"
    fmt.Println(user1)
    // 获取这个人的名字
    fmt.Println("username: ", user1.name)

    // 创建对象的方式二
    user2 := User{}
    user2.name = "lisi"
    user2.age = 18
    user2.sex = "female"
    fmt.Println(user2)
    // 获取这个人的名字
    fmt.Println("username: ", user2.name)

    // 创建对象的方式三
    user3 := User{
       name: "wangwu",
       age:  11,
       sex:  "male",
    }
    fmt.Println(user3)
    fmt.Println("username: ", user3.name)

    // 创建对象的方式四
    user4 := User{"Elm", 21, "male"}
    fmt.Println(user4)
    fmt.Println("username: ", user4.name)
}
```

### 结构体指针

```go
package main

import "fmt"

// 定义一个结构体  type User struct{}
type User2 struct {
    name string
    age  int
    sex  string
}

func main() {
    user1 := User2{"Elm", 21, "male"}
    fmt.Println(user1)
    // 结构体类型   包.struct名
    fmt.Printf("%T, %p\n", user1, &user1) // main.User2, 0xc0001004b0

    // 结构体是值类型还是引用类型
    user2 := user1
    fmt.Println(user2)
    fmt.Printf("%T, %p\n", user2, &user2) // main.User2, 0xc000100540
    // 地址发生了变化, 是值类型

    // 指针解决值传递的问题
    var user_ptr *User2
    user_ptr = &user1
    fmt.Println(user_ptr)
    fmt.Printf("%T, %p\n", user_ptr, &user_ptr)
    // *user_ptr 等价于 user1
    // 通过指针修改结构体变量的值
    (*user_ptr).name = "Bob"
    fmt.Println((*user_ptr).name)
    // 语法糖
    user_ptr.name = "Alice"
    fmt.Println((*user_ptr).name)

    // 内置函数 new 创建对象, new 关键字创建的对象都返回指针, 而不是结构体对象
    // func new(Tyoe) *Type
    // 通过这种方式创建的结构体更加灵活, 突破了结构体是值类型的限制
    user3 := new(User2)
    fmt.Println(user3)
    user3.name = "hacker"
    user3.age = 35
    user3.sex = "female"
    fmt.Println(user3)
    updateUser(user3)
    fmt.Println(user3)
}

func updateUser(user *User2) {
    user.age = 1000
}
```

### 匿名结构体

```go
package main

import "fmt"

// 匿名结构体

type Student struct {
    name string
    age  int
}

func main() {
    s1 := Student{"Elm", 20}
    fmt.Println(s1.name, s1.age)

    // 匿名结构体, 可以在函数内部创建出来, 创建后就需要赋值使用
    s2 := struct {
       name string
       age  int
    }{
       name: "zhangsan",
       age:  10,
    }
    fmt.Println(s2.name, s2.age)

    // 匿名字段
    t1 := Teacher{"lisi", 19}
    // 如何打印字段, 默认使用数据类型当作名称
    fmt.Println(t1.string, t1.int)
}

// 结构体中的匿名字段, 没有名字的字段, 匿名字段是不可以重复的
type Teacher struct {
    string
    int
}
```

### 结构体嵌套（重点）

```go
package main

import "fmt"

// 一个结构体可能包含一个字段, 而这个字段又是一个结构体: 结构体嵌套
type Person struct {
    name string
    age  int
    addr Address
}
type Address struct {
    city  string
    state string
}

// 结论: 结构体是可以嵌套的, 我们可以定义很多复杂的对象来进行拼接, 构成一个更大的对象
func main() {
    var person = Person{}
    person.name = "Elm"
    person.age = 18
    person.addr = Address{
       city:  "New York",
       state: "NY",
    }
    fmt.Println(person.name, person.age, person.addr.city, person.addr.state)
}
```

### 结构体导出

结构体：结构体名字，属性名字。大写字母可以导出使用，小写字母，不能导出使用，类似（public、private）

**如果结构体名称首字母小写，则结构体不会被导出**。这时，即使结构体成员字段名首字母大写，也不会被导出

**如果结构体名称首字母大写，则结构体可被导出**。但只会导出大写首字母的成员字段，那些小写首字母的成员字段不会被导出

如果存在嵌套结构体，即使嵌套在内层的结构体名称首字母小写，外部也能访问到其中首字母大写的成员字段

`lesson07/base/pojo/User.go`

```go
package pojo

type User struct { // 可以被导出的
    Name  string // 可以被导出
    Age   int    // 可以被导出
    money int    // 不可以被导出
}
```

`lesson07/main.go`

```go
package main

import (
    "fmt"
    "lesson07/base/pojo"
)

func main() {
    var user pojo.User
    user.Name = "Elm"
    user.Age = 18
    // money字段无法被获取到
    fmt.Println(user)
}
```