

# Golang Tutorial

### 1.go安装

**优势：**快速开发和高性能。

**go编译器安装教程：（同时包含go入门教程，自行查看）**

```
https://www.cainiaoplus.com/golang/go-environment-install.html
```

安装完成打开cmd键入`go version`，返回如下信息：`go version go1.21.4 windows/amd64`

在cmd做一下相关配置：

1.键入`go env`查看相关配置

2.键入如下命令修改配置（默认的是：`set GO111MODULE=     set GOPROXY=https://proxy.golang.org,direct`）

```go
set GO111MODULE=on
set GOPROXY=https://goproxy.cn,direct
```

**后续我只更新与其它语言不一样的知识点**

### 2.基本知识点

#### 2.1标识符命名规则：

- 与`c/c++`一样，标识符由字母、数字和下划线组成，且只能以字母和下划线开头，不能以数字开头，关键字也不能做标识符。

- 在Go语言中，有一些预定义的标识符可用于常量，类型和函数。以下是预定义标识符列表：

```go
常量:
true, false, iota, nil

类型:
int, int8, int16, int32, int64, uint,
uint8, uint16, uint32, uint64, uintptr,
float32, float64, complex128, complex64,
bool, byte, rune, string, error

函数:
make, len, cap, new, append, copy, close, 
delete, complex, real, imag, panic, recover
```

#### 2.2关键字：

没啥讲的...

#### 2.3数据类型：

1. **基本类型：**数字，字符串和布尔值属于此类别。
2. **聚合类型：**数组和结构属于此类别。
3. **引用类型：**指针，切片，map集合，函数和Channel属于此类别。
4. **接口类型**

- **整数**

![image-20231201105115550](./../../images/image-20231201105115550.png)

```go
// 整数举例
package main
import "fmt"

func main()
{
    var x uint8 = 225
    fmt.Printf("%d", x)
}
// 打印225
```

- **浮点数**

![image-20231201105522003](./../../images/image-20231201105522003.png)

```go
// 浮点数举例
package main  
import "fmt"
         
func main() { 
    a := 20.45 
    b := 34.89 
      
    //两个浮点数相减
    c := b-a 
      
    //显示结果 
    fmt.Printf("结果: %f", c) 
      
    //显示c变量的类型
    fmt.Printf("\nc的类型是 : %T", c)   
}

// 输出：
// 结果: 14.440000
// c的类型是: float64
```

- **复数：**将复数分为两部分，如下表所示。`float32`和`float64`也是这些复数的一部分。

![image-20231201110005175](./../../images/image-20231201110005175.png)

```go
//复数的使用 
package main 
import "fmt"
  
func main() { 
      
   var a complex128 = complex(6, 2) 
   var b complex64 = complex(9, 2) 
   fmt.Println(a) 
   fmt.Println(b) 
     
   //显示类型 
  fmt.Printf("a的类型是 %T 以及"+ "b的类型是 %T", a, b) 
}

// (6+2i)
// (9+2i)
// a的类型是 complex128 以及b的类型是 complex64
```

- **布尔类型：**布尔数据类型仅表示true或false。布尔类型的值不会隐式或显式转换为任何其他类型。

```go
//布尔值的使用
package main

import "fmt"

func main() {

    //变量
    str1 := "nhooo"
    str2 := "nhooo"
    str3 := "nhooo"
    result1 := str1 == str2
    result2 := str1 == str3

    //打印结果
    fmt.Println(result1)
    fmt.Println(result2)

    //显示result1和result2的类型
    fmt.Printf("result1 的类型是 %T ， "+"result2的类型是 %T", result1, result2)

}

// true
// true
// result1 的类型是 bool ， result2的类型是 bool
```

- **字符串：**字符串数据类型表示Unicode代码点的序列。换句话说，我们可以说一个字符串是不可变字节的序列，这意味着一旦创建了一个字符串，您就无法更改该字符串。

```go
//使用字符串
package main 
import "fmt"
  
func main() { 
      
    //用于存储字符串的str变量
   str := "nhooo"
     
   //显示字符串的长度
   fmt.Printf("字符串的长度:%d", len(str)) 
     
   //显示字符串 
   fmt.Printf("\n字符串是: %s", str) 
     
   // 显示str变量的类型
   fmt.Printf("\nstr的类型是: %T", str) 
}

// 字符串的长度:5
// 字符串是: nhooo
// str的类型是: string
```

