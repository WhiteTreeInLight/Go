# Go语言流程控制

程序的本质：将现实逻辑使用代码来进行编辑，将逻辑一一映射到代码中

**现实世界的逻辑**

- 定义（属性）
- 判断（对错，导致不同的结果）
- 循环（很多事情本质上都是在循环当中）

程序代码底层的本质就是跟上面相同的，是所有编程语言都有的



顺序结构：自上而下运行

选择结构：满足了某些条件才会执行

- if （常用的）
- switch （某些情况下可以优化代码结构）

循环结构：条件满足的情况，我们会去循环0 - N次。

- for

## if-else

- if 可以单独存在，不一定要配 else
- if...else
- if...else if ... else if ... else

```
if 条件: bool {

}
```

```go
package main

import "fmt"

func main() {
	var num int = 15

	// if 的代码必须条件满足才可以执行
	if num > 100 {
		fmt.Println("num > 100")
	}

	fmt.Println("main func end.....")
}

```

> 如果....否则

```go
package main

import "fmt"

func main() {

	// 成绩
	var score int = 90

	// 满足条件则进入对应的处理
	if score >= 60 {
		fmt.Println("及格")
	} else {
		// 不满足就进入 else
		fmt.Println("不及格")
	}
}

```

> 小练习

```go
package main

import "fmt"

func main() {
	// 练习：if 的练习

	// 判断用户密码输入是否正确
	// 输入框：接收用户的输入
	// 第一次判断
	// 输入框，请再次输入密码
	// 第二次判断
	// 如果两次都是对的，那么登录成功

	var num1 int
	var num2 int

	// 提示用户输入
	fmt.Println("请输入密码：")
	// 接收用户的输入 （阻塞式等待，直到用户输入之后才继续运行）
	fmt.Scan(&num1)

	if num1 == 123456 {
		fmt.Println("请再次输入密码：")
		fmt.Scan(&num2)
		if num2 == 123456 {
			fmt.Println("登录成功")
		} else {
			fmt.Println("登录失败")
		}
	} else {
		fmt.Println("登录失败")
	}

}

```

> 多个条件判断

```go
package main

import "fmt"

func main() {

	// 分数
	var score int
	fmt.Println("请输入你的成绩：")
	fmt.Scan(&score)
	// 逻辑运算符应用
	if score >= 90 && score <= 100 {
		fmt.Println("A")
	} else if score >= 80 && score < 90 {
		fmt.Println("B")
	} else if score >= 70 && score < 80 {
		fmt.Println("C")
	} else if score >= 60 && score < 70 {
		fmt.Println("D")
	} else if score < 0 || score > 100 {
		fmt.Println("非法输入")
	} else {
		fmt.Println("不及格")
	}
}

```

## switch

```
switch var1 {
	case val1:
	case val2:
	default:
}
```

```go
package main

import "fmt"

func main() {

	var score int = 60

	// 通过 switch 来判断 score
	switch score {
	case 100:
		fmt.Println("A")
	case 90:
		fmt.Println("B")
	case 80:
		fmt.Println("C")
	default:
		fmt.Println("others")
	}

}

```

> switch 的基本使用

```go
package main

import "fmt"

func main() {

	var score int = 95

	// 通过 switch 来判断 score
	// case，后面可以写多个条件
	switch score {
	case 100, 95, 91:
		fmt.Println("A")
	case 90:
		fmt.Println("B")
	case 80:
		fmt.Println("C")
	default:
		fmt.Println("others")
	}

	// 不写 switch 的参数默认为 true
	switch {
	case false:
		fmt.Println("false")
	case true:
		fmt.Println("true")
	default:
		fmt.Println("other")
	}
}

```

> 特殊的情况：需要多个条件结果的输出，case穿透  。fallthrough

```go
package main

import "fmt"

func main() {

	a := false

	switch a {
	case false:
		fmt.Println("1")
		fallthrough // 一但使用 fallthrough ，则会强制执行下一个 case 语句
	case true:
		fmt.Println("2")
		fallthrough
	case false:
		fmt.Println("3")
		fallthrough
	case true:
		fmt.Println("4")
	}
}

```

> 分支结构小结

- if
- if...else
- if...else if...else if...else
- switch-case
- switch-case-fallthrough
- switch-case-default

## for

循环：做一些重复性的操作

```go
package main

import "fmt"

func main() {
	// for 循环
	// for 参数1：从哪里开始	参数2：到哪里结束
	// 参数3：控制这个循环条件变量（自增和自减）

	// 输出 0-10，每输出完一个换行
	for i := 0; i <= 10; i++ {
		fmt.Println(i)
	}
}

```

> 计算1 + 10 的和

```go
package main

import "fmt"

func main() {
	sum := 0

	// goland 的快捷输入 fori
	for i := 1; i <= 10; i++ {
		sum += i
		fmt.Println("i = ", i, " , sum = ", sum)

	}

	fmt.Println(sum)

}

```

> for 循环的探究

```go
package main

import (
	"fmt"
	"time"
)

// 探究 for 循环	init：起始值	end：结束值	condition：条件变量
func main() {
	i := 0
	// 在 for 循环中定义的变量，作用域就只在 for 循环内，外面无法调用
	for i <= 5 {
		i++
	}
	fmt.Println(i)

	// 没有起始值，没有结束值====无限循环，死循环
	for {
		fmt.Println("hello world")
		time.Sleep(time.Second)
	}
}

```

> 终止循环

break：结束整个循环

continue：结束当前这次循环，继续下一次循环

