#### 第五天课程

##### 5.1 复习

结构体

~~~go
type person struct{
    name string
    age  int
}
~~~



构造函数

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



方法和接收者

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



结构体嵌套



结构体匿名字段



JSON序列化与反序列化



##### 5.2 作业



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



#### 第六天课程

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





#### 第七天课程

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



###### 7.5.2 Sprintf函数

> Sprintf() 其他类型转换成字符串

~~~go
//Sprintf 使用时 int - %d ; float - %f ; bool - %t ; byte - %c  %2f两位小数

var i int = 20
var f float64 = 12.542
var t bool = true
var b byte = 'a'

str1 := fmt.Sprintf("%d", i)
fmt.Printf("值：%#v,类型：%T\n", str1, str1)

str2 := fmt.Sprintf("%f", f)
fmt.Printf("值：%#v,类型：%T\n", str2, str2)

str3 := fmt.Sprintf("%t", t)
fmt.Printf("值：%#v,类型：%T\n", str3, str3)

str4 := fmt.Sprintf("%c", b)
fmt.Printf("值：%#v,类型：%T\n", str4, str4)

/**
 * 值："20",类型：string
 * 值："12.542",类型：string
 * 值："true",类型：string
 * 值："a",类型：string
 */
~~~



###### 7.5.3 Parse系列函数

> Parse类函数用于转换字符串为给定类型的值：ParseBool()、ParseFloat()、ParseInt()、ParseUint()

举例说明：

~~~go
//字符串转换成其他类型
str        := "1000"
boolStr    := "true"
floatStr   := "3.14159"

ret1, _    := strconv.ParseInt(str, 10, 64)//Parse解析的意思，10进制；64位，而函数确实只返回int64
boolVal,_  := strconv.ParseBool(boolStr)	 //把字符串中解析出布尔类型
floatVal,_ := strconv.ParseFloat(floatStr, 64)//把字符串解析出浮点数

fmt.Printf("%#v , %T", ret1, ret1) // 1000 int
fmt.Printf("%#v , %T\n", boolVal, boolVal) // true bool
fmt.Printf("%#v , %T\n", floatVal, floatVal)// 3.14159  float64
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



###### 7.5.4 Format系列函数

> Format()函数，将给定类型数据格式化为string类型数据的功能。FormatBool() 、FormatInt()、FormatUint()、FormatFloat()

~~~go
/**
 * func FormatBool(b bool) string
 *
 * func FormatInt(i int64, base int) string
 * base 必须在2到36之间，结果中会使用小写字母’a’到’z’表示大于10的数字
 *
 * func FormatUint(i uint64, base int) string
 *
 * func FormatFloat(f float64, fmt byte, prec, bitSize int) string
 * 参数1：传入要格式化的值；
 * 参数2："f" 正常格式;b 二进制；e 十进制指数；E 十进制指数；g (很大e格式)；G(很大的E格式)
 * 参数3：prec控制精度,对 f、e、E它表示小数点后的数字个数；对g、G 它控制总的数学个数。prec=-1则代表使用最少数量的、但又必需的 
 * 数字来表示f
 * 参数4：bitSize表示f的来源类型（32：float32、64：float64），会据此进行舍入。
 */

s1 := strconv.FormatBool(true) //"true"
s2 := strconv.FormatFloat(3.1415, 'E', -1, 64) // 3.1415E+00
s3 := strconv.FormatInt(-2, 16) //-2
s4 := strconv.FormatUint(2, 16) //2
~~~



##### 7.6 网络并发

###### 7.6.1 并发与并行

并发：同一时间段内执行多个任务(同两个人聊天)

并行：同一时刻执行多个任务 （两个人一起进门）



###### 7.6.2 goroutine

> Go语言的并发通过goroutine实现

- goroutine类似于线程，属于用户态的线程，我们可以根据需要创建成千上万个goroutine并发工作
- goroutine是由Go语言的运行时（runtime）调度完成，而线程是由操作系统调度完成
- Go语言还提供channel在多个goroutine间进行通信
- goroutine和channel是 Go 语言秉承的 CSP（Communicating Sequential Process）并发模式的重要实现基础。



在java/c++中要实现并发编程，需要维护一个线程池，包装任务，需要自己去高度线程执行任务并维护上下文切换。
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



**启用单个goroutine**

一个`goroutine`必定对应一个函数，可以创建多个`goroutine`去执行相同的函数。

~~~go
func main() {
	go hello() //启动另外一个goroutine去执行hello函数
	fmt.Println("main goroutine done!") //只打印这一行结果
  //time.Sleep(time.Second)
}
//当main()函数返回的时候该goroutine就结束了，所有在main()函数中启动的goroutine会一同结束，
/** 使用简单粗暴的方式 time.Sleep()等一等hello()函数执行 */
~~~