#### 2.4变量

变量是通过两种方式创建的：

- 使用`var`关键字：语法为`var variable_name type = expression`，在上述语法中，变量类型`type`和`= expression`可以二删一，如果删除了类型`type`，则变量类型由表达式中的初值确定。

- 使用**短变量**声明：语法为`variable_name:= expression`，注意`:=`是声明，而`=`是赋值。在上面的表达式中，变量的类型由表达式的类型确定。

- **需要注意的是：**借助短变量声明操作符（：=），**您只能声明**仅具有块级作用域**的局部变量**。如果尝试使用short声明运算符声明全局变量，则会抛出错误消息。syntax error: non-declaration statement outside function body

```go
//方式一：
var name string = "hello"

//方式二：Go是一种静态类型的语言，但是它仍然提供了一种在声明变量的同时省略数据类型声明的功能，如以下语法所示。这通常称为类型推断。类似于c++的auto关键字
var name = "hello"    // fmt.Printf("%T", name)   打印string

//方式三：
var name string
name = "hello"

//方式四：在单个声明中声明相同类型的多个变量。在声明期间使用类型时，只允许声明多个相同类型的变量。
var myvariable1, myvariable2, myvariable3 int = 2, 454, 67

//方式五：在单个声明中声明不同类型的多个变量。变量的类型由初始化值确定。  在声明期间删除类型，您可以声明多个不同类型的变量。
var myvariable1, myvariable2, myvariable3 = 2, "GFG", 67.56

//方式六：使用多行使用var关键字声明和初始化不同类型的值
var(
     nhooo1 = 100
     nhooo2 = 200.57
     nhooo3 bool
     nhooo4 string = "hello"
)

//方式七：使用短变量声明，可以在单个声明中声明不同类型的多个变量。这些变量的类型由表达式确定。
myvariable1, myvariable2, myvariable3 := 800, 34.7, 56.9
myvariable1, myvariable2, myvariable3 := 800, "NHOOO", 47.56

// 打印格式控制符%t输出bool类型的变量，%T打印变量的数据类型
package main
import "fmt"
func main() {
	var (
		a int  = 1
		b      = "hello"
		c bool = false
		d bool = false
	)
	c = true
	fmt.Printf("%d\t%s\t%t\t%T\n", a, b, c, d) // 1	hello	true	bool
}

```

#### 2.5常量

- 使用关键字`const`声明：

```go
const PI = 3.14
const STR = "hello World"
```

#### 2.6运算符

- 按位运算符多一种：`&^`，按位清除运算符

```go
package main

import "fmt"

func main() {

	a := 3
	b, c, d, e := 0, 1, 2, 3
	res1, res2, res3, res4 := a&^b, a&^c, a&^d, a&^e
	fmt.Printf("%d %d %d %d", res1, res2, res3, res4) // 3 2 1 0
}
```

- 杂项运算符：`&`：取地址运算符，`*`：指针运算符，和`c/c++`里面的作用一致。

#### 2.7类型转换

- c/c++,java之类的静态类型语言提供了对隐式类型转换的支持，但是**Golang不支持自动类型转换或隐式类型转换**。对于类型转换，必须执行**显式转换**。

显示转换的语法和c/c++也有一定区别，强制转换的类型没有了括号：`type(val)`

```go
var nhooo1 int = 845

// 显式类型转换举例
var nhooo2 float64 = float64(nhooo1)
var nhooo3 int64 = int64(nhooo1)
var nhooo4 uint = uint(nhooo1)

package main
import "fmt"
func main() {
	var a, b int = 1, 2
	c := float32(a)
	fmt.Printf("%f %T %d", c, a, b) //1.000000 int 2
}
```

### 3.控制语句

**代码块即使只有一行语句，也需要加花括号{}将代码块括起来**，c/c++的代码块只有一行语句，可以不用花括号{}括起来。

**注意事项：**

- 在if和for循环的表达式 不使用括号()。

- 花括号在if和for循环中是必需的。
- 左括号应与if和for语句所在的行相同。
- 如果数组，字符串，切片或map集合为空，则for循环不会抛出错误并继续其流程。换句话说，如果数组，字符串，切片或map为nil，则for循环的迭代次数为零。

#### 3.1条件语句（分支语句）

- if中的运算表达式**不加括号()**，c/c++需要加括号，其它的和c/c++一模一样。

