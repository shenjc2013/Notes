#### Day05

###### 5.1 复习

> 结构体

~~~go
type person struct{
    name string
    age  int
}
~~~



> 构造函数

~~~go
type fb struct {
	x int
	y int
}
type person struct {
    name string
    age int
}
//构造函数
func newPerson(n string, i int) person {//person就是一种类型
    return person{
        name : n,
        age  : i,
    }
}
func main()  {
    //匿名结构体，多是临时使用
	var a = struct {
		x int
		y int
	}{10, 20} //后面是赋值
	fmt.Println(a) //{10, 20}

	var b = fb {
		x : 30,
		y : 40,
	}
	fmt.Println(b) //{30, 40}
    
    var p1 person
    p1.name = "chenglh"
    p1.age = 19
    //var p1 = person{"chenglh",19}
    fmt.Printf("%#v", p1) //main.person{name:"chenglh", age:19}
    
    p2 := newPerson("nzha", 25) //通过构造函数调用
}
~~~



> 方法和接收者

~~~go
type person struct {
    name string
    age int
}

//1、对象接收者
//只有person类型才能调用
func (p person) dream(str string) {
    fmt.Printf("%s的梦想是%s", p.name, str)
}
//2、指针接收者，实际使用建议，统一使用同一类型，如指针接收者
func (p *person) nextYear() {
    p.age++ 
}

func main() {
    var p2 = person{"chenglh",19}
    p2.dream("学好Go语言")
    p2.nextYear() //结构体是值类型，修改值需要使用指针
}
~~~



> 结构体嵌套



> 结构体匿名字段



> JSON序列化与反序列化



###### 5.2 作业



##### 5.3 接口(interface)

> 接口是一种类型，是特殊的类型，它规定了变量有哪些方法

**接口定义**

~~~go
type 接口名 interface {
    方法名1(参数1...)(返回值1....)
    方法名2(参数2...)(返回值2....)
}
~~~

**接口的实现**

一个变量如果实现了接口中规定的所有方法，那么这个变量就实现了这个接口，可以称这个接口类型的变量

~~~go
type animal interface{
    move()
    eat(string) //eat(food string)
}
type cat struct {
    name string
    feet int8
}
func (c cat)move() { fmt.Println("走猫步！") }
func (c cat)eat(food string) { fmt.Printf("猫吃%s！\n", food) }

type chicken struct {
    feet int8
}
func (c chicken)move() { fmt.Println("鸡动！") }
func (c chicken)eat(food string) { fmt.Printf("鸡吃饲料！\n", food) }

func main() {
    var a1 animal //定义一个animal接口类型的变量
    
    bc := cat{//定义一个cat类型的变量bc
        name:"淘气"
        feet:4
    }
    a1 = bc  //接口变量分为：类型和值两部分
    fmt.Println(a1)
    fmt.Printf("%T", a1)
    a1.eat("小黄鱼")
    
    kfc := chicken{
        feet:2,
    }
    a1 = kfc
    fmt.Printf("%T", a1)
}
~~~

<img src="./Golang2.assets/image-20200804162045881.png" alt="image-20200804162045881" style="zoom:50%;float:left" />



举个例子

~~~go
type cat struct {}
type dog struct {}

func (c cat)speak() { fmt.Println("喵喵喵~") }
func (d dog)speak() { fmt.Println("汪汪汪~") }

func hitta(x) {//传参：x dog 或 x cat；x需要具体类型
    x.speak() //挨打了就要叫
}
func main() {
    var c1 cat
    var d1 dog
    hitta(c1)
    hitta(d1)
}
~~~



改造代码

~~~go
type speaker interface{
    speak() //只要实现了speak方法的变量都是speaker类型
}
type cat struct {}
type dog struct {}
func (c cat)speak() { fmt.Println("喵喵喵~") }
func (d dog)speak() { fmt.Println("汪汪汪~") }

func hitta(x speaker) {
    x.speak() //挨打了就要叫
}
func main() {
    var c1 cat
    var d1 dog
    hitta(c1)
    hitta(d1)
}
~~~



再举例子