举个例子：

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



使用sleep时间来等待协程执行时间不可控，使用`sync.WaitGroup`来实现goroutine的同步

~~~go
var wg sync.WaitGroup

func hello()  {
	fmt.Println("hello,welcome~")
	wg.Done()
}
func main()  {
	wg.Add(1)
	go hello()
	//fmt.Println("job.")  会先执行打印job. ；协程的还需要时间处理，会后打印
	wg.Wait()
	fmt.Println("job.") //程序先阻塞挂起，等待执行完hello()方法，再执行后面的程序
}
~~~



**启动多个groutine**

~~~go
var wg sync.WaitGroup

func hello(i int) {
	defer wg.Done() // goroutine结束就登记-1
	fmt.Println("Hello Goroutine!", i)
}
func main() {
	for i := 0; i < 10; i++ {
		wg.Add(1) // 启动一个goroutine就登记+1
		go hello(i)
	}
	wg.Wait() // 等待所有登记的goroutine都结束
}
~~~

多次执行上面的代码，会发现每次打印的数字的顺序都不一致。这是因为10个`goroutine`是并发执行的，而`goroutine`的调度是随机的。



###### 7.6.3 groutine与线程

> 可增长的栈

OS线程（操作系统线程）一般都有固定的栈内存（通常为2MB）

`goroutine`的栈在其生命周期开始时只有很小的栈（最小2KB），`goroutine`的栈不是固定的，可增缩，最大可达1G

Go语言中一次创建十万左右的`goroutine`也是可以的



> goroutine调度

**GPM**是Go语言运行时（runtime）层面的实现，是go语言自己实现的一套调度系统。

- **G** 就是个goroutine，里面除了存放本goroutine信息外，还有与所在P的绑定等信息。
- **P** 管理着一组goroutine队列，P里面会存储当前goroutine运行的上下文环境（函数指针，堆栈地址及地址边界），P会对自己管理的goroutine队列做一些调度（比如把占用CPU时间较长的goroutine暂停、运行后续的goroutine等等）当自己的队列消费完了就去全局队列里取，如果全局队列里也消费完了会去其他P的队列里抢任务。
- **M（machine)**是Go运行时（runtime）对操作系统内核线程的虚拟， M与内核线程一般是一一映射的关系， 一个groutine最终是要放到M上执行的；



**M（machine）**是Go运行时（runtime）对操作系统内核线程的虚拟， M与内核线程一般是一一映射的关系， 一个groutine最终是要放到M上执行的；



P的个数是通过**runtime.GOMAXPROCS**设定（最大256），Go1.5版本之后默认为物理线程数。 

在并发量大的时候会增加一些P和M，但不会太多，切换太频繁的话得不偿失。



> GOMAXPROCS

Go运行时的调度器使用`GOMAXPROCS`参数来确定需要使用多少个OS线程来同时执行Go代码。默认值是机器上的CPU核心数。例如在一个8核心的机器上，调度器会把Go代码同时调度到8个OS线程上（GOMAXPROCS是m:n调度中的n）。

Go语言中可以通过`runtime.GOMAXPROCS()`函数设置当前程序并发时占用的CPU逻辑核心数。

Go1.5版本之前，默认使用的是单核心执行。Go1.5版本之后，默认使用全部的CPU逻辑核心数。



我们可以通过将任务分配到不同的CPU逻辑核心上实现并行的效果，这里举个例子：

~~~go
func a() {
	for i := 1; i < 10; i++ {
		fmt.Println("A:", i)
	}
}

func b() {
	for i := 1; i < 10; i++ {
		fmt.Println("B:", i)
	}
}

func main() {
	runtime.GOMAXPROCS(1) //开一个cpu来跑
	go a()
	go b()
	time.Sleep(time.Second)
}
~~~

两个任务只有一个逻辑核心，此时是做完一个任务再做另一个任务。 

将逻辑核心数设为2，此时两个任务并行执行，代码如下。

~~~go
func a() {
	for i := 1; i < 10; i++ {
		fmt.Println("A:", i)
	}
}

func b() {
	for i := 1; i < 10; i++ {
		fmt.Println("B:", i)
	}
}

func main() {
	runtime.GOMAXPROCS(2)
	go a()
	go b()
	time.Sleep(time.Second)
}
~~~



###### 7.6.4 channel

单纯地将函数并发执行是没有意义的。函数与函数间需要交换数据才能体现并发执行函数的意义。