```go
if a < b {  //花括号必须要, 并且左花括号{应与if或者for语句所在的行相同。
	c = b
} else { 
	c = a
}
```

#### 3.2循环语句

- for循环也**不加括号()**。

```go
for i:=0; i < 4; i ++ {
    fmt.Printf("%d ", i)
}
```

- 死循环写法，类似于`c/c++`的`while(1){...}`。

```go
for{
    ...
}
```

- for循环用作while循环。

```go
for i < 3 {
	...
}
// 等价
while i < 3 {
    
}
```

- 类似于python的zip循环，循环键值对：

```go
package main
import "fmt"
func main() {
	a := []string{"hello", "world", "baby"}
	for k, v := range a {
		fmt.Printf("%d %s\n", k, v)
	}
}
// 打印
/*
0 hello
1 world
2 baby
*/
```

- 对字符串使用for循环：

```go
package main
import "fmt"
func main() {
	var str string = "my wife is you?"
	for idx, ch := range str {
		fmt.Printf("%d %c\n", idx, ch)
	}
    // 等价写法
    var str string = "my wife is you?"
	var n int = len(str)
	for i := 0; i < n; i++ {
		fmt.Printf("%d %c\n", i, str[i])
	}
}
/*
0 m
1 y
2  
3 w
4 i
5 f
6 e
7  
8 i
9 s
10  
11 y
12 o
13 u
14 ?
*/
```

- 对于map使用for循环：for循环遍历map的键值对。

```go
package main

import "fmt"

func main() {
	mp := map[int]string{
		24:  "you",
		89:  "are",
		71:  "my",
		100: "wife",
	}
	for key, value := range mp {
		fmt.Printf("%d %s\n", key, value)
	}
}
/*
24 you
89 are
71 my
100 wife
*/
```

- **（待学习，现在看不懂2023.12.1）For通道： for循环可以遍历通道上发送的顺序值，直到关闭为止。 **

```go
package main

import "fmt"

func main() {

    // 使用 channel
    chnl := make(chan int)
    go func() {
        chnl <- 100
        chnl <- 1000
        chnl <- 10000
        chnl <- 100000
        close(chnl)
    }()
    for i := range chnl {
        fmt.Println(i)
    }

}
/*
100
1000
10000
100000
*/
```

#### 3.3循环控制语句

- goto用不上

```go
package main
import "fmt"
func main() {
	var i int = 0
index:
	for i < 5 {
		if i == 3 {
			i++
			goto index
		}
		fmt.Printf("%d\n", i)
		i++
	}
}
/*
0
1
2
4
*/
```

#### 3.4switch语句

三种常用方式：

```go
package main
import "fmt"
func main() {
	switch n := 2; n {
	case 1:
		fmt.Printf("111")
	case 2:
		fmt.Printf("222")
	case 3:
		fmt.Printf("333")
	default:
		fmt.Printf("444")
	}
}
/*
222
*/

package main
import "fmt"
func main() {
	var n int = 3
	switch n {
	case 1:
		fmt.Printf("111")
	case 2:
		fmt.Printf("222")
	case 3:
		fmt.Printf("333")
	default:
		fmt.Printf("444")
	}
}
/*
333
*/

package main
import "fmt"
func main() {
	var n int = 3
	switch {
	case n == 1:
		fmt.Printf("111")
	case n == 2:
		fmt.Printf("222")
	case n == 3:
		fmt.Printf("333")
	default:
		fmt.Printf("444")
	}
}
/*
333
*/
```

### 4.函数&方法

#### 4.1函数声明

语法：

```go
func function_name(parameter_list) (return_type) {
    ...
}
```

- **func：**它是Go语言的关键字，用于创建函数。

- **function_name：**它是**函数**的名称。

- **parameter_list：**包含函数参数的名称和类型。

- **return_type：**这是可选的，它包含**函数返回的值的类型**。如果在函数中使用return_type，则必须在函数中使用return语句。

```go
package main
import "fmt"
func area(width, height int) int {
	var area int
	area = width * height
	return area
}
func main() {
	fmt.Printf("%d\n", area(10, 12))  // 120
}

///////////////////////////
package main
import "fmt"
func swap(a *int, b *int) {
	t := *a
	*a = *b
	*b = t
}
func main() {
	a, b := 2, 3
	fmt.Printf("%d %d\n", a, b)
	swap(&a, &b)  //调用函数
	fmt.Printf("%d %d\n", a, b)
}
/*
2 3
3 2
*/
```

