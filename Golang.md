```php
##【老男孩教育】GO语言 基础+就业班 #新版
https://www.bilibili.com/video/BV1QJ411V73q/?p=3

##2020老男孩系列之掌握Go语言
https://www.bilibili.com/video/BV14C4y147y8?p=8

##【老男孩教育】GO语言 基础+就业班 #新版
https://www.bilibili.com/video/BV1QJ411V73q/?spm_id_from=333.788.videocard.0
https://www.bilibili.com/video/BV1QJ411V73q/?spm_id_from=333.788.videocard.1
```

##### Day01 Golang入门

###### 1.1 Go语言基础

```
什么是Go语言
1、Google开源
2、编译型语言(C、Go等)
(php、Python解析型语言)
3、21世纪的C语言
天生支持并发
执行性能好
真正的企业级编程语言(Java、Go)

Go语言特点
1、语法简洁(25个关键字)
2、开发效率高(自带gc)
3、执行性能好
```

解析型语言与编译型语言区别

<img src="H:\笔记本\Golang.assets\image-20200525220721506.png" alt="image-20200525220721506" style="float:left;" />

![](H:\笔记本\Golang.assets\image-20200525221233192.png)

```bash
#百度内部Go语言应用
百度流量入口BFE
自动驾驶
百度智能小程序
百度APP
...

#腾讯内部Go语言应用
开源运维平台蓝鲸
开源微服务架构TarsGo
云平台
...

#知乎使用Go语言重构(Py转Go)
节约80%的服务器资源
协作开发效率高
Go成为内部推荐语言
...
```

![image-20200525222605019](H:\笔记本\Golang.assets\image-20200525222605019.png)

```go
package main //main就是说能打包成一个可执行的exe文件

//导入语句(包)
import "fmt"

//函数外只能放置标识符(变量\常量\函数\类型)的声明

//程序的入口
func main() {
    fmt.Println("人生苦短，Let's Go!")
}
```



###### 1.2 开发环境

```bash
##1、Golang下载安装
https://golang.org/dl		#官网地址
https://golang.google.cn/dl	#官方镜像(推荐)

##2、安装
双击一步步安装即可

##3、验证(cmd或Git命令面板)
#查看版本信息
$ go version
go version go1.14.2 windows/amd64

#查看Go环境参数
$ go env
set GO111MODULE=
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\Administrator\AppData\Local\go-build
set GOENV=C:\Users\Administrator\AppData\Roaming\go\env
set GOEXE=.exe
set GOFLAGS=
...

##4、开发环境

```

```go
/**
 * 1、旧版本需要设置GOPATH，即代码的存放路径
 * 在系统属性 >>> 新建系统变量 >>> 变量名：GOPATH ; 变量值：E:\Go
 * 2、在 E:\Go目录下新建三个文件夹
 * bin 存放编译后生成的可执行文件
 * pkg 存放编译后生成的归档文件
 * src 存放源码文件
 * 3、E:\Go\bin也配置到环境变量中，放在PATH后面即可
 * 4、本机电脑上GOPATH应该是有默认值的，通常是%USERPROFILE%/go，需要删除按以上方法新建即可
 */

#配置完成后，重新打开新的cmd
> go env
...
set GOPATH=G:\Go    //项目路径
set GOROOT=c:\go    //安装go软件的路径
...
```

|  平台   |   GOPATH默认值   |        举例        |
| :-----: | :--------------: | :----------------: |
| windows | %USERPROFILE%/go | c:\Users\用户名\go |
|  unix   |     $HOME/go     |  /home/用户名/go   |



1、适合个人开发者，组织代码

<img src="H:\笔记本\Golang.assets\image-20200525220721511.png" style="zoom:80%;float:left;" />

2、目前流行的项目结构
<img src="H:\笔记本\Golang.assets\image-20200525220721500.png" style="zoom:110%;float:left;" />





















3、企业项目组织代码
<img src="H:\笔记本\Golang.assets\20200526140839.png" style="zoom:200%;" />

Go采用 Utf-8编码的文本，下载安装 vscode