虽然可以使用共享内存进行数据交换，但是共享内存在不同的`goroutine`中容易发生竞态问题。

为了保证数据交换的正确性，必须使用互斥量对内存进行加锁，这种做法势必造成性能问题。



Go语言的并发模型是`CSP(Communicating Sequential Processes)`，提倡**通过通信共享内存**而不是**通过共享内存而实现通信**。

`channel`是可以让一个`goroutine`发送特定值到另一个`goroutine`的通信机制。



Go 语言中的通道（channel）是一种**特殊的类型**。通道像一个传送带或者队列，总是遵循先入先出（First In First Out）的规则，保证收发数据的顺序。每一个通道都是一个具体类型的导管，也就是声明channel的时候需要为其指定元素类型。



> channel定义

~~~go
var 变量 chan 元素类型
~~~



channel 是一种类型，也是引用类型。

~~~go
var ch1 chan int   // 声明一个传递整型的通道
var ch2 chan bool  // 声明一个传递布尔型的通道
var ch3 chan []int // 声明一个传递int切片的通道
~~~



通道是引用类型，通道类型的空值是`nil`。

~~~go
var ch1 chan int
fmt.Println(ch1) // <nil>
~~~



> 创建channel

~~~go
make(chan 元素类型, [缓冲大小]) //缓冲大小 选填
~~~



创建channel

~~~go
ch1 := make(chan int)
ch2 := make(chan bool)
ch3 := make(chan []int)
~~~



> channel操作

通道有发送（send）、接收(receive）和关闭（close）三种操作



**发送 **

~~~go
ch1 <- 10 // 把10发送到ch1中
~~~



**接收**

~~~go
x := <- ch1 // 从ch1中接收值并赋值给变量x
<-ch1       // 从ch1中接收值，忽略结果
~~~



**关闭**

~~~go
close(ch1)
~~~



关闭后的通道有以下特点：

1. 对一个关闭的通道再发送值就会导致panic。
2. 对一个关闭的通道进行接收会一直获取值直到通道为空。
3. 对一个关闭的并且没有值的通道执行接收操作会得到对应类型的零值。
4. 关闭一个已经关闭的通道会导致panic。



> 无缓冲的通道

无缓冲的通道又称为阻塞的通道。

~~~go
func main(){
  ch := make(chan int)
  ch <- 10
  fmt.Println("发送成功")
}
~~~

上面这段代码能够通过编译，但是执行的时候会出现以下错误：

~~~go
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan send]:
main.main()
        /Users/xx/go/src/day07/04channel1/main.go:7 +0x54
exit status 2
~~~

为什么会出现`deadlock`错误呢？

因为我们使用`ch := make(chan int)`创建的是无缓冲的通道，无缓冲的通道只有在有人接收值的时候才能发送值。

简单来说就是无缓冲的通道必须有接收才能发送。

上面的代码会阻塞在 ch <- 10 这一行代码形成死锁，那如何解决这个问题呢？



一种方法是启用一个goroutine去接收值，例如：

~~~go
func recv(c chan int) {
	ret := <-c
	fmt.Println("接收成功", ret)
}
func main() {
	ch := make(chan int)
	go recv(ch) // 启用goroutine从通道接收值
	ch <- 10
	fmt.Println("发送成功")
}
~~~



使用无缓冲通道进行通信将导致发送和接收的goroutine同步化。因此，无缓冲通道也被称为同步通道。



> 有缓冲的通道

另一种方法是创建有缓冲区的通道。

我们可以在使用make函数初始化通道的时候为其指定通道的容量，例如：

~~~go
func main() {
	ch := make(chan int, 1) // 创建一个容量为1的有缓冲区通道
	ch <- 10
	fmt.Println("发送成功")
}
~~~

只要通道的容量大于零，那么该通道就是有缓冲的通道，通道的容量表示通道中能存放元素的数量。

只有容量满了就装不下了，就阻塞了，等到协程取走一个就能往里面放一个。

使用内置的`len`函数获取通道内元素的数量，使用`cap`函数获取通道的容量。



> For range  从通道循环取值

当向通道中发送完数据时，我们可以通过`close`函数来关闭通道。

当通道被关闭时，再往该通道发送值会引发`panic`，从该通道取值的操作会先取完通道中的值，再然后取到的值一直都是对应类型的零值。

~~~go
//channel练习
//关闭通道，for range也退出循环