#### 4.2变参函数

定义：允许用户在变参函数中传递零个或多个参数。其中`fmt.Printf`是可变参数函数的示例。

```go
package main
import (
	"fmt"
	"strings"
)
func joinstr(str ...string) string { //可变参数函数连接字符串
	return strings.Join(str, "")
}
func main() {
	fmt.Printf("%s\n", joinstr("I", "love", "you"))
	fmt.Printf("%s\n", joinstr("A", "E", "I", "O", "U"))
}
/*
Iloveyou
AEIOU
*/
```

#### 4.3匿名函数

定义：匿名函数是不包含任何名称的函数。当您要创建内联函数时，此函数很有用。匿名函数可以形成闭包。

语法：

```go
func(parameter_list) return_type {
    ...
    return
}()
```

注意：

1.可以将匿名函数分配给变量，变量的类型就是函数类型。可以像调用函数一样调用变量。

2.匿名函数还可以作为参数传递给其它函数，还可以从另一个函数返回匿名函数。

```go
package main
import "fmt"
func main() {
	func(str string) {
		fmt.Printf("%s\n", str)
	}("hello")
}
// hello


package main
import "fmt"
var my_function func(str string) = func(str string) {  //匿名函数赋值给my_function变量
	fmt.Printf("%s\n", str)
}
func main() {
	my_function("hello")
	fmt.Printf("%T\n", my_function)
}
/*
hello
func(string)
*/

package main
import "fmt"
//匿名函数作为参数传递
func GFG(i func(p, q string) string) {
	fmt.Println(i("Geeks", "for"))
}
func main() {
	value := func(p, q string) string {
		return p + q + "Geeks"
	}
	GFG(value) //匿名函数作为参数传递
}
/*
GeeksforGeeks
*/
```

#### 4.4函数返回多个值

定义：允许一个return返回多个值。

语法：

```go
func function_name(parameter_list)(return_type_list) {
    ...
    return 
}
```

```go
package main

import "fmt"

func math_function(a, b int) (int, int, int) {
	return a + b, a - b, a * b
}

func main() {
	a, b, c := math_function(2, 1)
	fmt.Printf("%d %d %d\n", a, b, c)
}
/*
3 1 2
*/
```

**为返回值命名：**在Go语言中，允许为返回值提供名称。你也可以在代码中使用这些变量名。没有必要用return语句来编写这些名称，因为Go编译器将自动理解这些变量必须被分派回去。这种类型的回报被称为**裸回报**。简单的返回减少了程序中的重复。

```go
package main

import "fmt"

func math_function(a, b int) (add, sub, mul int, res string) { //默认返回这四个变量
	add, sub, mul = a+b, a-b, a*b
	res = "OK"
	return   //裸返
}

func main() {
	a, b, c, res := math_function(2, 1)
	fmt.Printf("%d %d %d %s\n", a, b, c, res)
}
/*
3 1 2 OK
*/
```

#### 4.5空白标识符（下划线）

作用：Golang是一种更加简洁和可读的编程语言，因此它不允许程序员定义未使用的变量，如果你这样做，编译器将抛出一个错误。因此用**空白标识符`_`定义的变量可以不使用**，它告诉编译器不需要此变量，并且将其忽略而没有任何错误，**并且`_`不可用**。

```go
package main
import "fmt"
func main() {

	//调用函数
	//函数返回两个值
	//分配给mul和空白标识符
	mul, _ := mul_div(105, 7)

	//只使用mul变量
	fmt.Println("105 x 7 = ", mul)
}

func mul_div(n1 int, n2 int) (int, int) {
	//返回值
	return n1 * n2, n1 / n2
}
/*
105 x 7 =  735
*/
```

#### 4.6方法(method)

定义：Go方法与Go函数相似，但有一点不同，就是方法中包含一个接收者参数。在接收者参数的帮助下，该方法可以访问接收者的属性。在这里，接收方可以是结构类型或非结构类型。在代码中创建方法时，接收者和接收者类型必须出现在同一个包中。而且不允许创建一个方法，其中的接收者类型已经在另一个包中定义，包括像int、string等内建类型。如果您尝试这样做，那么编译器将抛出错误。（好好理解）