[官方下载](https://code.visualstudio.com/Download )

1、打开软件，在左侧 “扩展” > 搜索 chinese 汉化插件包 ，重启软件

2、再次 “扩展” > 搜索 go 扩展插件，让软件支持Go语言开发


第一个小程序Hello world

```go
# 1、在vscode中打开文件路径 G:\Go\src\www.testgo.com\day01\helloworld
需要创建对应路径

# 2、新建 main.go文件
package main
import "fmt"

func main()  {
	fmt.Println("Hello world!")
}
```



###### 1.3 项目编译

命令行：go build

1、在项目目录下执行go build

2、在其他路径下执行go build，需加上项目路径(从GOPATH/src后边写起)，编译后可执行文件保存在当前目录下

3、可以使用`-o`参数来指定编译后得到的可执行文件的名字

```bash
##方式一
#day01右键  》  终端中打开 / open in Terminal
#右上处，选择cmd，如果没有出现cmd选项，点开默认shell找到cmd
#其实是进入命令面板

#构建
G:\Go\src\www.testgo.com\day01\helloworld>go build
helloworld.exe

#运行
G:\Go\src\www.testgo.com\day01\helloworld>helloworld.exe
Hello world!

##方式二
cd E:\www
go build www.testgo.com/day01/helloworld
helloworld.exe

##方式三
> go build -o hello.exe
hello.exe
```

```php
#常用命令
> go build     	  //构建项目
> go run main.go  //运行项目

> go install
//1、先编译得到exe；
//2、把可执行文件拷贝到"GOPATH/bin"目录下。任意地方直接执行当前可执行文件
```

> 跨平台编译

`go build`生成可执行文件都是当前操作系统可执行的文件，如果想在windows下编译一个linux下可执行文件，那需要怎么做呢？

只需要指定目标操作系统的平台和处理器架构即可 (cmd界面下)

```
> SET CGO_ENABLED=0  // 禁用CGO，因为使用cgo的代码是不支持跨平台编译的
> SET GOOS=linux     // 目标平台是linux
> SET GOARCH=amd64   // 目标处理器架构是amd64
> go build
```

Mac下编译Linux和Windows平台64位可执行程序

```
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build
```

Linux下编译Mac和Windows平台64位可执行程序：

```
CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build
```

Windows下编译Mac平台64位可执行程序：

```
SET CGO_ENABLED=0
SET GOOS=darwin
SET GOARCH=amd64
go build
```


代码格式化

```
> go fmt main.go
```



###### 1.4 变量常量

Go语言中有25个关键字

```go
break		default		func		interface	select
case		defer		go			map			struct
chan		if			else		goto		switch
package		const		range		type		fallthrough
continue	for			import		return		var
```

Go语言37个保留字

```go
Constants:	true  false  iota  nil

Types:		int  int8  int16  int32  int64  
            uint  uint8  uint16  uint32  uint64  uintptr
            float32  float64  complex128  complex64
            bool  byte  rune  string  error

Functions:  make  len  cap  new  append  copy  close  delete
            complex  real  imag
            panic  recover
```



**变量**
Go语言中的变量必须`先声明后才能使用`，同一作用域内不支持重复声明，并且Go语言中的变量声明后必须使用

> 标准声明

```go
// var 变量名  变量类型
var age int
var name string
var isok bool
```

> 批量声明

~~~go
var (
    //Go语言中推荐使用驼峰式命名
    studentName  string
    studentAge   int
    studentState bool
)
~~~



~~~go
package main
var (
	name string
    age  int
    isok bool
)

func main() {
    name = 'chengl'
    age  = 16
    isok = false
}
~~~

以上代码如果有错位或乱放位置，在控制台中 > go fmt main.go 可以格式化重排代码。

~~~go
package main

import "fmt"

var (
	name string
	age  int
	isok bool
)

func main() {
	name = "chenglh"
	age = 16
	isok = false

	fmt.Printf("name:%s, age:%d", name, age) //打印信息
	fmt.Print(isok) //打印结果
    fmt.Println() //快捷打印一个空行
    fmt.print("\n")
}

##控制台：
> go build
> day01.exe
~~~



> 变量的初始化

Go语言在声明变量的时候，会自动对变量对应的内存区域进行初始化操作。

~~~php
##例如：
整型和浮点型变量的默认值为0

字符串变量的默认值为空字符串

布尔型变量默认为false

切片、函数、指针变量的默认为nil
~~~



> 变量初始化标准格式

~~~go
//声明变量的同时赋值
//var 变量名 类型 = 表达式

//举例子
//单个变量声明
var name string = "chenglh"
var age int = 18

//多个变量声明
var name, age = "chenglh", 18
~~~



> 类型推导

有时候将变量的类型省略，编译器会根据等号右边的值来推导变量的类型完成初始化

~~~go
//根据值来判断变量是什么类型
var name = "chenglh"
var age = 18
~~~

> 短变量声明(函数内部)

只在函数内部使用，通过 `:=` 方式声明并初始化变量

~~~go
package main
import ("fmt")

//全局变量m
var m = 100

func main() {
    //简短变量声明
    s3 := "haha" //使用了类型推导
    n  := 10
    m  := 200
    fmt.Println(m, n)
}
~~~



> 匿名变量

在使用多重赋值时，如果想要忽略某个值，可以使用`匿名变量（anonymous variable）`。 匿名变量用一个下划线`_`表示，例如：

```go
func foo() (int, string) {
	return 10, "chenglh"
}
func main() {
	x, _ := foo()
	_, y := foo()
	fmt.Println("x=", x)
	fmt.Println("y=", y)
}
```

匿名变量不占用命名空间，不会分配内存，所以匿名变量之间不存在重复声明。 (在`Lua`等编程语言里，匿名变量也被叫做哑元变量。)

> 注意事项：

1. 函数外的每个语句都必须以关键字开始（var、const、func等）
2. `:=`不能使用在函数外。
3. `_`多用于占位，表示忽略值。
4. <u>同一个作用域中，不能`重复声明`同名的变量</u>

~~~go
//例子
package main
import "fmt"

//声明变量
/*
var name string
var age int
var isOk bool */

//批量声明
var (
	name string
    age  int
    isOk bool
)

var (
	//student_name string
	studentName  string //使用驼峰式，上面和下面的声明，编辑器会有小波浪线
	//StudentName  string
)

func main() {
	//赋值
	name = "chenglh"
	age = 16
	isOk = true

	fmt.Print(isOk) //在终端打印字符
	fmt.Printf("my name is:%s", name) // %s占位符
	fmt.Printf("my age is:%d", age)   // %d
	fmt.Println("this end")   // 输出+换行符
	fmt.Println() //打印一个空行

    //Go语言中，非全局变量声明后必须使用，不使用就编译不过去
    //var testHeihei string
    //testHeihei = "测试使用"
    //fmt.Print(testHeihei)

    //声明变量同时赋值
	//var setStudentName string = "chenglh"
    //var setStudentName = "chenglh"  //推荐使用，类型推导(根据值判断该变量是什么类型)
	//fmt.Print(setStudentName)
}
~~~



**常量**

相对于变量，常量是恒定不变的值，多用于定义程序运行期间不会改变的那些值，常用于全局。 常量的声明`const`，常量在定义的时候必须赋值。

~~~go
package main

//常量，一经定义后，程序运行期间不能改变

//单个常量声明
//const pi = 3.141529
//const e = 2.7182

//批量常量声明
const (
    pi = 3.141529
    e = 2.7182
)

//const同时声明多个常量时，如果省略了值则表示和上面一行的值相同。
const (
    n1 = 100
    n2
    n3
    // 相当于常量`n1`、`n2`、`n3`的值都是100
)
~~~



**iota**

`iota`是go语言的常量计数器，只能在常量的表达式中使用。

`iota`在`const关键字出现时将被重置为0`。const中每新增一行常量声明将使`iota`计数一次(iota可理解为const语句块中的行索引)。 使用iota能简化定义，在定义枚举时很有用。

~~~go
//举个例子
const (
	n1 = iota //0
	n2        //1，默认是 n2 = iota
	n3        //2
	n4        //3
)
~~~



> 几个常见iota例子

~~~go
//例1：使用`_`跳过某些值
const (
	n1 = iota //n1 = 0
	n2        //n2 = iota => n2 = 1
	_		  //_  = iota => iota = 2
	n4        //n4 = iota => n4 = 3
)

//例2：`iota`声明中间插队
const (
	n1 = iota //n1 = 0
	n2 = 100  //n2 = 100   iota计算器为1
	n3 = iota //n3 = iota => n3 = 2
	n4        //n4 = iota => n4 = 3
)
const n5 = iota //n5 = 0

//例3：多个`iota`定义在一行
const (
	a, b = iota + 1, iota + 2 //a = 0 + 1   ; b = 0 + 2    结果：1 2
	c, d                      //c = iota + 1; d = iota + 2 结果：2,3
	e, f                      //e = iota + 1; f = iota + 2 结果：3,4
)

//例4：定义数量级
const (
	_  = iota
	KB = 1 << (10 * iota)
	MB = 1 << (10 * iota)
	GB = 1 << (10 * iota)
	TB = 1 << (10 * iota)
	PB = 1 << (10 * iota)
    /** <<表示左移操作，1<<10表示将1的二进制表示向左移10位，也就是由1变成了10000000000，
    也就是十进制的1024*/
)
~~~



###### 1.5 数据类型

基本数据类型：**整型、浮点型、布尔型、字符串外，数组、切片、结构体、函数、map、通道（channel）等**



> 整型分两大类，带符号整型：int8、int16、int32、int64；无符号整型：uint8、uint16、uint32、uint64

|  类型  | 描述                                                         |
| :----: | ------------------------------------------------------------ |
|  int8  | 有符号8位整型 (-128 到 127)                                  |
| int16  | 有符号16位整型 (-32768 到 32767)                             |
| int32  | 有符号 32位整型 (-2147483648 到 2147483647)                  |
| int64  | 有符号 64位整型 (-9223372036854775808 到 9223372036854775807) |
| uint8  | 无符号 8位整型 (0 到 255)                                    |
| uint16 | 无符号 16位整型 (0 到 65535)                                 |
| uint32 | 无符号 32位整型 (0 到 4294967295)                            |
| uint64 | 无符号 64位整型 (0 到 18446744073709551615)                  |

> 特殊整型(注意：越界或溢出)

| 类型    | 描述                                               |
| :------ | :------------------------------------------------- |
| uint    | 32位操作系统上就是uint32，64位操作系统上就是uint64 |
| int     | 32位操作系统上就是int32，64位操作系统上就是int64   |
| uintptr | 无符号整型，用于存放一个指针                       |

> 数字字面量语法（Number literals syntax）

Go1.13版本之后引入了数字字面量语法，这样便于开发者以二进制、八进制或十六进制浮点数的格式定义数字。



**八进制**

Go语言中无法直接定义二进制数，关于八进制和十六进制的示例如下：

~~~go
func main()  {
	//十进制
	var a = 10
	fmt.Printf("%d \n", a) // 10
	fmt.Printf("%b \n", a) // 1010

    //八进制 以0开头(文件权限)
	var b = 077
	fmt.Printf("%o \n", b) // 77

    //十六进制 以0x开头(内存地址)
	var c = 0xff
	fmt.Printf("%x \n", c) // ff
	fmt.Printf("%X \n", c) // FF
    
    var d = int8(9)
	fmt.Printf("%T\n", d)
}
~~~



**浮点型**

~~~go
package main
import "fmt"

func main()  {
	//小数默认类型，float64
	f1 := 1.234568
	fmt.Printf("%T\n", f1)

	//显式声明类型
	f2 := float32(1.23552)
	fmt.Printf("%T\n", f2)
    
    //float32类型的值不能直接赋值给 float64 ,如错误用法：f1 = f2
}
~~~



**布尔值**

Go语言中 bool类型进行声明布尔型数据，只有 true和false两个值

注意：

1、布尔值变量默认值为 false

2、Go语言中不允许将整型强制转换为布尔型

3、布尔型无法参与数据运算，也无法与其他类型进行转换

```go
package main

import "fmt"

func main()  {
	var a1 = true
	var a2 bool

	fmt.Printf("a1类型：%T, val=%v", a1, a1)
	fmt.Println()
	fmt.Printf("a2类型：%T, val=%v", a2, a2)
}

/**输出结果
 * a1类型：bool, val=true
 * a2类型：bool, val=false
 */
```



**类型总结**

~~~go
package main
import "fmt"

func main()  {
	num := 100
	fmt.Printf("%T\n", num) //类型
	fmt.Printf("%v\n", num) //万能输出值
	fmt.Printf("%b\n", num) //二进制
	fmt.Printf("%o\n", num) //八进制
	fmt.Printf("%d\n", num) //十进制
	fmt.Printf("%x\n", num) //十六进制

	name := "chenglh"
	fmt.Printf("%s\n", name)
	fmt.Printf("%v\n", name)
	fmt.Printf("%#v\n", name) //会自动加上双引号
}
~~~



**字符串**

字符串：双引号

字符：单引号   //只能是一个字符/英文/汉字/符号

~~~go
s := "hello world"
a := '程'   //如果再写多一个字符就标红报错
fmt.Println(s)
fmt.Println(a)  //对应是的24352 ASCI码
~~~

**字符串转义**

| 转义符 | 含义                             |
| ------ | -------------------------------- |
| \r     | 回车符(返回行首)                 |
| \n     | 换行符(跳到下一行的同一行列位置) |
| \t     | 制表符                           |
| \\'    | 单引号                           |
| \\"    | 双引号                           |
| \\\    | 反斜杠                           |

~~~go
//s := "I'm ok"
//fmt.Print(s)

//多行原样输出字符串
s2 := `
东风恶
人情薄
雨送黄昏花易落
`
fmt.Print(s2)

s3 := `G:\Go\src\www.testgo.com\day03\fmt`  //原样
s4 := "G:\\Go\\src\\www.testgo.com\\day03\\fmt" //需要转义
~~~



**字符串操作**

~~~go
//字符串长度
s1 := "chenglh"
fmt.Print(len(s1))

//字符串拼接
s2 := "cheng"
s3 := " li hui"
ss := s2 + s3
fmt.Println(ss)  //第一种：使用 + 连接
fmt.Printf("%s%s\n", s2, s3) //第二种，格式化输入
ss1 := fmt.Sprintf("%s%s", s2, s3)//第三种，Sprintf格式化后返回值给变量
fmt.Println(ss1)

//字符串分割
//email := "abc@a.com"
//emailS := strings.Split(email, "@")
//fmt.Println(emailS) //[abc a.com]

s1 := "G:\\aaa\\bb\\ccc"
ss := strings.Split(s1, "\\")
fmt.Println(ss)



~~~



https://www.bilibili.com/video/BV14C4y147y8?p=14