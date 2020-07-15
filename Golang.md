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


##编译
> go build
> xxx.exe

> go build main.go
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

Go语言里的字符串的内部实现使用 UTF-8 编码

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

//包含关系
name := "cheng lihui"
fmt.Println(strings.Contains(name, "cheng"))
fmt.Println(strings.Contains(name, "test"))

//前缀后缀
name := "chenglihui"
fmt.Println(strings.HasPrefix(name, "cheng"))
fmt.Println(strings.HasSuffix(name, "ui"))

//字串出现的位置
name := "chenglihui"
fmt.Println(strings.Index(name, "a"))  //找不到 -1
fmt.Println(strings.Index(name, "c"))  //0 索引位置从0开始
fmt.Println(strings.LastIndex(name, "i"))//最后一次出现i的位置

//join操作，切片连接起来
strings.(a[] string, sep string)
~~~



**byte和rune类型**

组成每个字符串的元素叫做“字符”，可以通过遍历或者单个获取字符串元素获得字符。字符用单引号包裹起来，如：

~~~go
var a := '中'
var b := 'x'
~~~

Go语言的字符有以下两种：

1、uint8类型，或者叫byte型，代表了ASCII码的一个字符；

2、rune类型，代表一个UTF-8字符。

~~~go

//byte和rune类型
//Go语言中为了处理非ASCII码类型的字符，定义了新的rune类型
//英文字符是 byte
//其他语言字符是 rune类型

s := "Hello意难平"
// len()求的是byte字节的数量
n := len(s)
fmt.Println(n) //值：14

//for i := 0; i < len(s); i++  {
//	fmt.Printf("%c\n", s[i])  // %c：字符，但是这里中文会变成“乱码”
//}

for _, c := range s { //从字符串中拿出具体的字符
    fmt.Printf("%c\n", c)
}
~~~



**修改字符串**

要修改字符串，需要先将其转换成 []rune 或 []byte，完成后再转换为 string。无论哪种转换，都会重新分配内存，并复制字节数组。

~~~go
func changeString() {
    s1 := "big"
    byteS1 := []byte(s1)
    byteS1[0] = 'p'
    fmt.Println(string(byteS1))
    
    s2 := "白萝卜"
    runeS2 := []rune(s2) //强制转换成切片
    runeS2[0] = '红' //改成 字符，如用双引号会变成红线不能编译
    fmt.Println(string(runeS2))
}
~~~

~~~go
s1 := "程"  //string
s2 := '程'  //rune(int32)
fmt.Printf("s1:%T s2:%T", s1, s2)

> go run main.go
s1:string    s2:int32
~~~



if判断

~~~go
if 表达式1 {}
   ...
} else if 表达式2 {
   ...
} else {
   ...
}

//例子1
age := 19
if age > 18 {
    fmt.Println("成年了")
} else {
    fmt.Println("去写作业")
}

//例子2
if age := 19; age > 18 {
    fmt.Println("成年了")
} else {
    fmt.Println("去写作业")
}
//fmt.Println(age) 作用域不到这里，飘红不让编译通过
~~~



for循环

~~~go
//第一种：基本格式
for i := 0; i < 10; i++ {
    fmt.Println(i)
}

//变种1
i := 0
for ; i < 10; i++ {
   fmt.Println(i) 
}

//变种2
i := 0
for ; i < 10; {
   fmt.Println(i)
   i++
}

无限循环
for {
    循环语句
}
for循环可以通过 break、goto、return、panic 语句强制退出循环
~~~



**for range(键值循环)**

Go语言中可以使用 for range 遍历数组、切片、字符串、map及通道 channel。

1、数组、切片、字符串返回索引和值

2、map返回键和值

3、通道(channel)只返回通道内的值

~~~go
s := "cheng天明"
for i,v := range s {
    fmt.Printf("%d %c\n", i, v)
    //汉字占了三个位置，索引会跳跃的
}

>go run main.go
0 c
1 h
2 e
3 n
4 g
5 天  //跳跃
8 明
~~~



练习

~~~go
//九九乘法表
for i := 1; i < 10; i++ {
	for j := 1; j <= i; j++ {
		fmt.Printf("%d * %d = %d\t", j, i, i * j)
	}
	fmt.Println()
}
~~~

~~~go
//单行注释

/**多行注释
多行注释
*/

变量声明
1、var name1 string
2、var name2 = "chenglh"
3、函数内部声明 ： name3 := "test"

匿名变量(哑元变量)
s := "hello"
for i,v := range s { // key , value
   fmt.Printf("%d , %c \n", i, v)
}
for i := range s { // key 只有一个变量是默认是 索引
   fmt.Printf("%d\n", i)
}
for _,v := range s { // 不需要key , 只要value
   fmt.Printf("%c \n", v)
}

continue 继续下一次循环
for i := 0; i <10; i++ {
    if (i == 5) {
        continue
    }
}