**语法：**

```go
func(reciver_name Type) method_name(parameter_list)(return_type){
    // Code  //在此，可以在方法内访问接收器。
}
```

- **结构类型接收器的方法**：定义其接收者为结构类型的方法。可以在方法内部访问此接收器：

```go
package main

import "fmt"

// type 类似于c/c++的#define 自定义类型
type user struct {
	name   string
	age    int
	salary int
}

func (a user) show(pay int) (total_salary int) {
	fmt.Printf("my name is %s, i'm %d years old.\n", a.name, a.age)
	total_salary = pay + a.salary
	return
}

func main() {
	person := user{
		name:   "royal_111",
		age:    22,
		salary: 200000,
	}
	salary := person.show(100) //调用方法用.method()
	fmt.Printf("my salary is %d.\n", salary)
}
/*
my name is royal_111, i'm 22 years old.
my salary is 200100.
*/
```

- **非结构类型接收器的方法：**只要类型和方法定义存在于同一包中，就可以使用非结构类型接收器创建方法。如果它们存在于int，string等不同的包中，则编译器将抛出错误，因为它们是在不同的包中定义的。

```go
//说白了定义方法，就需要自定义接收器类型，不能使用int string这种在其它包中定义好的类型

//报错的举例：
package main

import "fmt"

func (a int) add(b int) (c int) { //cannot define new methods on non-local type int
	c = a + b
	return
}

func main() {
	a := 5
	fmt.Println(a.add(3))
}
/*
.\main.go:5:7: cannot define new methods on non-local type int
*/

//正确示范，应该自己定义接收者类型
package main

import "fmt"

type Integer int

func (a Integer) add(b Integer) (c Integer) { //cannot define new methods on non-local type int
	c = a + b
	return
}

func main() {
	a := Integer(5)
	var b Integer = 4
	fmt.Println(a.add(3), b.add(6)) //5 + 3   4 + 6
}
/*
8 10
*/
```

- **方法可以接受指针和值：一点都不符合规范**。在Go中，当一个函数具有值参数时，它将仅接受参数的值，如果您尝试将指针传递给值函数，则它将不接受，反之亦然。但是Go方法可以接受值和指针，无论它是使用指针还是值接收器定义的。

```go
package main

import "fmt"

// Author 结构体
type author struct {
	name   string
	branch string
}

//带有指针的方法
//*author类型的接收者
func (a *author) show_1(abranch string) {
	(*a).branch = abranch
}

//带有值的方法
//author类型的接收者
func (a author) show_2() {
	a.name = "Gourav"
	fmt.Println("Author's name(Before) : ", a.name)
}

func main() {

	//初始化值
	//作者结构体
	res := author{
		name:   "Sona",
		branch: "CSE",
	}

	fmt.Println("Branch Name(Before): ", res.branch)

	//调用show_1方法
	//(&res).show_1("ECE")  这种符合规范
	res.show_1("ECE")
	fmt.Println("Branch Name(After): ", res.branch)

	//调用show_2方法
	//res.show_2()  这种符合规范
	(&res).show_2()
	fmt.Println("Author's name(After): ", res.name)
}
/*
Branch Name(Before):  CSE
Branch Name(After):  ECE
Author's name(Before) :  Gourav
Author's name(After):  Sona
*/
```

![image-20231202111957624](./../../images/image-20231202111957624.png)

#### 4.7同名方法

定义：在Go语言中，允许在同一包中**创建两个或多个具有相同名称的方法**，但是这些方法的接收者**必须具有不同的类型**。该功能在Go**函数**中不可用，这意味着不允许您在同一包中创建相同名称的函数，如果尝试这样做，则编译器将抛出错误。

```go
package main

import "fmt"

//创建结构体
type student struct {
	name   string
	branch string
}

type teacher struct {
	language string
	marks    int
}

//名称相同的方法，但有不同类型的接收器
func (s student) show() {

	fmt.Println("学生姓名:", s.name)
	fmt.Println("Branch: ", s.branch)
}

func (t teacher) show() {

	fmt.Println("Language:", t.language)
	fmt.Println("Student Marks: ", t.marks)
}

func main() {

	// 初始化结构体的值
	val1 := student{"Rohit", "EEE"}

	val2 := teacher{"Java", 50}

	//调用方法
	val1.show()
	val2.show()
}
/*
在上面的示例中，我们有两个相同的名称方法，即show()，但接收器的类型不同。这里，第一个show()方法包含s student类型的接收者，第二个show()方法包含t teacher类型的接收者。在main()函数中，我们借助各自的结构体变量来调用这两种方法。如果尝试使用相同类型的接收器创建此show()方法，则编译器将抛出错误。

学生姓名: Rohit
Branch:  EEE
Language: Java
Student Marks:  50
*/
```

