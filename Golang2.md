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

<img src="H:\笔记本\Golang2.assets\image-20200804162045881.png" alt="image-20200804162045881" style="zoom:50%;float:left" />

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





https://www.bilibili.com/video/BV14C4y147y8?p=65