基本数据类型
//整型
无符号：uint8,uint16,uint32,uint64
带符号：int8,int16,int32,int64

uint和int：具体是32位还是64位要看操作系统
uintptr：表示指针

Go语言中没办法直接定义二进制数
var n1 = 0777
var n2 = 0xff

//浮点型
float32和float64
Go语言中浮点数默认是float64

//布尔值
true和false
不能和其他的类型做转换

//字符串
常用方法
字符串不能修改

//byte和rune类型
都属于类型别名

//字符串、字符、字节
字符串：双引号
字符：单引号，单个字母、单个符号、单个文字
字节：1byte=8bit
utf-8中，一个常用汉字 一般占用3个字节

//常量
const PI = 3.14159
const UserNotExistErr = 10000

iota：实现枚举
三个要点：
1、iota在const关键字出现时将被重置为0
2、const中每新增一行常量声明，iota累加1
~~~



**switch**

~~~go
//switch
var n1 = 10
switch n1 {
    case 1:
    	fmt.Println("this is 1")
    case 2:
    	fmt.Println("this is 2")
    default:
    	fmt.Println("this is nothing")
}

//变种
switch n := 3; n { //先赋值然后判断
    case 1:
    	fmt.Println("这是1")
    case 2:
    	fmt.Println("这是2")
    default:
    	fmt.Println("不确定")
}

//判断
age := 18
switch {
    case age < 18:
    	fmt.Println("未成年")
    case age > 18 && age < 60: 
        fmt.Println("好好工作")
    default:
    	fmt.Println("好好生活")
}
~~~



**数组**

~~~go
package main

import "fmt"

func main()  {
	//数组
	//存放元素的容器
	//必须指定存放的元素的类型和容量(即长度)
	var a1 [3]bool //[false false false]
	var a2 [4]bool //[false false false false]

	fmt.Printf("a1：%T，a2：%T\n", a1, a2)

	//数组的初始化
	//默认元素都是零值(布尔值false，整形和浮点型都是0，字符串是"")
	fmt.Println(a1, a2)

	//初始化方式1
	a3 := [3]bool{true, false, true}
	fmt.Println(a3)

	//初始化方式2
	//根据初始值自动推断数组的长度
	a6 := [...]int{0,1,2,3,4,5}
	fmt.Println(a6)

	//初始化方式3
	//a5 := [5]int{1,2} 后面默认值0
	//根据索引指定来声明
	a5 := [5]int{0:1,4:3}
	fmt.Println(a5)
}
~~~



**数据遍历**

~~~go
//一维数组遍历
city := [...]string{"北京","上海","深圳"}
//1、根据索引遍历
for i := 0; i < len(city); i++  {
	fmt.Println(city[i])
}

//2、for range遍历
for i,v := range city{
	fmt.Println(i,v)
}

//多维数组
//[[1,2] [3,4] [5,6]]
var all = [3][2]int{
    [2]int{1,2},
    [2]int{3,4},
    [2]int{5,6},
}

fmt.Println(all)
for _,v := range all{
    for _,vv := range v{
        fmt.Println(vv)
    }
}
~~~



**切片**

数组的局限性，因为数组的长度是固定的并且数组长度属于类型的一部分。

~~~go
func arraySum(x [3]int) int{
    sum := 0;
    for _,v := range x{
        sum += v
    }
    return sum
}

这个求各函数只能接受 [3]int类型，其他的都不支持

a := [3]int{1,2,3}
只要定义了a数组，就不能再继续往数组a中添加新元素了
~~~

> 切片

切片(slice)是一个拥有相同类型元素的`可变长度的序列`。它是基于数组类型做的一层封装。它非常灵活，支持自动扩容。

切片是一个"引用类型"，它的内部结构包含 地址、长度和容量。切片一般用于快速地操作一块数据集合。

~~~go
package main

import "fmt"

//切片slice
func main()  {
	//切片定义
	var s1 []int    //定义一个存放int类型元素的切片
	var s2 []string //定义一个存放string类型元素的切片
	fmt.Println(s1, s2)
    fmt.Println(s1==nil)  //true
	fmt.Println(s2==nil)  //true
    //fmt.Println(s1==s2) 飘红报错，切片是引用类型，不支持直接比较，只能和nil比较

	//初始化
	s1 = []int{1,2,3}
	s2 = []string{"广东","广州","天河"}
	fmt.Println(s1, s2)
}
~~~



**切片的长度和容量**

切片拥有自己的长度和容量，可以通过内置的len()函数求长度，使用内置的cap()函数求切片的容量。

~~~go
package main

import "fmt"
//切片slice