### 5.结构体

#### 5.1基本概念

**不支持继承但支持组合的轻量级类**。

- 利用**type**自定义新类型，例如：`type my_integer int`，以后`my_integer`和`int`作用等价。

语法：

```go
type struct_name struct {
    ... //成员变量
}

//例如定义一个用户结构体
type User struct {
    username, password string
    age int
}

//声明一个用户类型的变量，默认情况下初值为""或0
var a User

//使用User来初始化user类型的变量
var b = User{"royal_111", "123456", 22}

//还可以对部分成员变量初始化,其它未初始化的默认为对应的零
c := User{username:"royal", age:22}

var a User
a.age = 11

//访问User结构体的成员变量，用(.)运算符

```

- 指向结构体的指针引用成员变量和`c/c++`有一定区别，如下：

```go
package main

import (
	"fmt"
)

type User struct {
	username, password string
	age, salary        int
}

func main() {
	//定义一个指向User类型的结构体指针
	p := &User{"royal_111", "123456", 22, 300000}
	//传统的引用方法，显示的解引用
	fmt.Printf("%s %d\n", (*p).username, (*p).salary)

	//Golang为我们提供了非显示的引用成员变量
	fmt.Println(p.username, p.salary)
}
/*
royal_111 300000
royal_111 300000
*/
```

- 同类型的结构体变量判断是否相等可以直接使用`==`进行判断，也可以使用`reflect`包下的`DeeplyEqual()`方法。

```go
type User struct {
    username, password string
}

u1 := User{
    username:"royal"
    password:"123456"
}

u2 := User {
    username:"royal"
    password:"123456"
}

// ==
if u1 == u2 {
    ...
}

//DeeplyEquel()
if reflect.DeeplyEquel(u1, u2) {
    ...
}


package main
import (
	"fmt"
	"reflect"
)
type User struct {
	username, password string
	age, salary        int
}
func main() {
	u1 := User{"roayl", "123", 22, 123456}
	u2 := User{
		"roayl",
		"123",
		22,
		123456,
	}
	fmt.Println(reflect.DeepEqual(u1, u2), (u1 == u2))
}
/*
true true
*/
```

#### 5.2匿名结构体

- 匿名结构体

定义：没有名称的结构体。

语法：

```go
variable_name := struct {
	//成员变量
}{
	//对应的值
}
```

```go
package main

import (
	"fmt"
)

func main() {

	user := struct {
		username, password string
		age                int
	}{
		"royal", "123", 22,
	}

	fmt.Printf("%T\n", user)
	fmt.Println(user)
}
/*
struct { username string; password string; age int }
{royal 123 22}
*/
```

- 匿名字段（成员变量）：在Go结构中，允许创建匿名字段。匿名字段是那些不包含任何名称的字段，你只需要提到字段的类型，然后Go就会自动使用该类型作为字段（成员变量）的名称。如下：

```go
package main

import (
	"fmt"
)

type User struct {
	username, password string  //命名字段
	int    //匿名字段
}

func main() {
	user := User{
		"royal", "123", 55,
	}
	fmt.Printf("%s %s %d\n", user.password, user.username, user.int)  //自动使用该类型作为成员变量的名称
}
/*
123 royal 55
*/
```

#### 5.3函数用作结构体字段(不重要)

- 我们知道在Go语言中函数也是用户定义的类型，所以你可以在Go结构体中创建一个函数字段（函数成员变量）。

```go
// 作为Go结构中的字段
package main
import "fmt"
// Finalsalary函数类型
type Finalsalary func(int, int) int
//创建结构
type Author struct {
	name      string
	language  string
	Marticles int
	Pay       int
	//函数作为字段
	salary Finalsalary
}
func main() {
	// 初始化字段结构
	result := Author{
		name:      "Sonia",
		language:  "Java",
		Marticles: 120,
		Pay:       500,
		salary: func(Ma int, pay int) int {
			return Ma * pay
		},
	}
	fmt.Println("作者姓名: ", result.name)
	fmt.Println("语言: ", result.language)
	fmt.Println("五月份发表的文章总数: ", result.Marticles)
	fmt.Println("每篇报酬: ", result.Pay)
	fmt.Println("总工资: ", result.salary(result.Marticles, result.Pay))
}
/*
作者姓名:  Sonia
语言:  Java
五月份发表的文章总数:  120
每篇报酬:  500
总工资:  60000
*/
```