~~~go
type car interace{
    run()
}
type falali struct {
    brand string
}
type baoshijie struct {
    brand string
}
func (f falali)run() { fmt.Printf("%s速度70迈~\n", f.brand) }
func (b baoshijie)run() { fmt.Printf("%s速度100迈~\n", b.brand) }

func drive(c car) {
    c.run()
}
func main() {
    var f1 = falali{brand:"法拉利",}
    var b1 = baoshijie(brand:"保时捷",)
    drive(f1)
    drive(b1)
}
//不同数据库，实现连接和增删查改等操作
~~~



##### 5.4 包(package)

> 包(package)是多个Go源码的集合，是一种高级的代码复用方案，go也为我们提供了很多内置包，如fmt、os、io等。



**定义包**

~~~go
package 包名
~~~



**可见性**

如果想在一个包中引用另外一个包里的标识符(如变量、常量、类型、函数等)，该标识符必须是对外可见的(public)

在go语言中只需要将标识符的首字母大写即可对外可见。

~~~go
package pkg

import "fmt"

var a = 100 //首字母小写，包外不可见
const Mode = 1 //包外可见，可用

type person struct { //包外不可用
    name string
}

func Sum(x, y int) int {//包外可用
    return x + y
}

func age() {//包外不可用
    var Age = 18 //局部变量，函数外、包外不可用
    fmt.Println(Age)
}
~~~



结构体中的字段名和接口中的方法名如果首字母都是大写，外部包可以访问这些字段和方法。例如：

~~~go
type Student struct {
	  Name  string //可在包外访问的方法
	  class string //仅限包内访问的字段
}

type Payer interface {
	  init() //仅限包内访问的方法
	  Pay()  //可在包外访问的方法
}
~~~



**包的导入**

~~~go
import "包的路径"
~~~

- import导入语句通常放在文件开头包声明语句的下面。
- 导入的包名需要使用双引号包裹起来。
- 包名是从$GOPATH/src/后开始计算的，使用/进行路径分隔。
- Go语言中禁止循环导入包。



> 单行导入

~~~go
import "包1"
import "包2"
~~~



> 多行导入

~~~go
import (
    "包1"
    "包2"
)
~~~



**fmt包**

~~~go
/** Print Println的区别 */
fmt.Print("A")
fmt.Print("B")
fmt.Print("C")
// ABC  输出是连在一起

fmt.Println("A")
fmt.Println("B")
fmt.Println("C")
// 每个字母占一行

fmt.Print("A"， "B", "C")
// ABC 输出是连在一起的

fmt.Println("A", "B", "C")
// A B C 一次性输出多个字符串时，Println输出是有一个空格的

/** Printf() 格式化输出 */
fmt.Printf(a)
~~~



> Printf总结

~~~go
func main()  {
	num := 100
	fmt.Printf("%T\n", num) //查看类型    int
	fmt.Printf("%v\n", num) //万能输出值	 100
  
	fmt.Printf("%b\n", num) //二进制	  1100100
	fmt.Printf("%o\n", num) //八进制	  144
	fmt.Printf("%d\n", num) //十进制	  100
	fmt.Printf("%x\n", num) //十六进制  64
    
  number := 100.98
  fmt.Printf("%T，%f\n", number, number) //%f输出10进制的浮点数,(这里float64，100.980000)

	name := "chenglh"
	fmt.Printf("%s\n", name)  //字符串输出  chenglh
	fmt.Printf("%v\n", name)  //万能输出值  chenglh
	fmt.Printf("%#v\n", name) //"结果串"会自动加上双引号 "chenglh"
    
  //%c 输出单个字符
  n1 := '程'
	n2 := 'A'
	fmt.Printf("%c\n",n1) //如果不格式化，显示ASCII码
	fmt.Printf("%c\n",n2)
  
  age := 18
  nickname = "chenglh"
  fmt.Printf("%T\n", &age)      // *int    返回int类型的指针
	fmt.Printf("%T\n", &nickname) // *string 返回string类型的指针
}
~~~



##### 5.5 文件操作

###### 5.5.1 文件读操作

