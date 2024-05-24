# Go面向对象编程

## OOP思想

**Go不是面向对象的语言，只是让大家理解一些面向对象的思想，通过一些方法来模拟面向对象**

**面向过程的思维**

- 线性思维 --- 1/2/3/4/5
- 构建空天站------太过复杂无法使用线性思维完成

**面向对象的思维**

- 是一种分类的思维模式，首先思考这个问题怎么解决，细分为一些不同的类，然后再对类进行单独思考
  - 空天站
    - 地面基地
      - 军方
      - 科研
      - 编码
        - 软件
        - 硬件
    - 空天基地

面向对象可以解决很多复杂的问题，将现实中的问题拟人化（用人的思维方式去解决），适合多人协作

面向过程适合解决简单的问题，个人就可以完成



**面向对象思维核心就三个点：封装、继承、多态**

## 继承

**继承就是子类继承父类的特征和行为，使得子类具有父类的属性和方法，使得子类具有和父类相同的行为。**

**子类会具有父类的一般特性，也会具有自身的特性**

> Go语言中的继承：结构体的嵌套

```go
/*
1、模拟继承  is-a

type A struct{
    field
}
type B struct{
    A  // 匿名字段，使用方式：B.A
    field
}
// B是可以直接访问A的属性的

2、聚合关系  has-a

type C struct{
    field
}
type D struct{
    c C    //聚合
    field
}
//D是无法直接访问C中的属性的，必须使用D.c.c_field
*/
```

```go
package main

import "fmt"

// 定义一个父类
type Person struct {
    name string
    age  int
}

// 定义一个子类，Student拥有了父类的所有属性，还有了自己的特性
type Student struct {
    Person //匿名字段，实现了继承
    school string
}

func main() {
    //1、创建父类对象
    p1 := Person{"Elm", 18}
    fmt.Println(p1)
    fmt.Println(p1.name, p1.age)

    //2、创建子类
    s1 := Student{Person: Person{name: "Sam", age: 18}, school: "mju"}
    fmt.Println(s1)
    fmt.Println(s1.Person.name, s1.Person.age, s1.school)

    //3、创建子类
    var s2 Student
    s2.Person.name = "张三"
    s2.Person.age = 3
    s2.school = "pku"
    fmt.Println(s2)
    fmt.Println(s2.Person.name, s2.Person.age, s2.school)

    //概念：提升字段。只有匿名字段才可以做到
    //Person在Student中是一个匿名字段，Person中的属性 name age 就是提升字段
    //所有的提升字段可以直接使用，不用通过匿名字段来点
    var s3 Student
    s3.name = "李四"
    s3.age = 4
    s3.school = "qinghua"
    fmt.Println(s3)
    fmt.Println(s3.Person.name, s3.Person.age, s3.school)
}
```

**继承就是子类继承父类的特征和行为，使得子类具有父类的属性和方法，使得子类具有和父类相同的行为。子类会具有父类的一般特性，也会具有自身的特性**

匿名字段+提升字段=Go语言中的匿名字段

## 方法

**Go语言中同时拥有函数和方法，一定要和其他只有方法的语言区分开**

方法一定要属于某个结构体（类）

**方法：需要指定调用者，约定这个方法属于谁的	对象.方法()**

**函数：不需要指定调用者，定义了函数就可以直接函数名()调用**

```go
package main

import "fmt"

// 方法
type Dog struct {
    name string
    age  int
}

type Cat struct {
    name string
    age  int
}

// 方法定义，需要增加一个调用者。func 函数调用者 函数名()
func (d Dog) eat() {
    fmt.Println("Dog eating...")
}

// 1.函数是不可以重名的，但是方法可以重名
// 2.如果调用者相同则不能重名
func (c Cat) eat() {
    fmt.Println("Dog eating...")
}

func (c Cat) sleep() {
    fmt.Println("Cat sleeping...")
}

func main() {
    var d Dog
    d.name = "旺财"
    d.age = 3
    // 方法的调用通过对应的结构体对象来调用
    d.eat()
    var c Cat
    c.name = "Tom"
    c.age = 3
    c.eat()
    c.sleep()

    fmt.Println(d)
    fmt.Println(c)
}

// 方法可以理解为函数多了一个调用者
// 同名的方法通过不同的调用者展现出来的内容是不一样的
```

方法：

- 某个类的行为功能，需要指定调用者
- 一段独立的代码功能，必须要使用调用者来调用
- 多个类的方法可以重名，单个类不行
- 方法是某个类的动作

