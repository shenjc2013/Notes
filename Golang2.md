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