### 6.切片&函数（重点）

#### 6.1数组

定义：存储一组同类型的数据，缺点：**固定长度**。

- 创建数组（两种方式）：

- **方法一：使用var关键字**：

```go
var arr_name [const_length]type
//例如：定义一个长度为100的整数数组a
var a [100]int
```

```go
package main
import (
	"fmt"
)
func main() {
	var a [3]int
	for i := 0; i < len(a); i++ {
		a[i] = (i + 1) * (i + 1)
	}
	for _, v := range a {
		fmt.Printf("%d\n", v)
	}
}
/*
1
4
9
*/
```

- **方法二：简写声明，它比上面的声明更灵活**

```go
arr_name := [const_length]type{item1, item2, ...}

//定义一个长度为3的整数数组a，花括号{}不可省略，也可以部分初始化，未初始化元素的默认为0
a := [3]int{}
a := [3]int{1,2}

//无需写出长度
a := [...]int{-1, 0, 1, 0}
```

- 多维数组

```go
package main
import (
	"fmt"
)
const N = 3
func main() {
	//方式一var定义数组
	var a [N][N]int
	for i := 0; i < N; i++ {
		for j := 0; j < N; j++ {
			a[i][j] = i*N + j
		}
	}
	for k, v := range a {
		fmt.Printf("%d %d\n", k, v)
	}
	//方式二简写，部分初始化，其余默认为0
	b := [N][N]int{{1, 2, 3},
		{4, 5, 6},
	}
	for i := 0; i < N; i++ { //初始化第三行
		b[2][i] = 2*3 + i + 1
	}
	for _, arr := range b { //遍历每个一维数组
		for _, v := range arr { //遍历每个一维数组中的每一个元素
			fmt.Printf("%d\n", v)
		}
	}
}
/*
0 [0 1 2]
1 [3 4 5]
2 [6 7 8]
1
2
3
4
5
6
7
8
9
*/
```

- 在数组中，你可以使用`len()`方法获取数组的长度。
- 创建大小已确定的数组，根据其中元素的数量确定，使用省略号`...`，如下所示

```go
// 创建大小已确定的数组，根据其中元素的数量确定，使用省略号...，如下所示
a := [...]int{-1, 0, 1, 0}

a := []int{-1, 0, 1, 0}   //这种有很多问题
len(a)  //4
```

- **将一个数组用等号赋值给另一个数组时，新数组发生改变并不会影响原数组，原数组依旧不变。**

```go
package main

import (
	"fmt"
)

func main() {
	a := [...]int{-1, 0, 1, 0}
	b := a
	b[0] = 100  //对b数组改变，a数组不变
	fmt.Println(b, a)
}
/*
[100 0 1 0] [-1 0 1 0] 
*/


package main
import (
	"fmt"
)
func main() {
	var a [3]int //0 1 2
	for i := 0; i < 3; i++ {
		a[i] = i
	}
	b := a  
	b[0] = 100  //对b数组改变，a数组不变
	fmt.Println(b, a)
}
/*
[100 1 2] [0 1 2]
*/



package main
import (
	"fmt"
)
func main() {
	a := []int{-1, 0, 1, 0}   //创建的切片
	b := a
	b[0] = 100  //对b改变，a改变
	fmt.Println(b, a)
}
/*
[100 0 1 0] [100 0 1 0] 
*/
```

- 在数组中，如果数组的元素类型是可比较的，则数组类型也是可比较的。因此，**我们可以使用`==`运算符直接比较两个数组，前提是两个数组的类型和长度都必须一样，否则报错**。

#### 6.2数组复制

Golang没有提供将一个数组复制到另一个数组的特定内置函数。但是我们可以简单地通过值或引用将数组分配给新变量来创建数组的副本。

如果我们通过值创建数组的副本并在原始数组的值中进行了一些更改，则它不会反映在该数组的副本中。 而且，如果我们通过引用创建数组的副本，并对原始数组的值进行了一些更改，那么它将反映在该数组的副本中。