func main()  {
	//切片定义
	var s1 []int    //定义一个存放int类型元素的切片
	var s2 []string //定义一个存放string类型元素的切片
	//fmt.Println(s1, s2)
	//fmt.Println(s1==nil)
	//fmt.Println(s2==nil)

	//初始化
	s1 = []int{1,2,3}
	s2 = []string{"广东","广州","天河"}
	fmt.Println(s1, s2)
    //计算-长度和容量
	fmt.Printf("s1:len:%d s1:cap:%d\n", len(s1), cap(s1))
	fmt.Printf("s2:len:%d s2:cap:%d\n", len(s2), cap(s2))
    
    //2、由数组得到切片
	s1 := [...]int{1,3,5,7,9,11}
	s2 := s1[0:4] //基于一个数组切割，左包含右不包含，(左闭右开) [1 3 5]
	fmt.Println(s2)

	s3 := s1[1:6]  // [3 5 7 9 11]
	fmt.Println(s3)
    
    s4 := s1[:4]  // [0:4]
	s5 := s1[3:]  // [3:最后即len(s1)]
	s6 := s1[:]  //  [0:最后即len(s1)]
	fmt.Println(s4, s5, s6)
}

>go run main.go
[1 2 3] [广东 广州 天河]
s1:len:3 s1:cap:3
s2:len:3 s2:cap:3
~~~



> 切片的本质

切片就是一个框，框住了一块连续的内存。属于引用类型

切片的本质就是对底层数组的封装，它包含了三个信息：底层数组的指针、切片的长度（len）和切片的容量（cap）。

举个例子，现在有一个数组a := [8]int{0, 1, 2, 3, 4, 5, 6, 7}，切片s1 := a[:5]，相应示意图如下

<img src="H:\笔记本\Golang.assets\image-20200714155741790.png" alt="image-20200714155741790" style="float:left;" />



切片s2 := a[3:6]，相应示意图如下：

<img src="H:\笔记本\Golang.assets\image-20200714155917610.png" alt="image-20200714155917610" style="float:left;" />

> 判断切片是否为空

要检查切片是否为空，请始终使用len(s) == 0来判断，而不应该使用s == nil来判断

~~~go
s1 := [...]int{1,3,5,7,9,11}

//切片是引用类型，都指向了底层的一个数组，如果底层数组修改了，其他切片数据读取也是跟着改变的
s4 := s1[:4]  // [0:4]
s1[2] = 1200
fmt.Println(s4) // [1 3 1200 7]
~~~



**make()函数创造切片**

~~~go
make([]T, size, cap)

//T:切片的元素类型
//size:切片中元素的数量
//cap:切片的容量

func main() {
	a := make([]int, 2, 10)
	fmt.Println(a)      //[0 0]
	fmt.Println(len(a)) //2
	fmt.Println(cap(a)) //10
}

//上面代码中a的内部存储空间已经分配了10个，但实际上只用了2个。 容量并不会影响当前元素的个数，所以len(a)返回2，cap(a)则返回该切片的容量。
~~~



> 切片不能直接比较

切片之间是不能比较的，我们不能使用==操作符来判断两个切片是否含有全部相等元素。 切片唯一合法的比较操作是和nil比较。 

一个nil值的切片并没有底层数组，一个nil值的切片的长度和容量都是0。但是我们不能说一个长度和容量都是0的切片一定是nil

~~~go
var s1 []int         //len(s1)=0;cap(s1)=0;s1==nil
s2 := []int{}        //len(s2)=0;cap(s2)=0;s2!=nil  有初始化，已开辟内存空间
s3 := make([]int, 0) //len(s3)=0;cap(s3)=0;s3!=nil
~~~

**切片的赋值**

~~~go
//切片的赋值拷贝
s3 := []int{1, 3, 5}
s4 := s3  //s3 、s4指向同一个底层数组
fmt.Println(s3, s4) // [1 3 5]  [1 3 5]
s3[0] = 1200
fmt.Println(s3, s4) // [1200 3 5] [1200 3 5]
~~~

**切片遍历**

~~~go
func main() {
	s := []int{1, 3, 5}

    //方法1：索引数组遍历
	for i := 0; i < len(s); i++ {
		fmt.Println(i, s[i])
	}

    //方法2：for range循环
	for index, value := range s {
		fmt.Println(index, value)
	}
}
~~~

**append**

~~~go
package main

func main() {
    s := []string{"北京", "上海", "深圳"}
    //s[3] = "广州"  //索引溢界，错误写法，会导致编译错误
    fmt.Printf("s=%v \t len(s)=%d \t cap(s)=%d\n", s, len(s), cap(s))
    
    //调用append函数必须用原来的切片变量来接收返回值
    //名字不变，但是内存地址已变化
    s = append(s, "广州")//append追加元素，原来的底层数组放不下的时候，Go底层就会把底层数组换一个位置
    fmt.Printf("s=%v \t len(s)=%d \t cap(s)=%d\n", s, len(s), cap(s))
}
~~~















https://www.bilibili.com/video/BV14C4y147y8?p=14





https://www.liwenzhou.com/posts/Go/06_slice/