> 打开与关闭文件 os.Open() 和 file.Close()

~~~go
// 只读方式打开文件
file, err := os.Open("./main.go") //返回*File, err
if err != nil {
  fmt.Println("open file failed!, err:", err)
  return
}
// 关闭文件
file.Close()
~~~



> 读取文件 file.Read()

~~~go
func (f *File) Read(b []byte) (n int, err error)
~~~

函数接收一个字节切片，返回读取的字节数和可能的具体错误，读到文件末尾会返回 `0` 和 `io.EOF`



**指定字节读取文件**

~~~go
func main()  {
	file,err := os.Open("./main.go")
	if err != nil {
		fmt.Println("读取文件出错")
	}
	defer file.Close()

  //循环读取文件
	var content []byte
	var tmp = make([]byte, 128)
	for {
		n, err := file.Read(tmp) //指定字节读取
		if err == io.EOF {
			fmt.Println("文件读完了")
			break
		}
		if err != nil {
			fmt.Println("read file failed,err:", err)
			return
		}
		content = append(content, tmp[:n]...)
	}
	fmt.Println(string(content))
}
~~~



**一行行读取文件 **

~~~go
//bufio读取文件
//bufio是在file的基础上封装了一层API，支持更多的功能。

func main()  {
	file, err := os.Open("./main.go")
	if err != nil {
		fmt.Println("open file failed, err:", err)
		return
	}
	defer file.Close()

	var contents []byte
	reader := bufio.NewReader(file)
	for {
		line, err := reader.ReadString('\n') //注意是字符
		if err == io.EOF {//读到文件末了
			if err == io.EOF {
				if len(line) != 0 {
					contents = append(contents, line[:]...)
				}
				fmt.Println("文件读完了")
				break
			}
		}
		if err != nil {
			fmt.Println("文件读取出错")
		}
		contents = append(contents, line[:]...)
	}

	fmt.Println(string(contents[:]))
}
~~~



**ioutil读取整个文件**

`io/ioutil`包的`ReadFile`方法能够读取完整的文件，只需要将文件名作为参数传入。

~~~go
func main()  {
	contents,err := ioutil.ReadFile("./main.go")
	if err != nil {
		fmt.Println("read file railed, err:", err)
		return
	}
	fmt.Println(string(contents))
}
~~~



###### 5.5.2 文件写操作

定义格式：

~~~go
func OpenFile(name string, flag int, perm FileMode) (*File, error) {
	...
}
//参数1：文件名；参数2：打开文件的模式；参数3：文件权限 一个八进制数。r（读）04，w（写）02，x（执行）01。
~~~



**文件打开模式：**

| 模式        |   含义   |
| :---------- | :------: |
| os.O_WRONLY |   只写   |
| os.O_CREATE | 创建文件 |
| os.O_RDONLY |   只读   |
| os.O_RDWR   |   读写   |
| os.O_TRUNC  |   清空   |
| os.O_APPEND |   追加   |



> Write、WriteString

~~~go
func main()  {
	file,err := os.OpenFile("log.txt",os.O_CREATE|os.O_TRUNC|os.O_WRONLY, 0666)
	if err != nil {
		fmt.Println("open file failed, err:", err)
		return
	}
	defer file.Close()

	str := "天若有情\n"
	file.Write([]byte(str))    //通过字节写进去
	file.WriteString("人间正道")//通过字符串写进去
}
~~~



> Bufio.NewWriter

~~~go
func main()  {
	file,err := os.OpenFile("log.txt", os.O_CREATE|os.O_TRUNC|os.O_WRONLY, 0666)
	if err != nil {
		fmt.Println("创建文件失败")
	}
	defer file.Close()

	writer := bufio.NewWriter(file)
	for i := 0; i < 10; i++ {
		writer.WriteString(fmt.Sprintf("this is line:%d\n", i))//将数据写入缓存
	}
	writer.Flush()//刷新缓冲区
}
~~~



> Ioutil.WriteFile