语法：

```go
//按值创建数组的副本，理解为两个不同的空间
arr := arr1  //类型一致

//通过引用创建数组的副本，理解为指向同一块地址，一个变，另一个跟着变
arr := &arr1  //但是他们类型不一致, arr是arr1类型的指针变量  例如 *[3]int   [3]int



package main
import (
	"fmt"
)
func main() {
	a := [3]int{1, 2, 3}
	b := &a
	b[0] = 10000000
	a[1] = 3333
	fmt.Printf("%T %T\n", b, a)  //类型不一致，不能判断==是否相等
	fmt.Println(b, a)
}
/*
*[3]int [3]int
&[10000000 3333 3] [10000000 3333 3]
*/
```

#### 6.3函数参数

```go
package main

import (
	"fmt"
)

func myfunc(a *[3]int) {
	a[0] = 11111
}
func main() {
	a := [3]int{1, 2, 3}
	myfunc(&a)
	fmt.Println(a)
}
/*
[11111 2 3]
*/


package main

import (
	"fmt"
)

func myfunc(a [3]int) {
	a[0] = 11111
}
func main() {
	a := [3]int{1, 2, 3}
	myfunc(a)
	fmt.Println(a)
}
/*
[1 2 3]
*/
```

#### 6.4切片(Slice)

在Go语言中，切片比数组更强大，灵活，方便，并且是轻量级的数据结构。slice是一个可变长度序列，用于存储相同类型的元素，不允许在同一slice中存储不同类型的元素。就像具有索引值和长度的数组一样，但是切片的大小可以调整，切片不像数组那样处于固定大小。在内部，切片和数组相互连接，切片是对基础数组的引用。允许在切片中存储重复元素。***切片中的第一个索引位置始终为0，而最后一个索引位置将为（切片的长度– 1）\***。

- **切片声明（创建切片）：跟数组一样，但是有一个区别，即不允许您在方括号[]中指定切片的大小。因此它的长度可以根据需要增长或缩小。**

```go
//利用var
var a []int
//简写, 注意：切记，当您使用int数字创建切片时，它首先创建一个数组，然后返回对其的切片引用。
a := []int{1,2,3}
var a = []int{1,2,3}
```

- **切片的组成：**

- - **指针：**指针用于指向可通过切片访问的数组的第一个元素。在这里，指向的元素不必是数组的第一个元素。
- - **长度：**长度是数组中存在的元素总数。
- - **容量：**容量表示可以扩展的最大大小。

```go
// 切片
package main

import "fmt"

func main() {

	//创建一个数组
	arr := [7]string{"这", "是", "Golang", "基础", "教程", "在线", "111"}

	//显示数组
	fmt.Println("数组:", arr)

	//创建切片
	myslice := arr[1:6]

	//显示切片
	fmt.Println("切片:", myslice)

	//显示切片的长度
	fmt.Printf("切片长度: %d", len(myslice))

	//显示切片的容量
	fmt.Printf("\n切片容量: %d\n", cap(myslice))

	fmt.Printf("%T", myslice)
}
/*
数组: [这 是 Golang 基础 教程 在线 111]
切片: [是 Golang 基础 教程 在线]
切片长度: 5
切片容量: 6
[]string
*/

/*
用法解释：在上面的实例中，我们从给定的数组中创建一个切片。这里，片的指针指向索引1，因为片的下界被设置为1，所以它开始访问来自索引1的元素。切片的长度为5，表示切片中元素的总数为5，而切片6的容量表示最多可以存储6个元素。
*/
```

- **切片是数组的引用，也可以从给定的切片中创建切片**

```go
arr_name[low:high]  //切取数组下标从[low, high)之间的元素作为一个切片返回
slice_name[low:high]

//举例：
//创建切片
a := []int{90, 60, 40, 50, 34, 49, 30}
a[0:]
a[:]
a[1:2]
```

- **使用make()函数：**您还可以使用go库提供的*make()函数*创建切片。此函数采用三个参数，即类型，长度和容量。在此，容量值是可选的。它为底层数组分配的大小等于给定的容量，并返回一个切片，该切片引用底层数组。通常，make()函数用于创建一个空切片。在这里，空切片是包含空数组引用的那些切片。









### 7.字符串(String)（重点）
