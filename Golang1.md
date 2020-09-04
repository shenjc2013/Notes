#### 第三天课程

##### 3.1 复习

**运算符**

~~~go
算术运算符：+、-、*、/

逻辑运算符：&& || !

位运算符：>> << | ^ &

赋值运算符： = 、+= 、-= 、 ++ 、--

比较运算符： <、<=、!=、>、>=
~~~

**数组**

~~~go
var ages  [30]int	//元素的个数、类型
var names [30]string

var aa = []int{1,13,50}
bb := aa
fmt.Printf("aa:%T\tbb:%T\n", aa, bb)  //aa:[]int        bb:[]int
fmt.Printf("aa:%p\tbb:%p\n", aa, bb)  //aa:0xc00009e140 bb:0xc00009e140

bb[1] = 2
fmt.Println(aa,bb)  //[1 2 50] [1 2 50]  数组赋值是地址引用
~~~



数组是值类型

~~~go
func main() {
	x := [3]int{1, 2, 3}
	y := x //把x的值拷贝了一份给y
	y[1] = 200 //修改了的是副本y，并不影响x

	fmt.Println(x)
	f1(x)
	fmt.Println(x)
}

func f1(a [3]int)  {
	a[1] = 100
}

func main() {
	var a1 = [...][2]int{ //多维数组只有最外层可以使用...
		[2]int{1,2},
		[2]int{3,4},
		[2]int{5,6},
	}
	fmt.Println(a1)
}
~~~





**切片**

~~~go
//切片(Slice) 方法1：声明+初始化
var s1 []int  //没有分配内存，== nil
fmt.Println(s1) 	 // []
fmt.Println(s1==nil) //true
s1 = []int{1, 2, 3}

//方法2：make初始化，分配内存
s2 := make([]bool, 2, 4) //类型  长度  容量
fmt.Println(s2)		//[false false]
fmt.Println(s2==nil) //false 已经分配内存有内存地址 != nil
~~~

切片本质

切片：指针、长度、容量

<img src="./Golang.assets/image-20200718115108169.png" alt="image-20200718115108169" style="float:left;" />

切片的拷贝

~~~go
s1 := []int{1, 2, 3}
s2 := s1
fmt.Println(s2) // [1 2 3]
s2[1] = 200
fmt.Println(s2) // [1 200 3]
fmt.Println(s1) // [1 200 3]
~~~

切片不存值，相当于一个框，去底层数组去框值

//切片只是切了一部分数据，但还是同一个内存地址；切片不存值，指向同一个底层数组

 ~~~

 ~~~

切片的扩容策略：

1、如果申请的容量大于原来的2倍，那就直接扩容到新申请的容量

2、如果小于1024，那么直接两倍

3、如果大于1024，就按照1.25倍去扩容

4、具体存储的值类型不同，扩容策略也有差别

~~~go
var s1 []int  // nill
s1 = append(s1, 1) //append函数追加，会自动初始化切片。
fmt.Println(s) // [1]
~~~

copy()

~~~go
s1 := []int{1, 2, 3}
s2 := s1  //赋值
var s3 []int  //nil 所以打印出来的是 []
// var s3 = make([]int, 3)
copy(s3, s1)  //拷贝，注意copy不会自动扩容，make时需要设置长度
fmt.Println(s2) // [1 2 3]
s2[1] = 200
fmt.Println(s2) // [1 200 3]
fmt.Println(s1) // [1 200 3]
fmt.Println(s3) // []
~~~



**指针**

& 和 *