~~~go
func main()  {
	//一次性读取文件
	contents,error := ioutil.ReadFile("./main.go")
	if error != nil {
		fmt.Println("read file railed, err:", error)
		return
	}

	//一次性写入文件
	err := ioutil.WriteFile("log.txt", []byte(contents), 0666)
	if err != nil {
		fmt.Println("创建文件失败", err)
		return
	}
}
~~~



###### 5.5.3 复制文件

~~~go
// 自定义 CopyFile 拷贝文件函数
func CopyFile(dstName, srcName string) (written int64, err error) {
	// 以读方式打开源文件
	src, err := os.Open(srcName)
	if err != nil {
		fmt.Printf("open %s failed, err:%v.\n", srcName, err)
		return
	}
	defer src.Close()

	// 以写|创建的方式打开目标文件
	dst, err := os.OpenFile(dstName, os.O_WRONLY|os.O_CREATE, 0644)
	if err != nil {
		fmt.Printf("open %s failed, err:%v.\n", dstName, err)
		return
	}
	defer dst.Close()

	//调用io.Copy()拷贝内容
	return io.Copy(dst, src)
}

func main()  {
	_, err := CopyFile("dst.txt", "src.txt")
	if err != nil {
		fmt.Println("copy file failed, err:", err)
		return
	}
	fmt.Println("copy done!")
}
~~~



###### 5.5.4 模拟cat命令

~~~go
func cat(r *bufio.Reader)  {
	for {
		buf, err := r.ReadBytes('\n') //注意是字符
		if err == io.EOF {
			break
		}
		fmt.Fprintf(os.Stdout, "%s", buf)
	}
}

func main()  {
	flag.Parse() //解析命令行参数
	if flag.NArg() == 0 {
		cat(bufio.NewReader(os.Stdin))
	}

	for i := 0; i < flag.NArg(); i++ {
		f, err := os.Open(flag.Arg(i))
		if err != nil {
			fmt.Fprintf(os.Stdout, "reading from %s failed, err:%v\n", flag.Arg(i), err)
			continue
		}
		cat(bufio.NewReader(f))
	}
}
~~~



##### Day06

###### 6.1 复习

###### 6.2 作业



###### 6.3 包(time)

time包提供了时间的显示和测量用的函数，日历的计算采用的是公历。

**日期时间**

~~~go
timeObj := time.Now() //时间对象

fmt.Printf("%T\n", timeObj) //类型：time.Time
fmt.Printf("%v\n", timeObj) //2020-08-12 10:54:19.2226132 +0800 CST m=+0.004000001

year  := timeObj.Year()
month := timeObj.Month() //默认是英文的月份,可以使用int强制转换数值
day   := timeObj.Day()
hour  := timeObj.Hour()
minute:= timeObj.Minute()
second:= timeObj.Second()

fmt.Printf("%d-%02d-%02d %02d:%02d:%02d", year, month, day, hour, minute, second);
//2020-08-12 11:06:57

//12小时制  03
date1 := timeObj.Format("2006-01-02 03:04:05") //格式化的模板，可自定义
fmt.Println(date1)

//24小时制  15
date := timeObj.Format("2006-01-02 15:04:05")
fmt.Println(date)
~~~



**时间戳**

~~~go
timeObj := time.Now() 			 //时间对象

unixtime := timeObj.Unix() 		 //获取当前时间戳
unixNatime := timeObj.UnixNano() //纳秒时间戳
~~~



**时间戳转字符串**

~~~go
unixTime := 1597202822

timeOjb := time.Unix(int64(unixtime), 0)//第一个参数是64位的秒数，第二个参数是纳秒
var date = timeOjb.Format("2006-01-02 15:04:05")
fmt.Println(date)
~~~

~~~go
//定义为通用函数
func timestampToDate(timestamp int64) string {
	timeObj := time.Unix(timestamp, 0)
	year := timeObj.Year()
	month:= int(timeObj.Month())
	day  := timeObj.Day()
	hour := timeObj.Hour()
	minute:= timeObj.Minute()
	second := timeObj.Second()

	return fmt.Sprintf("%d-%02d-%02d %02d:%02d:%02d", year,month,day,hour,minute,second)
}
~~~