函数

- 一段独立的代码功能，可以直接调用
- 命令完全不能冲突
- 函数是一个特殊的类型

## 方法的重写

需要和继承结合

子类可以重写父类的方法 override

子类还可以新增自己的方法

子类还可以访问父类中的属性和方法

```go
package main

import "fmt"

// 方法重写，建立在父类和子类结构上的
type Animal struct {
	name string
	age  int
}

func (a Animal) eat() {
	fmt.Printf("%s eats\n", a.name)
}

func (a Animal) sleep() {
	fmt.Printf("%s sleeps\n", a.name)
}

// 子类
type Dog1 struct {
	Animal
}

// 子类自己的方法
func (d Dog1) bark() {
	fmt.Printf("%s barks\n", d.name)
}

// 子类重写父类的方法, 子类的方法名和父类重名即可重写父类的方法
func (d Dog1) eat() {
	fmt.Printf("%s eats\n", "大黄")
}

type Cat1 struct {
	Animal
}

func main() {
	// 定义一个子类，使用父类方法
	dog := Dog1{Animal{
		name: "旺财",
		age:  3,
	}}
	// 重写父类的的方法，就是调用子类自己的方法
	dog.eat()
	cat := Cat1{Animal{"mimi", 3}}
	cat.eat()

	// 子类可以操作父类的方法，父类可以操作子类的吗？不可以
	// 父类不能调用子类自己的扩展方法
	a := Animal{"temp", 3}
	a.eat()
	a.sleep()
}

```

## 接口

```go
package main

import "fmt"

// 接口
// 1、Go语言提供了接口数据类型
// 2、接口就是把一些共性的方法集合在一起定义
// 3、如果有实现类将接口定义的方法全部实现了，那么就代表实现了这个接口
// 4、显式实现implement A implement B    Java 强耦合   低效的
// 5、隐式实现 Go，假设A实现了B接口中的所有方法，不需要显式声明
// 6、接口是方法的定义集合，不需要实现具体的方法内容

// 定义接口，方法太多了要归类，所以这是方法的集合
type USB interface {
    input()  // 输入方法
    output() // 输出方法
}

// 结构体
type Mouse struct {
    name string
}

// 实现了接口的全部方法就代表实现了这个接口，否则不算实现这个接口
func (m Mouse) input() {
    fmt.Println("Mouse input")
}
func (m Mouse) output() {
    fmt.Println("Mouse output")
}

type Keyboard struct {
    name string
}

func (k Keyboard) input() {
    fmt.Println("Keyboard input")
}
func (k Keyboard) output() {
    fmt.Println("Keyboard output")
}

// 测试接口
func test(u USB) {
    u.input()
    u.output()
}

func main() {
    // 通过传入接口实现类来进行调用
    m1 := Mouse{name: "雷蛇"}
    // test 参数 USB 类型
    // 如果一个结构体实现了这个接口的所有的方法，那么这个结构体就是这个接口类型
    test(m1)
    k1 := Keyboard{"狼蛛"}
    test(k1)

    // 定义高级类型
    var usb USB
    // k1升级了，Keboard --> USB 向上转型
    usb = k1
    fmt.Println(usb)
    // 接口是无法使用实现类的属性的，接口里面只能用方法
    fmt.Println(usb)
}
```

## 模拟多态

多态：一个事物拥有多种形态

```
父类：动物
子类：猫、狗
猫和狗是多态的他们既可以是自己，也可以是动物，这个就是多态，一个事物有多种形态
```

```go
package main

// 定义接口
type Animal1 interface {
    eat()
    sleep()
}

type Dog2 struct {
    name string
}

func (d Dog2) eat() {
    println(d.name, "---eat")
}
func (d Dog2) sleep() {
    println(d.name, "---sleep")
}

// 多态
func main() {
    // Dog 两重身份：1、Dog  2、Animal，多态
    dog := Dog2{"旺财"}
    dog.eat()
    dog.sleep()

    // Dog 也可以是 Animal
    test1(dog)

    // 定义一个类型可以为接口类型的变量
    // 实际上所有实现类都可以赋值给这个对象
    var animal1 Animal1 // 模糊的、抽象的--需要具体化的实现类赋值
    animal1 = dog       // 实现类赋值给接口，实现意义
    test1(animal1)

}

func test1(a Animal1) {
    a.eat()
    a.sleep()
}
```

接口的实现类都拥有多态特性，除了它自己本身还是他对应接口的类型