func main()  {
	ch1 := make(chan int)
	ch2 := make(chan int)

	//开启goroutine将 0- 100发送到ch1中
	go func() {
		for iNum := 1; iNum <= 100; iNum++ {
			ch1 <- iNum
		}
		close(ch1)
	}()

	//开启goroutine从ch1中接收者，并将该值的平方发送到ch2中
	go func() {
		for {
      //方法1：判断该通道是否被关闭
			ret,ok := <- ch1 //通道关闭后再取值时，ok=false
			if !ok {
				break
			}
			ch2 <- ret * ret
		}
		close(ch2)
	}()

  //方法2：判断该通道是否被关闭
	//遍历ch2中接收的值
	for result := range ch2 {
		fmt.Println(result)
	}
}
~~~



以上有两种方式在接收值的时候判断该通道是否被关闭，不过我们通常使用的是`for range`的方式。使用`for range`遍历通道，当通道被关闭的时候就会退出`for range`。



###### 7.6.5 单向通道

在不同的任务函数中使用通道都会对其进行限制，比如限制通道在函数中只能发送或只能接收。

Go语言中提供了**单向通道**来处理这种情况。

~~~go
//单向通道

//只能发送进通道
func counter(count chan<- int)  {
	for iNum := 1; iNum <= 100; iNum++ {
		count <- iNum
	}
	close(count)
}

//只能从count通道中取值
func squarer(square chan<- int, count <-chan int)  {
	for i := range count {
		square <- i * i
	}
	close(square)
}

func printer(out <- chan int)  {
	for i := range out{
		fmt.Println(i)
	}
}

func main()  {
	ch1 := make(chan int)
	ch2 := make(chan int)
	go counter(ch1)
	go squarer(ch2, ch1)
	printer(ch2)
}
~~~

- `chan<- int`是一个只写单向通道（只能对其写入int类型值），可以对其执行发送操作但是不能执行接收操作；
- `<-chan int`是一个只读单向通道（只能从其读取int类型值），可以对其执行接收操作但是不能执行发送操作。



> 通道异常

<img src="Golang2.assets/image-20200821133857394.png" alt="image-20200821133857394" style="zoom:50%;float:left;" />



关闭已经关闭的`channel`也会引发`panic`。



###### 7.6.6 worker pool

**Worker pool  即是goroutine池**，在工作中我们通常会使用可以指定启动的goroutine数量–`worker pool`模式，控制`goroutine`的数量，防止`goroutine`泄漏和暴涨。

一个简易的`work pool`示例代码如下：

~~~go
func worker(id int, jobs <-chan int, results chan<- int) {
	for j := range jobs {
		fmt.Printf("worker:%d start job:%d\n", id, j)
		time.Sleep(time.Second)
		fmt.Printf("worker:%d end job:%d\n", id, j)
		results <- j * 2
	}
}

func main() {
	jobs := make(chan int, 100)
	results := make(chan int, 100)
	// 开启3个goroutine
	for w := 1; w <= 3; w++ {
		go worker(w, jobs, results)
	}
	// 5个任务
	for j := 1; j <= 5; j++ {
		jobs <- j
	}
	close(jobs)
	// 输出结果
	for a := 1; a <= 5; a++ {
		<-results
	}
}
/**
 * worker:3 start job:1
 * worker:1 start job:2
 * worker:2 start job:3
 * worker:3 end job:1
 * worker:2 end job:3
 * worker:2 start job:5
 * worker:3 start job:4
 * worker:1 end job:2
 * worker:2 end job:5
 * worker:3 end job:4
 */
~~~



###### 7.6.7 select

select 多路复用

在某些场景下我们需要同时从多个通道接收数据。通道在接收数据时，如果没有数据可以接收将会发生阻塞。你也许会写出如下代码使用遍历的方式来实现：

~~~go
for{
    // 尝试从ch1接收值
    data, ok := <-ch1
    // 尝试从ch2接收值
    data, ok := <-ch2
    …
}
~~~

可以使select优化效率。

~~~go
select{
    case <-ch1:
        ...
    case data := <-ch2:
        ...
    case ch3<-data:
        ...
    default:
        默认操作
}
~~~

举个例子：

~~~go
func main() {
	ch := make(chan int, 1)
	for i := 0; i < 10; i++ {
		select {
		case x := <-ch:
			fmt.Println(x)
		case ch <- i:
		}
	}
}
//打印出来的是：0 2 4 6 8
~~~

使用`select`语句能提高代码的可读性。

- 可处理一个或多个channel的发送/接收操作。
- 如果多个case同时满足，select会随机选择一个。
- 对于没有case的select{}会一直等待，可用于阻塞main函数。



##### 7.7 并发安全与锁

引出：有时候在Go代码中可能会存在多个`goroutine`同时操作一个资源（临界区），这种情况会发生`竞态问题`（数据竞态）。