**字符串转时间戳**

~~~go
var str = "2020-08-12 11:38:00"
var template = "2006-01-02 15:04:05"  //str 与 template格式要一样
timeObj,_ := time.ParseInLocation(template, str, time.Local)
fmt.Println(timeObj.Unix())

var str = "2020-08-12"
var temp = "2006-01-02"
timeObj,_ := time.Parse(temp, str)
fmt.Println(timeObj.Unix())
~~~

~~~go
//定义为通用函数
func dateToTimestamp(strTime string) int64 {
	var tempTime = "2006-01-02 15:04:05"
	timeObj,_ := time.ParseInLocation(tempTime, strTime, time.Local)

	return timeObj.Unix()
}
~~~



**时间包中常量**

~~~go
type Duration int64

const (
	Nanosecond  Duration = 1 //类自定义 Duration = int64别名
	Microsecond          = 1000 * Nanosecond
	Millisecond          = 1000 * Microsecond
	Second               = 1000 * Millisecond
	Minute               = 60 * Second
	Hour                 = 60 * Minute
)
~~~



**时间操作函数**

> Add

~~~go
//时间 + 时间间隔（如30分钟、1小时后）
timeObj := time.Now()
later := timeOjb.Add(time.Hour * 2) //返回的还是时间对象
~~~



> Sub



**定时器**

~~~go
//ticker.C 定时器
ticker := time.NewTicker(time.Second) //1秒执行一次
for tt := range ticker.C {
    fmt.Println(tt)
}

number := 5
ticker := time.NewTicker(time.Second)
for tt := range ticker.C {
    number--
    fmt.Println(tt)
    if number <= 0 {
        ticker.Stop() //关闭定时器 ticker.Stop 终止定时器
        break
    }
}
~~~



**时间格式化**

~~~go
now := time.Now()

fmt.Println(now.Format("2006-01-02 15:04:05"))// 24小时制
fmt.Println(now.Format("2006-01-02 03:04:05"))// 12小时制
fmt.Println(now.Format("2006/01/02 15:04"))
fmt.Println(now.Format("15:04 2006/01/02"))
fmt.Println(now.Format("2006/01/02"))
~~~



举例子：

~~~go
now := time.Now()

// 加载时区
loc, err := time.LoadLocation("Asia/Shanghai")
if err != nil {
	fmt.Println(err)
	return
}
// 按照指定时区和指定格式解析字符串时间
timeObj, err := time.ParseInLocation("2006/01/02 15:04:05", "2019/08/04 14:15:20", loc)
if err != nil {
	fmt.Println(err)
	return
}
fmt.Println(timeObj)

//编写程序统计一段代码的执行耗时时间，单位精确到微秒
fmt.Println(timeObj.Sub(now))
~~~



###### 6.4 日志需求





##### Day07

###### 7.1 复习



###### 7.2 作业



###### 7.3 反射



###### 7.4 配置文件



##### 7.5 strconv内置包

> 包strconv主要实现对字符串和基本数据类型之间的转换。

基本数据类型包括：布尔、整型和浮点型等。



###### 7.5.1 字符串转整型

~~~go
str := "1000"
int64(str)

iNum := int32(97)
ret2 := string(i) //把当成asci编码和utf-8编码,转成字符一定不能这样写
fmt.Println(ret2) //字母a
~~~



> Atoi() 字符串转整数int类型

~~~go
func Atoi(s string) (i int, err error) //如果转不成功，返回错误,只能是“数值字符串”能才转成功
~~~



举个例子：

~~~go
str := "100000"
ret1,err := strconv.Atoi(str)
if err != nil {
  fmt.Println("解析出错") // 1000程 | 10a00
}
fmt.Printf("%#v , %T\n", ret1, ret1) // 100000  int
~~~



> Itoa() 整形转字符串

~~~go
func Itoa(i int) string
~~~



举个例子:

~~~go
str1 := 1000
ret2 := strconv.Itoa(str1)
fmt.Printf("%#v , %T\n", ret2, ret2) // "1000" string
~~~