## 空接口

不包含任何方法，因此：所有的结构体都默认实现了空接口

因此空接口可以存储任何的类型

```
interface{} == any	也就相当于Java中的Object
```

```go
package main

import "fmt"

// 定义空接口
type A interface {
}
type Dogg struct {
    name string
}
type Catt struct {
    name string
}

func testNow(a A) {
    fmt.Println(a)
}

func main() {
    var a1 A = Catt{"mimi"}
    var a2 A = Dogg{"wangcai"}
    var a3 A = 1
    var a4 = "Elm"
    testNow(a1)
    testNow(a2)
    testNow(a3)
    testNow(a4)

    testNow2(a1)
    testNow2(a2)
    testNow2(a3)
    testNow2(a4)

    // any == interface{}
    // map
    map1 := make(map[string]interface{})
    map1["name"] = "zhangsan"
    map1["age"] = 18

    // slice
    s1 := make([]any, 0, 10)
    s1 = append(s1, 1, "1321", false)

    // array
    var arr [4]interface{}
    fmt.Println(arr)
}

// 方法形参定义为空接口就形同与any，可以接收任何类型的参数
func testNow2(temp interface{}) {
    println(temp)
}
```

## 接口嵌套

```go
package main

import "fmt"

type AA interface {
    testA()
}

type BB interface {
    testB()
}

// 如果要实现接口CC，那么就需要实现三个方法。
// 那这个对象就有三个接口可以转型
type CC interface {
    AA // 导入AA接口中的方法
    BB // 导入BB
    testC()
}

type Dog7 struct {
    name string
}

func (d Dog7) testA() {
    fmt.Println("testA")
}
func (d Dog7) testB() {
    fmt.Println("testB")
}
func (d Dog7) testC() {
    fmt.Println("testC")
}

func main() {

    var dog Dog7
    dog.testA()
    dog.testB()
    dog.testC()

    // 向上转型后只能调用它自己对应的方法
    var a AA = dog
    a.testA()
    var b BB = dog
    b.testB()
}
```

## 接口断言

**检查一个接口类型的变量是不是符合你的预期值**

比如：我们预期使用test3()，如果传递的是a、b对象，那么我们就需要判断这个对象是否是我们预期的对象，这就称之为断言。

**被断言的对象必须是接口类型，否则会报错，但是所有的类型都是空接口，所以说Go语言中所有的类型都可以进行断言**

如果一个对象存在多种形态

- i.(T)判断是否是预期接口
- switch i.(type)判断是否是预期的case结果

```go
package main

import (
    "fmt"
)

type test9 interface {
}

func test9Asserts(i interface{}) {
    switch i.(type) {
    case test9:
       fmt.Println("is test9")
    case int:
       fmt.Println("is Int")
    case string:
       fmt.Println("is String")
    case bool:
       fmt.Println("is Bool")
    case nil:
       fmt.Println("is nil")
    default:
       fmt.Println("not found")
    }
}

func main() {
    var t test9
    // 当t没有赋值的时候默认为nil类型
    test9Asserts(t)
    // 只有赋值了只会才会是对应的类型
    var t2 test9
    t2 = 1
    test9Asserts(t2)
}
```

## type定义类型、别名

```go
package main

import "fmt"

// var 定义变量
// type 定义类型(结构体、接口、...)

// type 的别名用法，全局变量中是定义了一个新类型
// MyInt 是int转换过来的和int一样，但是不能同int发生操作
type MyInt int

func main() {
    var a int = 19
    var b MyInt = 20
    // invalid operation: a + b (mismatched types int and MyInt)
    // fmt.Println(a + b)
    // 类型转换后才能互相操作
    fmt.Println(int(b) + a)
    fmt.Printf("%T\n", b)
    fmt.Printf("%T\n", a)

    // 另一种用法：给int起一个小名，但是它还是int
    type diyint = int // diyint和int一样
    var c int = 19
    fmt.Println(a + c)
    fmt.Printf("%T\n", c)
}

/*
type关键字的理解：
1、type定义一个类型
 - type User struct 定义结构体类型
 - type User interface 定义接口类型
 - type Diy (int、string、...)自定义类型，全新的类型
2、type起别名：
 - type xxx = 类型    将某个类型赋值给xxx，相当于这个类型的别名
 - 别名只能在写代码的时候使用，增加代码的可阅读性
 - 真实在项目的编译过程中，它还是原来的类型
*/
```