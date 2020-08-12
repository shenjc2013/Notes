##### Day05

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



###### 5.3 接口(interface)

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
func (c cat)move() {
    fmt.Println("走猫步！")
}
func (c cat)eat(food string) {
    fmt.Printf("猫吃%s！\n", food) 
}

type chicken struct {
    feet int8
}
func (c chicken)move() {
    fmt.Println("鸡动！")
}
func (c chicken)eat(food string) {
    fmt.Printf("鸡吃饲料！\n", food) 
}

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

func (c cat)speak() {
    fmt.Println("喵喵喵~")
}
func (d dog)speak() {
    fmt.Println("汪汪汪~")
}
func data(x) {//传参：x dog 或 x cat；x需要具体类型
    x.speak() //挨打了就要叫
}
func main() {
    var c1 cat
    var d1 dog
    data(c1)
    data(d1)
}
~~~

改造代码

~~~go
type speaker interface{
    speak() //只要实现了speak方法的变量都是speaker类型
}
type cat struct {}
type dog struct {}
func (c cat)speak() {
    fmt.Println("喵喵喵~")
}
func (d dog)speak() {
    fmt.Println("汪汪汪~")
}
func data(x speaker) {
    x.speak() //挨打了就要叫
}
func main() {
    var c1 cat
    var d1 dog
    data(c1)
    data(d1)
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
func (f falali)run() {
    fmt.Printf("%s速度70迈~\n", f.brand)
}
func (b baoshijie)run() {
    fmt.Printf("%s速度100迈~\n", b.brand)
}
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



###### 5.4 包(package)

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



**时间操作函数**

~~~go
var timeObj = time.Now()
fmt.Println(timeObj)
timeObj = timeObj.Add(time.Hour) //打印一小时后的时间
fmt.Println(timeObj)

//休眠1秒
time.Sleep(time.Second)

/**
const (
	Nanosecond  Duration = 1 //类自定义 Duration = int64别名
	Microsecond          = 1000 * Nanosecond
	Millisecond          = 1000 * Microsecond
	Second               = 1000 * Millisecond
	Minute               = 60 * Second
	Hour                 = 60 * Minute
)
*/
~~~



**定时器**

~~~go
//ticker.C 定时器
ticker := time.NewTicker(time.Second)
for tt := range ticker.C {
    fmt.Println(tt)
}

//关闭定时器 ticker.Stop
number := 5
ticker := time.NewTicker(time.Second)
for tt := range ticker.C {
    number--
    fmt.Println(tt)
    if number <= 0 {
        ticker.Stop() //终止定时器
        break
    }
}
~~~















https://www.bilibili.com/video/BV14C4y147y8?p=79