C语言中没有string类型而是用字符数组(array)表示字符串，所以`Itoa`对很多C系的程序员很好理解。



###### 7.5.2 Parse系列函数

> Parse类函数用于转换字符串为给定类型的值：ParseBool()、ParseFloat()、ParseInt()、ParseUint()。



举例说明：

~~~go
//字符串转数值
str := "1000"
ret1, err := strconv.ParseInt(str, 10, 64)//Parse解析的意思， 10进制；64位，而函数确实只返回int64
if err != nil {
	fmt.Println("解析出错")
}
fmt.Printf("%#v , %T", ret1, ret1) // 1000 int

//把字符串中解析出布尔类型
boolStr := "true"
boolVal,_ := strconv.ParseBool(boolStr)
fmt.Printf("%#v , %T\n", boolVal, boolVal) // true bool

//把字符串解析出浮点数
floatStr := "3.14159"
floatVal,_ := strconv.ParseFloat(floatStr, 64)
fmt.Printf("%#v , %T\n", floatVal, floatVal)//3.14159  float64
~~~



注意点：区别sprintf和itoa

测试效率：

~~~go
//测试Sprintf 与  strconv 的效率
func main()  {
	startTime := time.Now()
	for iNum := 0; iNum < 10000; iNum++ {
		fmt.Sprintf("%v", iNum)
	}
	fmt.Println(time.Now().Sub(startTime)) // 874.956µs

	startTime1 := time.Now()
	for iNum := 0; iNum < 10000; iNum++ {
		strconv.Itoa(iNum)
	}
	fmt.Println(time.Now().Sub(startTime1)) //261.744µs
}
~~~

结论如下：

~~~
可见时间性能相差约3倍，Sprintf 性能差些可以预见，因为它接收的是 interface，需要进行反射等操作。
建议使用 strconv 包中的方法进行转换。
~~~



###### 7.5.3 Format系列函数

> 格式化成string类型





##### 7.6 网络并发

并发：同一时间段内执行多个任务(同两个人聊天)

并行：同一时刻执行多个任务 （两个人一起进门）



> Go语言的并发通过goroutine实现。

goroutine类似于线程，属于用户态的线程，我们可以根据需要创建成千上万个goroutine并发工作。
goroutine是由Go语言的运行时（runtime）调度完成，而线程是由操作系统调度完成。
Go语言还提供channel在多个goroutine间进行通信。
goroutine和channel是 Go 语言秉承的 CSP（Communicating Sequential Process）并发模式的重要实现基础。



**goroutine**

在java/c++中要实现并发编程，需要维护一个线程池，包装任务，需要自己去高度线程执行任务并维护上下文切换

goroutine的概念类似于线程，但 goroutine是由Go的运行时（runtime）调度和管理的。
Go程序会智能地将 goroutine 中的任务合理地分配给每个CPU。
Go语言之所以被称为现代化的编程语言，就是因为它在语言层面已经内置了调度和上下文切换的机制。



**串行执行**

~~~go
func hello() {
	fmt.Println("Hello Goroutine!")
}
func main() {
	hello()
	fmt.Println("main goroutine done!")
}
~~~



**启用单个gorouteine**

~~~go
func main() {
	go hello() //启动另外一个goroutine去执行hello函数
	fmt.Println("main goroutine done!") //只打印这一行结果
  //time.Sleep(time.Second)
}
/** 当main（）函数返回的时候，goroutine就结束，使用简单粗暴的方式 time.Sleep等一等hello函数
~~~



举例子：

~~~go
func hello(i int)  {
	fmt.Println("hello,goroutine~",i)
}
func main()  {
	for i := 0; i < 10; i++ {
		go hello(i)
	}
	fmt.Println("this is main")
	time.Sleep(time.Second)
}

//匿名函数
for i := 0; i < 10; i++ {
   //go func() {
   //   fmt.Println(i) 因为是使用协程调用函数，for遍历太快了，这里的i与预想中的i++不一样
   //}()
		go func(i int) {
			fmt.Println(i)
		}(i)
}
~~~























https://www.bilibili.com/video/BV14C4y147y8?p=79