如多个 goroutine操作全局变量时，会出现问题

~~~go
var x int64
var wg sync.WaitGroup

func add() {
	for i := 0; i < 5000; i++ {
		x = x + 1
	}
	wg.Done()
}
func main() {
	wg.Add(2)
	go add()
	go add()
	wg.Wait()
    fmt.Println(x) //每次执行的结果都不一样，add()共同去获取变量x
}
~~~



###### 7.7.1 互斥锁

互斥锁是一种常用的控制共享资源访问的方法，它能够保证同时只有一个goroutine可以访问共享资源。
Go语言中使用sync包的Mutex类型来实现互斥锁。

~~~go
var x = 0
var wg sync.WaitGroup
var lock sync.Mutex

//互斥锁

func Sum()  {
	for i := 0; i < 5000 ; i++  {
		lock.Lock()   //加锁
		x += 1
		lock.Unlock() //解锁
	}
	wg.Done()
}

func main()  {
	wg.Add(2)
	go Sum()
	go Sum()
	wg.Wait()
	fmt.Println(x)
}
~~~

使用互斥锁能够保证同一时间有且只有一个`goroutine`进入临界区，其他的`goroutine`则在等待锁；
当互斥锁释放后，等待的`goroutine`才可以获取锁进入临界区，多个`goroutine`同时等待一个锁时，唤醒的策略是随机的。



###### 7.7.2 读写互斥锁

互斥锁是完全互斥的，但是有很多实际的场景下是读多写少的，当我们并发的去读取一个资源不涉及资源修改的时候是没有必要加锁的，这种场景下使用读写锁是更好的一种选择。

> 读写锁在Go语言中使用`sync`包中的`RWMutex`类型。

读写锁分为两种：读锁和写锁。
当一个goroutine获取**读锁**之后，其他的goroutine如果是`获取读锁会继续获得锁`，如果是获取写锁就会等待；
当一个goroutine获取**写锁**之后，其他的goroutine无论是`获取读锁还是写锁都会等待`。



举个例子：现一个变量，写10次，读1000次。

~~~go
var (
	x      int64
	wg     sync.WaitGroup
	lock   sync.Mutex
	rwlock sync.RWMutex
)

func write() {
	// lock.Lock()   // 加互斥锁
	rwlock.Lock()    // 加写锁
	x = x + 1
	time.Sleep(10 * time.Millisecond) // 假设读操作耗时10毫秒
	rwlock.Unlock()                   // 解写锁
	// lock.Unlock()                  // 解互斥锁
	wg.Done()
}

func read() {
	// lock.Lock()               // 加互斥锁
	rwlock.RLock()               // 加读锁
	time.Sleep(time.Millisecond) // 假设读操作耗时1毫秒
	rwlock.RUnlock()             // 解读锁
	// lock.Unlock()             // 解互斥锁
	wg.Done()
}

func main() {
	start := time.Now()
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go write()
	}

	for i := 0; i < 1000; i++ {
		wg.Add(1)
		go read()
	}

	wg.Wait()
	end := time.Now()
	fmt.Println(end.Sub(start))
}
~~~

需要注意的是读写锁非常适合读多写少的场景，如果读和写的操作差别不大，读写锁的优势就发挥不出来。



###### 7.7.3 sync.WaitGroup

| 方法名                          | 功能                |
| ------------------------------- | ------------------- |
| (wg * WaitGroup) Add(delta int) | 计数器 + delta      |
| (wg *WaitGroup) Done()          | 计数器 -  1         |
| (wg *WaitGroup) Wait()          | 阻塞直到计数器变为0 |

`sync.WaitGroup`内部维护着一个计数器，计数器的值可以增加和减少。例如当我们启动了N 个并发任务时，就将计数器值增加N。每个任务完成时通过调用Done()方法将计数器减1。通过调用Wait()来等待并发任务执行完，当计数器值为0时，表示所有并发任务已经完成。

需要注意`sync.WaitGroup`是一个结构体，传递的时候要传递指针。



###### 7.7.4 sync.Once

在编程的很多场景下我们需要确保某些操作在高并发的场景下只执行一次，例如只加载一次配置文件、只关闭一次通道等。

Go语言中的`sync`包中提供了一个针对只执行一次场景的解决方案–`sync.Once`。

其签名如下：

~~~go
func (o *Once) Do(f func()) {}
~~~

备注：如果要执行的函数`f`需要传递参数就需要搭配闭包来使用。



###### 7.7.5 sync.Map









##### 7.8 原子操作

###### 7.1.1 atomic包

































https://www.bilibili.com/video/BV14C4y147y8?p=79