~~~go
addr := "广东"
addrP := &addr
fmt.Println(addrP
fmt.Println(*addrP)
~~~

**map**

map存储的是键值对

~~~go
var m1 map[string]int
fmt.Println(m1 == nil)  //true

//声明变量且申请内存地址
var m1 = make(map[string]int, 10) //算一下存的长度
m1["chenglh"] = 100  //如果key不存在，返回的是对应类型的零值
fmt.Println(m1)

v,ok := m1["chenglh"]
if ok {
    //
} else {
    //没有对应key
}

delete(m1, "chenglh") //如果key不存在，什么都不干
~~~

##### 3.2 作业

~~~go
//1、判断字符串中汉字的数量
//统计汉字字数
s1 := "Hello沙河有沙有河"
var count int
for _, v := range s1{
    if unicode.Is(unicode.Han, v) {
        count++
    }
}
fmt.Println(count)


//2、统计英文单词出现的次数
s  := "how do you do"
m1 := make(map[string]int, 10)
s2 := strings.Split(s, " ")
for _,w := range s2 {
    if _,ok := m1[w]; ok {
        m1[w]++
    } else {
        m1[w] = 1
    }
}
for key, value := range m1 {
    fmt.Println(key,value)
}

//3、回文判断
//上海自来水来自海上
//山西落雁塔雁落西山
//黄山落叶松叶落山黄
ss := "a山西落雁塔雁落西山a"
//把字符串中的字符放到一个[]rune切片中
r := make([]rune, 0, len(ss)) //容量长一点没关系
for _,c := range ss{
    r = append(r, c)
}
fmt.Println("[]rune", r) //转成ASCII码了
for i := 0; i < len(r)/2; i++ {
    // ss[i]  ss[len(ss)-1-i]
    if r[i] != r[len(r)-1-i] {
        fmt.Println("不是回文 ")
        return
    }
}
fmt.Println("是回文")
~~~





#### 第四天课程

##### 4.1 复习

**函数**

函数的定义

~~~go
func 函数名(参数1,参数2...) 返回值 {
   //函数体
}
~~~



**函数进阶**

> 高阶函数(函数可以作为参数，也可以作为返回值)

~~~go
func main() {
    f1(f2, "chenglh")
    ret := zl() //返回的是一个函数体
    sum := ret(10, 20) //调用函数体
}

func f1(f func(string), name string) {
    f(name)
}

func f2(name string) {
    fmt.Println("Hello，",name)
}

func zl() func(int, int) int {
    return func (x,y int) int{
        return x+y
    }//返回的是一个函数类型
}
~~~



> 闭包

定义：函数和其外部变量的引用

~~~go
func ys(name string){
    fmt.Println("Hello，",name)
}
func low(f func()) { //类型不匹配，ys()函数传不进去
    f()
}

//闭包
func bibao(f func(string),name string) func() {
    return func() {
        f(name)
    }
}

func main() {
    fc := bibao(yx, "myname")
    low(fc)
}
~~~



> defer：延迟调用，多用于处理资源释放

> 内置函数

panic 和 recover

~~~go
func f1() {
    defer func() {
        err := recover()//收集当前的错误
        fmt.Println("松手去爱...")
        fmt.Println(err)
    }()
	panic("犯错") //程序崩溃了，后面不打印了
    fmt.Println("f1")
}

func f2() {
    fmt.Println("f2")
}

func main() {
    f1()
    f2()
}
~~~



##### 4.2 作业



##### 4.3 struct

Go语言中没有“类”的概念，也不支持“类”的继承等面向对象的概念。
Go语言中通过结构体的内嵌再配合接口比面向对象具有更高的扩展性和灵活性。
结构体是**值类型**，赋值的时候都是拷贝。



###### 4.3.1 自定义类型

~~~go
//将myInt定义为int类型
type myInt int
~~~



###### 4.3.2 类型别名

 类型别名是在 go1.9以后版本新增

~~~go
//定义格式
type TypeAlias = Type
~~~



如系统已定义的别名

~~~go
type byte = uint8
type rune = int32
~~~



###### 4.3.3 类型定义和别名区别

~~~go
//自定义类型
type newInt int

//类型别名
type myInt = int

func main()  {
		var a newInt = 10
		var b myInt  = 20

		fmt.Printf("a的值：%v 对应类型：%T\n", a, a) //a的值：10 对应类型：main.newInt
		fmt.Printf("b的值：%v 对应类型：%T\n", b, b) //b的值：20 对应类型：int
}

    //a的类型是main.newInt，表示main包下定义的newInt类型
    //b的类型是int
    /*myInt类型只会在代码中存在，编译完成时并不会有myInt类型*/
~~~



###### 4.3.4 结构体

> 结构体也是一种类型，可以通过 type 和 struct 来定义结构体

~~~go
type 类型名 struct {
    字段名 字段类型
    字段名 字段类型
    …
}
~~~



> 正常定义

~~~go
type person struct {
		name string
		city string
		age  int8
}
~~~



> 简写写法，同类型的字段简写

~~~go
type person1 struct {
    name,city string
    age int8
}
~~~



###### 4.3.5 结构体实例化

只有当结构体实例化时，才会真正地分配内存。

~~~go
var 实例名称  结构体类型
~~~



举个例子：

~~~go
//结构体的声明与实例化
type Person struct {
    Name string
    Age  int8
    Sex  string
}

func main()  {
		//实例化结构体一，var 变量名 类型
		var p1 Person
		p1.Name = "chenglh"
		p1.Age  = 20
		p1.Sex  = "男"
		fmt.Printf("p1的值：%v p1详：%#v p1类型：%T\n", p1, p1, p1)
  	//p1的值：{chenglh 20 男} p1详：main.Person{Name:"chenglh", Age:20, Sex:"男"} p1类型：main.Person
  
  	//实例化结构体二，通过new关键字来实例化结构体，得到的是结构体的地址
  	//创建指针类型结构体
		var p2  = new(Person)
		p2.Name = "fanlp" //等同于 (*p2).Name = "fanlp" //语法糖。
		p2.Age  = 20
		p2.Sex  = "女"
		fmt.Printf("p2的值：%v p2详：%#v p2类型：%T\n", p2, p2, p2)
  	//p2的值：&{fanlp 20 女} p2详：&main.Person{Name:"fanlp", Age:20, Sex:"女"} p2类型：*main.Person
  
  	//实例化结构体三
		//使用&对结构体进行取地址操作，相当于对该结构体类型进行了一次new实例化操作
		var p3  = &Person{}
		p3.Name = "chenghl"
		p3.Sex  = "男"
		p3.Age  = 2
		fmt.Printf("p3的值：%v p3详：%#v p3类型：%T\n", p3, p3, p3)
  	//p3的值：&{chenghl 2 男} p3详：&main.Person{Name:"chenghl", Age:2, Sex:"男"} p3类型：*main.Person
  
  	//实例化结构体四
		//使用键值对的方式来实例化结构体
		var p4 = Person{
				Name: "zhangsan",
				Sex	: "man",
				Age	: 22,
		}
		fmt.Printf("p4的值：%v p4详：%#v p4类型：%T\n", p4, p4, p4)
  	//p4的值：{zhangsan 22 man} p4详：main.Person{Name:"zhangsan", Age:22, Sex:"man"} p4类型：main.Person
  
  	//实例化结构体五
		//用了取地址
		var p5 = &Person{
				Name: "zhangsan",
				Sex	: "man",
				Age	: 22,
		}
		fmt.Printf("p5的值：%v p5详：%#v p5类型：%T\n", p5, p5, p5)
  	//p5的值：&{zhangsan 22 man} p5详：&main.Person{Name:"zhangsan", Age:22, Sex:"man"} p5类型：*main.Person
  
  	//实例化结构体六
		//简写变量名
		var p6 = Person{
				"李子",
				20,
				"女",
		}
		fmt.Printf("p6的值：%v p6详：%#v p6类型：%T\n", p6, p6, p6)
  	//p6的值：{李子 20 女} p6详：main.Person{Name:"李子", Age:20, Sex:"女"} p6类型：main.Person
}
~~~

**总结归纳为两种方法：1、创建指针类型结构体，2、取结构体的地址实例化**



###### 4.3.6 方法与接收者

**给类型（结构体，自定义类型）定义方法。**

~~~go
func (接收者 接收者类型) 方法名(参数列表)(返回参数) {
    //方法体
}
~~~

接收者：一般写法是使用接收者类型的首字母

接收者类型：接收者类型和参数类似，可以是指针类型和非指针类型。

- 非指针类型：表示不修改结构体的内容
- 指针类型：表示修改结构体中的内容



例子一：

~~~go
//方法与接收者

type Student struct {
		Name string
		Sex  string
		Age  int8
}

//打印学生基本信息
func (s Student) getInfo() {
		fmt.Println("姓名：",s.Name)
		fmt.Println("性别：",s.Sex)
		fmt.Println("年龄：",s.Age)
}

//修改学生基本信息
func (s *Student) setInfo(name string, sex string, age int8) {
		s.Name = name
		s.Sex  = sex
		s.Age  = age
}

func main()  {
		var student Student
		student.Name = "张三"
		student.Age  = 22
		student.Sex  = "男"
		student.getInfo()

		student.setInfo("chenglh", "man", 20)
		student.getInfo()
}
~~~

**注意，因为结构体是值类型，所以我们修改的时候，需要传入的指针**



例子二：

~~~go
//构造函数实例化

type Person struct {
		Name string
		Sex  string
		Age  int
}

/**
 * 构造函数：约定成俗用new开头
 * 如果字段少时可以返回结构体对象
 * 但是如果字段多的时侯，返回结构体指针
 */
//func newPerson(name string, sex string, age int) Person {
//	return Person{
//		Name: name,
//		Sex: sex,
//		Age: age,
//	}
//}

//当结构体比较大的时候尽量使用结构体指针，减少程序的开销
func newPerson(name string, sex string, age int) *Person {
		return &Person{
				Name: name,
				Sex: sex,
				Age: age,
		}
}

func main()  {
		var p = newPerson("程辉", "男", 25)
		fmt.Println(p)
}
~~~



> 什么时候使用指针接收者

1. 需要修改接收者中的值
2. 接收者是拷贝代价比较大的大对象
3. 保证一致性，如果有某个方法使用了指针接收者，那么其他的方法也应该使用指针接收者。



###### 4.3.7 结构体值类型

~~~go
/**
 * 值类型: 改变变量副本的时候，不会改变变量本身的值(数组，基本数据类型，结构体)
 * 引用类型: 改变变量副本的时候，会改变变量本身的值(切片，map)
 */

type Person struct {
    Name string
    Age  int
}

func main()  {
    var p1 Person
    p1.Name = "chenglh"
    p1.Age  = 18
	
    p2 	:= p1
    p2.Age = 22
    fmt.Printf("%#v %#v\n",p1, p2)
    //main.Person{Name:"chenglh", Age:18} main.Person{Name:"chenglh", Age:22}
    //是两个不同的对象
}
~~~



###### 4.3.8 任意类型添加方法

在Go语言中，接收者的类型可以是任何类型，不仅仅是结构体，任何类型都可以拥有方法。 

举个例子，我们基于内置的`int`类型使用type关键字可以定义新的自定义类型，然后为我们的自定义类型添加方法。

~~~go
//MyInt 将int定义为自定义MyInt类型
type MyInt int

//SayHello 为MyInt添加一个SayHello的方法
func (m MyInt) SayHello() {
		fmt.Println("Hello, 我是一个int。")
}
func main() {
	  var m1 MyInt
	  m1.SayHello() //Hello, 我是一个int。
	  m1 = 100
	  fmt.Printf("%#v  %T\n", m1, m1) //100  main.MyInt
}
~~~



结构体的字段类型可以是：基本数据类型，也可以是切片、Map 以及结构体；

如果结构体的字段类似是：指针、slice、和 map 的零值都是nil，即还没有分配空间；

如果需要使用这样的字段，需要先make，才能使用。

~~~go
/**
结构体的字段类型可以是：基本数据类型，也可以是切片、Map 以及结构体；
如果结构体的字段类似是：指针、slice、和 map 的零值都是nil，即还没有分配空间；
如果需要使用这样的字段，需要先make，才能使用。
*/

type Person struct {
		Name 		string
		age  		int
		hobby 	[]string
		Detail 	map[string]string
}

func main()  {
		var person Person
		person.Name = "chenglh"
		person.age  = 21

		//切片申请空间
		person.hobby = make([]string, 3, 3)
		person.hobby[0] = "学习"
		person.hobby[1] = "运动"
		person.hobby[2] = "看报"

		//map申请空间
		person.Detail = make(map[string]string)
		person.Detail["address"] = "广东省广州市天河区"
		person.Detail["phone"]   = "13678917765"

		fmt.Printf("%#v\n", person)
    // main.Person{Name:"chenglh", age:21, 
    // hobby:[]string{"学习", "运动", "看报"}, 
    // Detail:map[string]string{"address":"广东省广州市天河区", "phone":"13678917765"}}
}
~~~





###### 4.3.9 结构体匿名字段

结构体允许其成员字段在声明时没有字段名而只有类型，这种没有名字的字段就称为匿名字段。

~~~go
type Person struct {
    string
    int
}
func main()  {
    var p1 = Person {
        "chenglh",
        14,
    }
		fmt.Printf("%#v\n", p1) //main.Person{string:"chengl", int:14}
  	fmt.Println("姓名：",p1.string)
		fmt.Println("年龄：",p1.int)
}
~~~

**注意：**这里匿名字段的说法并不代表没有字段名，而是**默认会采用类型名作为字段名**，结构体要求字段名称必须**唯一**，因此一个结构体中同种类型的匿名字段只能有一个。



###### 4.3.10 嵌套结构体

结构体的字段类型可以是：基本数据类型，也可以是切片、Map 以及结构体等。

~~~go
//Address 地址结构体
type Address struct {
    Province string
		City     string
}

//Student 用户结构体
type Student struct {
		Name    string
		Gender  string
		Address Address
}

func main()  {
    var student = Student{
        Name: "chenglh",
				Gender: "男",
				Addr: Address{
						Province: "广东省",
						City		: "广州市",
						Dist		: "天河区",
				},
		}
		fmt.Printf("%#v\n",student)
		fmt.Println("姓名：",student.Name)
		fmt.Println("地址：", student.Addr.Province,student.Addr.City,student.Addr.Dist)
		/**
	main.Student{Name:"chenglh", Gender:"男", Addr:main.Address{Province:"广东省", City:"广州市", Dist:"天河区"}}
	姓名： chenglh
	地址： 广东省 广州市 天河区
		*/
}
~~~



**嵌套匿名结构体**

~~~go
//嵌套匿名字段
type Address struct {
    Province  string
    City	 		string
}

type Live struct {
    Province  string
    City	 		string
}

type User struct {
		Name 		string
		Gender 	string
	 //City		string 优先级是结构体当前字段
   //Live   //如果多个匿名结构体时，优先级从上往下找第一个的字段
		Address	//匿名字段
}

func main()  {
		var user User
		user.Name 	= "琪琪"
		user.Gender = "女"
		user.Address.Province = "广东"
		//user.Address.City = "广州"
		user.City = "广州" //当访问结构体成员时会先在结构体中查找该字段，找不到再去嵌套的匿名字段中查找。

		fmt.Printf("%#v\n",user)
  	//main.User{Name:"琪琪", Gender:"女", City:"广州", Address:main.Address{Province:"广东", City:""}}
}
~~~



**当访问结构体成员时会先在结构体中查找该字段，找不到再去嵌套的匿名字段中查找。嵌套的结构体从上到下查找的优先级**

但是在实际项目中最好不要这么使用，增加代码阅读复杂度。

嵌套结构体内部可能存在相同的字段名。在这种情况下为了避免歧义需要通过指定具体的内嵌结构体字段名。

~~~go
user.City 				//最外层结构体
user.Address.City	//Address结构体
user.Live.City		//Live 结构体
~~~





在定义一些`临时数据结构`等场景下还可以使用匿名结构体

~~~go
func main() {
    var user struct{ name string; age int } //声明匿名变量为结构体，多是临时使用
    user.name = "chenglh"
    user.age = 18
    fmt.Println(user) //{chenglh 18}
    fmt.Printf("%#v\n", user)//struct{name string; age int}{name:"chenglh", age:18}
}
~~~





> 创建指针类型结构体

使用`new()`关键字对结构体进行实例化，得到的是结构体的指针地址。

~~~GO
var p2 = new(person)
p2.name = "chenglh"
fmt.Printf("%T\n", p2)     //*main.person  结构体指针 " *main 表示是main包 "
fmt.Printf("p2=%#v\n", p2) //p2=&main.person{name:"chenglh", city:"", age:0}
fmt.Printf("%p\n", p2)     //得到的是内存地址
~~~

make是返回值类型

~~~go
func main() {
    var a int
    a = 100
    b:= &a
    fmt.Printf("type a:%T type b:%T\n", a, b)
    fmt.Printf("%p\n", &a)  //变量a的内存地址
    fmt.Printf("%p\n", b)   //变量b的值
    fmt.Printf("%p\n", &b)  //变量b的内存地址
}
~~~



Go语言中支持对结构体指针直接使用 `.` 来访问结构体的成员

~~~go
var p2 = new(person)
p2.name = "小王"
p2.age  = 18
p2.city = "广东"
fmt.Printf("p2=%#v\n", p2) //p2=&main.person{name:"小王", city:"广东", age:18}
~~~



> 取地址实例化

使用 `&` 对结构体进行取地址操作，相当于对该结构体类型进行了一次`new()`实例化操作

~~~go
p3 := &person{}
fmt.Printf("%T\n", p3)     //*main.person
fmt.Printf("p3=%#v\n", p3) //p3=&main.person{name:"", city:"", age:0}

p3.name = "chenglh"
p3.age  = 19
p3.city = "广东"
fmt.Printf("p3=%#v\n", p3) //p3=&main.person{name:"chenglh", city:"广东", age:19}
~~~

`p3.name = "chenglh"`其实在底层是`(*p3).name = "chenglh"`，这是Go语言帮我们实现的语法糖！



> 结构体初始化

没有初始化的结构体，其成员变量都是对应其类型的零值

~~~go
type person struct {
    name string
    city string
    age  int8
}

func main() {
    var p4 person
    fmt.Printf("p4=%#v\n", p4) //p4=main.person{name:"", city:"", age:0}
}
~~~



###### 4.3.11 结构体内存布局

**结构体占用一块连续的内存**

~~~go
type test struct {
		a int8  //8bit -> 1byte
		b int8
		c int8
		d int8
    //e string  会出现内存对齐(内存高级管理)
}
n := test{
		1, 2, 3, 4,
}
fmt.Printf("n.a %p\n", &(n.a))
fmt.Printf("n.b %p\n", &(n.b))
fmt.Printf("n.c %p\n", &(n.c))
fmt.Printf("n.d %p\n", &(n.d))
~~~

输出结果：[连续的内存地址]

~~~go
n.a 0xc00000a0d8
n.b 0xc00000a0d9
n.c 0xc00000a0da
n.d 0xc00000a0db
~~~



###### 4.3.12 空结构体

空结构体是不占用空间的。

~~~go
var v struct{}
fmt.Println(unsafe.Sizeof(v))  // 0
~~~



###### 4.3.13 结构体继承

~~~go
//结构体的继承

//Animal结构体
type Animal struct {
		Name string
}
func (a Animal) run() {
		fmt.Printf("%v 在跑~\n", a.Name)
}

//Dog结构体
type Dog struct {
		feet int8
		Animal			//*Animal 如果是指针类型,下面位置：Animal:&Animal{
}
func (d Dog) wang() {
		fmt.Printf("%v 在叫~\n", d.Name)
}

func main() {
		var dog = Dog{
				feet: 4,
				Animal:Animal{  //位置
						Name: "小汪财",
				},
		}
		dog.run()		//小汪财 在跑~
		dog.wang()	//小汪财 在叫~
}
~~~



###### 4.3.14 结构体与JSON序列化

Golang中的序列化和反序列化主要通过"encoding/json"包中的 **json.Marshal()** 和 **json.Unmarshal()**

~~~go
//定义一个学生结构体
type Student struct {
		Id			int64		`json:"id"`
		Gender	string	`json:"gender"`
		Name 		string	`json:"name"`
		Sno 		string	`json:"sno"`
}

func main()  {
		var student1 = Student{
				Id			: 12001,
				Gender	: "男",
				Name		: "张小三",
				Sno			: "No12001",
		}

		//结构体转换成json（返回的是byte类型的切片）
		studentByte,_ := json.Marshal(student1) //直接是屏蔽了错误信息。
		fmt.Println(string(studentByte))
    //{"id":12001,"gender":"男","name":"张小三","sno":"No12001"}
  
  	//字符串转成结构体，即反序列化
		var studentStr = `{"id":12002,"gender":"女","name":"赵四五","sno":"No12002"}`
		var student2 Student
		err := json.Unmarshal([]byte(studentStr), &student2)//转换成切片
		if err != nil {
				fmt.Println("格式化失败")
		}
		fmt.Printf("%#v\n",student2)
    //main.Student{Id:12002, Gender:"女", Name:"赵四五", Sno:"No12002"}
}
~~~

**因为要传入外包格式化，结构体中变量首字母需要大写，如果是小写，会被过滤丢弃掉**

**常见的格式化有：json:"name" db:"name" ini:"name"**



###### 4.3.15 嵌套结构体与JSON转换

~~~go
//嵌套结构体与json的互相转换

//班级结构体
type Class struct {
		ClassName string
		Students  []Student
}

//学生结构体
type Student struct {
		Id 			int64
		Gender 	string
		Name 		string
}

func main() {
	var class = Class{
			ClassName: "1年级1班",
			Students: make([]Student, 0),
	}
	for iNum := 1; iNum <= 5; iNum++ {
			s := Student{
					Id: int64(iNum),
					Gender: "男",
					Name: fmt.Sprintf("stu%v", iNum),
			}
			class.Students = append(class.Students, s) //切片追加
	}
	//fmt.Printf("%#v\n", class)

  //结构体序列化
	str,_ := json.Marshal(class)
	fmt.Println(string(str))
  //{"ClassName":"1年级1班","Students":[{"Id":1,"Gender":"男","Name":"stu1"},{"Id":2,"Gender":"男","Name":"stu2"},{"Id":3,"Gender":"男","Name":"stu3"},{"Id":4,"Gender":"男","Nam4"},{"Id":5,"Gender":"男","Name":"stu5"}]}

    //json数据反序列化成结构体
		ret := string(str)
		var class2 = &Class{
				ClassName: "",
				Students: make([]Student, 0),
		}
		err := json.Unmarshal([]byte(ret), class2)
		if err != nil {
				fmt.Println(err)
		}
		fmt.Printf("%#v\n", class2)
    //&main.Class{ClassName:"1年级1班", Students:[]main.Student{main.Student{Id:1, Gender:"男", Name:"stu1"}, main.Student{Id:2, Gender:"男", Name:"stu2"}, main.Student{Id:3, Gend", Name:"stu3"}, main.Student{Id:4, Gender:"男", Name:"stu4"}, main.Student{Id:5, Gender:"男", Name:"stu5"}}}
}
~~~



