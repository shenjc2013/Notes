**Go基础巩固加强**

###### 第一章 指针

指针是一个代表着某个内存地址的值。Go语言保留了指针，但与c语言指针有所不同，主要表现：

1、默认值是nil

2、& 取变量地址；* 通过指针访问目标对象

3、不支持指针运算，直接用  " . " 访问目标成员

~~~go
var a int = 10
var p *int = &a

*p = 100 // 这里是 取值运算符 *，也叫解引用或间接引用。 修改内存值为250 
~~~



**栈帧的内存布局**

32位操作系统，2的32次方 = 4G内存

62位操作系统，2的64次方 = 16G内存

Kernel内核，操作系统的主体部分，驱动等；0-3G是用户区；data区是全局初始化的存放的区，

<img src="Golang.assets/image-20200831172615864.png" style="float:left;" />



栈帧：用来给函数运行提供内存空间。取内存于stack上。

当函数调用时，产生栈帧。函数调用结束，释放栈帧。

栈帧存储：1、局部变量，2、形参。（形参与局部变量存储地位等同）3、内存字段描述值（记录栈基、栈顶指针位置）



stack开辟一片空间来运行 main()，main()里边有函数Test()，再在 main在相邻内存空间再开辟一块来运行

Test()函数。函数运行完要释放内存空间。

~~~go
func test(m int) {
	var b = 10
}
func main() {
	var a = 10
	var p *int = &a
    test(10)
}
~~~

<img src="golang.assets/image-20200918210154059.png" alt="image-20200918210154059" style="zoom:50%;float:left;" />



指针使用注意：

空指针：未被初始化的指针。var  p  *int

野指针：被一片无效空间初始化。var p *int = 0xc00001c080  程序直接飘红，不能编译通过。



以上使用指针变量，都需要另一个变量来跟随取地址。可以使用new来初始化指针变量，而new出来的内存地址是在 heap（堆）上面的。

~~~go
func main() {
	//在 heap 上申请一片内存地址空间
	var p *int
	p = new(int) //8字符空间大小，p是地址，对应的值是，默认类型的零值
	fmt.Println(*p)
}
~~~



~~~go
var a int = 10
var b int = 20 //b是一个变量名称，指向的是一个内存地址； b也是左值

a = b //b是右值，即变量b所指向的值
~~~



变量存储：等号左边的变量，代表变量所指向的内存空间。

等号右边的变量，代表变量内存空间存储的数据值。

~~~go
func test () {
	var p *int
	*p = 100
}
//函数是在栈帧上的，运行完回收内存空间
//如果参数是传指针变量，因为指针是存放在堆空间上的，没有在栈空间上，所以不会被消毁
~~~



栈 stack的空间在32位操作系统上是 1M-8M，但是heap堆却 1G+

如果指针不再使用，可以置成 nil，gc是去回收。



**函数new**

表达式 new(T) 将创建一个T类型的匿名变量，所做的是为T类型的新值分配并清零一块内存空间，然后将这块内存空间的地址作为结果返回，而这个结果就是指向这个新的T类型值的指针值，返回的指针类型为 *T。

new创建的内存空间位于heap上，空间的默认值为数据类型的默认值。如， var p = new(int) 则 *p 为 0。



我们只需使用 new()函数，无需担心其内存的生命周期或怎样将其删除，因为Go语言的内存管理会帮我们打理一切。



传地址(引用)：将地址值作为函数参数、返回值后传递。

传值(数据值)：将实参的值，拷贝一份给形参。

~~~go
func swap(a, b int)  {
	a,b = b, a
	fmt.Println("swap : a = ",a," b= ", b)
}

func main()  {
	a,b := 10, 20
	swap(a, b)
	fmt.Println("main : a = ",a," b= ", b)
}
/**
swap : a =  20  b=  10
main : a =  10  b=  20
*/
~~~



**图解程序：**

<img src="golang.assets/image-20200919094318359.png" alt="image-20200919094318359" style="zoom:50%;" />



**引用传参**

~~~go
func swap(a, b int)  {
	a,b = b, a
	fmt.Println("swap : a = ",a," b = ", b)
}

func swap1(x, y *int) {
	*x,*y = *y, *x //如果没有星号就是 地址交换，得不预期的值
	fmt.Println("swap : x = ", *x," y = ", *y)
}

func main()  {
	a,b := 10, 20
	swap(a, b) // 20 10
	swap1(&a, &b) // 传地址：20 10
	fmt.Println("main : a = ",a," b = ", b) //经过swap1() a b底层值已被修改
}
~~~



**图解程序**

![image-20200919100200560](golang.assets/image-20200919100200560.png)



传引用：在A栈帧内部，修改B栈帧中的变量值。



###### 第二章 切片

为什么要用切片：

1、数组的容量固定，不能自动拓展；

2、值传递。数组作为函数参数时，将整个数组值拷贝一份给形参。



在Go语言中，几乎可以在所有场景中，使用切片代替数组。

一个 slice由三部分构成：指针、长度和容量。

指针指向第一个slice元素对应的底层数组元素的地址，要注意的是 slice的第一个元素并不一定是数组的第一个元素。

切片的本质：不是一个数组的指针，来用操作数组内部元素

**切片并不是数组或数组指针，它通过内部指针和相关属性引用数组片段，以实现变长方案**

~~~go
type slice struct {
	*p
	len()
	cap()
}
~~~



切片的使用：

切片名称：a[low : high : max]

low : 起始下标位置(包括)

High：结束下标位置(不包括)

容量 : cap = max - low

Len = high - low

截取数组，初始化时，没有指定切片容量时，切片容量跟随原底层数组(切片)

~~~go
arr := [6]int{1,2,3,4,5,6}
s := arr[1:3:5] //s = []int{2,3} 长度是2 容量：5-1 = 4
//s := arr[1:3] //容量是max跟随底层数组值,6-1 = 5
~~~



切片的创建：

1、自动推导类型创建切片   slice := []int{1,2,3,4,5,6}

2、slice :=  make([]int, 长度, 容量)

3、slice := make([]int, 长度)  //没有指定容量， 容量值跟随长度。



注意：make 只能创建 slice、map、和 channel，并且返回一个有初始值(非零)的对象。



切片做函数参数 ---- 传引用，(传址)，切片和Map在函数参数中都是引用类型



append(切片对象, 待追加元素)



练习1：slice上过滤空串。

~~~go
//{"red", "", "black", "", "", "blue", "pink"}

func fn1(data []string) []string {
	//output := data[:0] //在原切片上截取一个长度为0的切片 == make([]string, 0)
	//for _,valStr := range data{
	//	if valStr != "" {
	//		output = append(output, valStr)
	//	}
	//}
	//
	//return output

	//直接在原串上挪动值的位置,不过这种方法会破坏底层原数据
	iNum := 0
	for _,str := range data {
		if str != "" {
			data[iNum] = str
			iNum++
		}
	}
	return data[:iNum]
}

func main() {
	data := []string{"red", "", "black", "", "", "blue", "pink"}
	slice1 := fn1(data)

	fmt.Println(slice1)
}
~~~



练习2：消除[]string中重复字符串。

~~~go
//消除 重复字符串
func fn2(data []string) []string {
	//有一个元素的切片
	var outSlice = data[:1]
	for _,val := range data {
		var flag = true
		for _,sVal := range outSlice { //循环已有切片
			if sVal == val {
				flag = false
				break
			}
		}
		if flag {
			outSlice = append(outSlice, val)
		}
	}

	return outSlice
}

func main() {
	data := []string{"red", "red", "black", "pink", "blue", "blue", "pink","white"}
	slice1 := fn2(data)
	fmt.Println(slice1)
}
~~~



copy 函数在 两个  slice 间复制数据，复制长度以 len 小的为准，两个 slice 指向同一底层数组，直接对应位置覆盖。

copy(目标位置切片，源位置切片)

切片拷贝：对应的下标进行覆盖。

~~~go
func main() {
	//copy(dst, src)
	data := [...]int{0,1,2,3,4,5,6,7,8,9}
	s1 := data[8:]	// {8,9}
	s2 := data[:5]  // {0,1,2,3,4}
	//copy(s2, s1)	// s1的值去覆盖 s2 得到 [8 9 2 3 4]
	copy(s1, s2) //这里覆盖后，s1 = {0，1}

	fmt.Println(s1)
}
~~~



练习：删除slice中间某个元素并保存原有元素顺序。如：{5, 6, 7, 8, 9}  -> {5, 6, 8, 9}

~~~go
func fn3(data []int, idx int) []int {
	// {5 ,6, 7, 8, 9}  即 8 9往前拷贝
	//方法一：
	//copy(data[idx:], data[idx+1:])
	//return data[:len(data)-1]

	//方法二：
	return append(data[:idx], data[idx+1:]...)
}

func main() {
	data := []int{5, 6, 7, 8, 9}
	ret := fn3(data, 2)
	fmt.Println(ret)
}
~~~



###### 第三章 Map

字典、映射。 Key -- value  key：唯一、无序，不能是引用类型数据。即key不能变化。

map是无序的。每次打印出的顺序可能不一样。

~~~go
func main() {
	var m1 map[int]string //只是声明一个map，没有初始化，为空 nil map
	fmt.Println(m1 == nil) //true
	//m1[1] = "luffy"  //nil 的map 没初始化，不使用使用 panic: assignment to entry in nil map

	//初始化
	m2 := map[int]string{}
	m3 := make(map[int]string)
	fmt.Println(m2, m3) // map[] map[]
	
	m4 := make(map[int]string, 10) //第二个参数是指定容量
	fmt.Println(m4) // map[]
}
~~~

map可以自动扩容，只不过不用借助类似 append函数，直接赋值即可。如 m1[10] = "Nini"，在赋值过程中，如果 key重复，新值会覆盖旧值。

但是！ 对于 map而言，可以使用  len() 函数，但是不能使用 cap()函数。

点进去看源码，cap()的注释上有标明，array、slice、channel可以使用  cap

make()的源码也看到注释标明哪些类型可以使用



创建方法：

~~~go
1、var m1 map[int]string -- 不能存储数据

2、m2 := map[int]string{}  -- 能存储数据

3、m3 := make(map[int]string)  --默认 len =0

4、m4 := make(map[int]string, 10)
~~~



初始化：

~~~go
var m = map[int]string{1:"lufei", 250:"zhansan"} //类型推导方式
//m =  map[1:lufei 250:zhansan]
~~~



赋值：

~~~go
m2 := make(map[int]string, 10) //如果长度不够，会自动扩容
m2[0] = "chenglh"
m2[1] = "fph"
~~~



map的使用：

var m = make(map[int]string, 10)

遍历 map: 1、 for k, v := range  m   2、for k := range m    读取值 m[k]

判断 map中某key是否存在： val,ok = m[k]

Val 存在 即为有值，不存在 为 nil

Ok 值是 true 或 false



删除 map

map做函数参数时，**是引用类型传参**

delete(m, key) 函数，如果 m[key]存在即删除，如果不存在即不进行任何操作。

~~~go
//删除map中某个key
func mapDelete(m map[int]string, key int) map[int]string {
	delete(m, key)
	return m
}
//因为map做函数参数，返回值 都是传引用
//可以没有返回值。简化写法
/**
func mapDelete(m map[int]string, key int) {
	delete(m, key)
}*/
~~~



练习：接收一段英文字符串，返回一个 map ，记录每个单词出现的次数。

提示：使用 strings.Fields() 函数可提高效率。

~~~go
//如 "I love my work and I love my family too too"
//1、字符串拆分
func tjWords(str string) map[string]int {
	var tjWord = make(map[string]int)
	slice := strings.Fields(str)  //拆分方法一
	//slice := strings.Split(str, " ") //拆分方法二
	for _,valStr := range slice {
		if _,ok := tjWord[valStr]; ok {
			tjWord[valStr] += 1
		} else {
			tjWord[valStr] = 1
		}
	}
	return tjWord
}
~~~



###### 第四章 Struct

结构体是一种数据类型。

~~~go
type Persion struct {
	name string  //只是类型定义；name string = "test" 错误代码，不能赋值，基本定义，只有默认零值
	sex byte
	age int
}
~~~



变通变量定义和初始化：

~~~go
//1、依次初始化
//顺序初始化，必须全部初始化完整
var boy1 = Person{
	"chenglh",
	1,
	20,
}

//2、部分初始化
var boy2 = Person{
	name: "chenglh",
	sex : 1,
	age : 20,
}

//3、声明类型，属性成员赋值
var boy3 Person
boy3.name = "mike"
boy3.sex = 1
boy3.age = 20
~~~



结构体比较

1、只支持 == 和 != ， 不支持 大于、小于等符号

2、相同结构体类型变量(成员变量的类型，个数，顺序要一致)之间可以直接赋值。（即定义结构体属性的顺序也要一致）

~~~go
var p1 = Person{"chenglh", 1, 20}
var p2 = Person{"chenglh", 1, 20}
var p3 = Person{"chenglh", 1, 18}
fmt.Println("p1 = p2 ? ", p1 == p2) //true
fmt.Println("p1 = p3 ? ", p1 == p3) //false
~~~



函数内部使用结构体传参。

unSafe.Sizeof(指针) ： 不管在何种类型的指针，在64位操作系统下，均为 8 字节。

将结构体变量的值拷贝一份，**值传递**，很少使用，内存消耗大，效率低

~~~go
var temp Person //放在  RW 内存区，声明后即会初始化默认零值

test(temp)  //值传递，拷贝一份副本给形参，所以test() 方法里修改等操作不会改变原值
~~~



嵌套结构体

~~~go
type Student struct{
	p Person  //嵌套Person结构体
	No string
	......
}
~~~



结构体地址：

**结构体变量的地址 == 结构体首个元素的地址。**(结构体比较第二点中的 原因)

将结构体变量地址值，传递(传引用) --- 使用频率非常高。



unSafe.Sizeof(结构体名称)  可以获得结构体所占用的内存空间大小。



指针变量定义和初始化：

~~~go
1、顺序初始化：
var boy1 *Person = &Person{ "chenglh", 1, 20,}
//使用类型推导
//var boy1 = &Person{ "chenglh", 1, 20,}

//new关键字，都放在堆内存空间上
boy := new(Person)
boy.name = "asdf"
boy.age = 20
boy.sex = 1

fmt.Printf("boy = %p\n", boy) //结构体指针地址
fmt.Printf("&boy.name = %p\n", &boy.name) //这里的两个值是一样的
~~~



图解结构体地址引用：

![image-20200920151514226](golang.assets/image-20200920151514226.png)



结构体指针做函数返回值：

**不能返回局部变量的地址值**。-- 局部变量保存在栈帧上，函数调用结束后，栈帧释放，局部变量的地址，不再受系统保护，随时可能分配给其他程序。

返回局部变量值。



###### 第五章 String

字符串拆分

~~~go
Strings.Split(str, sep) //按指定字符分割，返回的是切片

//按空格拆分
Strings.Fields(str)
~~~



判断字符串是否有前缀、后缀

~~~go
strings.HasPrefix(str, suffix string) bool

strings.HasSuffix(str, suffix string) bool
~~~



字符串处理函数:

1.字符串按指定分割符拆分:     Split

~~~go
ret := strings.Split(str, ";")
~~~

2.字符串按空格拆分: Fields

~~~go
ret = strings.Fields(str)
~~~

3.判断字符串结束标记 HasSuffix

~~~go
flg := strings.Hassuffix("test.abc", ".mp3")
~~~

4.判断字符串起始标记 HasPrefix

~~~go
flg = strings.HasPrefix("test.abc", "tes.")
~~~



###### 第六章 File

打开、创建文件：

1、创建文件 os.Create(fileName string)  (*file ,err)  文件不存在时创建； 文件存在时，将文件内容清空。

​	参数：name 文件名称，打开文件的路径：绝对路径、相对路径。

~~~go
func main() {
	file,err := os.Create("./a.log")
	if err != nil {
		fmt.Println("create file err :", err)
		return
	}
	defer file.Close()
}
~~~



2、打开文件 os.Open 以只读文件打开文件。文件不存在，打开失败。

​	参数：name 文件名称，打开文件的路径：绝对路径、相对路径。

~~~go
func main() {
	file,err := os.Open("./log.txt")
	if err != nil {
		fmt.Println("读取文件出错")
		return
	}
	file.Close()
	fmt.Println("打开文件成功")
}
~~~



3、打开文件 os.OpenFile	以只读、只写、读写方式 打开文件。文件不存在，打开失败。

​	参数1：name 文件名称，打开文件的路径：绝对路径、相对路径。

​	参数2：打开文件模式：O_RDONLY、O_WRONLY、O_RDWR

​	参数3：权限值，0666

~~~go
func main() {
	file,_ := os.OpenFile("log.txt", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0666)
	file.WriteString("this is test, " + time.Now().Format("2006/01/02 15:04"))
	file.Close()
}
~~~



总结：

1、创建文件 os.Create(fileName string)  (*file ,err)  文件不存在时创建； 文件存在时，将文件内容清空。

2、打开文件 os.Open 以只读文件打开文件。文件不存在，打开失败。

3、打开文件 os.OpenFile	以只读、只写、读写方式 打开文件。文件不存在，打开失败。



**文件打开模式：**

| 模式        |   含义   |
| :---------- | :------: |
| os.O_WRONLY |   只写   |
| os.O_CREATE | 创建文件 |
| os.O_RDONLY |   只读   |
| os.O_RDWR   |   读写   |
| os.O_TRUNC  |   清空   |
| os.O_APPEND |   追加   |



写文件：

​	**1、按字符串写**   file.WriteString(str)   **返回写入的字符个数；**

​	回车换行符： window下 \r\n；Linux系统下 \n

~~~go
func main() {
	file,_ := os.OpenFile("log.txt",os.O_CREATE|os.O_WRONLY, 0666) //读写模式
	defer file.Close()

	n, _ := file.WriteString("123")
	fmt.Println(n) //返回写入了n个字符数，如果 log.txt中已经有内容，字符串会从0开始覆盖原有字符串
	//这里代码没多少实用性 ，可用 os.O_APPEND 追加方式写入文件
}
~~~



​	**2、按位置写**       Seek()  获取文件的读写指针位置；

​			参数1：偏移量； 正数：向文件尾偏；负数：向文件头偏

​			参数2：偏移起始位置：

​					io.SeekStart：文件起始位置

​					io.SeekCurrent：文件当前位置

​					io.SeekEnd：文件结尾位置

​			返回值：表示从文件起始位置，到当前文件读写指针位置的偏移量。（无论是从前往后，还是从后往前读）

​	**3、按字节写**： writeAt()  在文件制定偏移位置，写入 []byte，通常搭配 Seek()

~~~go
	//offset,_ := file.Seek(5, io.SeekStart) // offset = 5
	offset,_ := file.Seek(-5, io.SeekEnd)
	fmt.Println("offset = ", offset)

	//可以以读写模式打开文件，使用offset偏移光标位置，然后写入文件
	//即可以修改覆盖文件内容
	n,_  := file.WriteAt([]byte("this word"),  offset)
~~~



**读文件：**

> 1、按行读

reader := bufio.NewReader(file) ;

buf,err := reader.ReadBytes('\n')

**判断文本结尾  err != nil && err == io.EOF**

~~~go
func main() {
	file,err := os.OpenFile("./main.go", os.O_RDONLY, 0666)
	if err != nil {
		fmt.Println("打开文件错误，err：", err)
		return
	}
	defer file.Close()

	//创建一个带有缓冲区的 reader
	reader := bufio.NewReader(file)
	for {
		buf,err := reader.ReadBytes('\n') //读取一行内容
		if err != nil && err == io.EOF {
			break
			return
		}
		fmt.Print(string(buf))
	}
}
~~~



缓冲区：内存中的一块区域，用来减少物理磁盘访问操作。



> 2、按字节读

~~~go
func main() {
	file,err := os.OpenFile("./main.go", os.O_RDONLY, 0666)
	if err != nil {
		fmt.Println("打开文件错误，err：", err)
		return
	}
	defer file.Close()

	var fileContext []byte
	var tmpStr = make([]byte, 128)
	for {
		n,err := file.Read(tmpStr) //按字节读取内容放入到tmpStr
		if err != nil && err == io.EOF {
			break
			return
		} else if err != nil {
			fmt.Println("读取文件错误")
			return
		}
		fileContext = append(fileContext, tmpStr[:n]...)
	}
}
~~~



> 3、整个文件读 ioutil工具包

~~~go
func main() {
	//整个文件读取
	read,_ := ioutil.ReadFile("./main.go")
	fmt.Println(string(read))
}
~~~



**复制文件：**（文件拷贝）

read([]byte)  按字节读文件。

write([]byte) 按字节写文件。

~~~go
// 拷贝大文件,内容多或视频文件等。

func main() {
	// 1、打开读文件
	fread, err := os.Open("./01file-copy.go")
	if err != nil {
		fmt.Println("open err:", err)
		return
	}
	defer fread.Close()

	// 2、创建写文件
	fwrite, err := os.Create("./01file-copy.bak")
	if err != nil {
		fmt.Println("write err:", err)
		return
	}
	defer fwrite.Close()

	// 3、遍历读文件获取数据放入缓冲区
	buffer := make([]byte, 1024)

	for {
		n, err := fread.Read(buffer)
		if err != nil && err == io.EOF {
			fmt.Printf("读完。n=%d", n)
			return
		} else if err != nil {
			fmt.Printf("读取出错。n=%d", n)
			return
		}

		fwrite.Write(buffer[:n]) // 读多少就写多少
		return
	}
}
~~~



**目录操作**

> 1、打开目录：OpenFile，以只读、只写、读写方式打开目录。如目录不存在，打开失败。

~~~go
func OpenFile(name string, flag int, perm FileMode) (*File, err)
~~~

参数1、name 表示要打开的目录名称。使用绝对路径(常用)或相对路径

参数2、打开目录的权限：O_RDONLY / O_WRONLY / O_RDWR

参数3、os.ModeDir

返回一个可以读写目录的文件指针。



> 2、读目录内容

写与读文件有所不同。目录中存放的是文件名和子目录名。所以使用Readdir函数来完成

~~~go
func (f *File) Readdir(n int) ([]FileInfo, error)
~~~

参数n 表示读取目录的成员个数。**通常传 -1**，表示读取目录所有的文件对象。

返回值，FileInfo类型的切片。其内部保存了文件名。error中保存错误信息。

结构体：通过返回的结果 **[]FileInfo **点开，查看源码如果。

```go
type FileInfo interface {
   Name() string       // base name of the file
   Size() int64        // length in bytes for regular files; system-dependent for others
   Mode() FileMode     // file mode bits
   ModTime() time.Time // modification time
   IsDir() bool        // abbreviation for Mode().IsDir()
   Sys() interface{}   // underlying data source (can return nil)
}
```



练习：输入目录路径，打印相关信息

~~~go
package main

// 目录操作

func main() {
	// 获取用户输入的目录路径
	fmt.Println("请输入目录路径：")
	var path string
	fmt.Scan(&path)

	// 打开目录
	file, err := os.OpenFile(path, os.O_RDONLY, os.ModeDir)
	if err != nil {
		fmt.Println("打开目录出错")
		return
	}
	defer file.Close()

	// 读取目录项 info是切片结构
	info, err := file.Readdir(-1) // -1表示全部读完
	for _, fileInfo := range info {
		if fileInfo.IsDir() {
			fmt.Println(fileInfo.Name(), "是目录")
		} else {
			fmt.Println(fileInfo.Name(), "是文件")
		}
	}
}

~~~



**初级**：判断是文件或目录

~~~go
.....

// 练习一：判断路径下的是目录或文件
if fileInfo.IsDir() {
	fmt.Println(fileInfo.Name(), "是目录")
} else {
	fmt.Println(fileInfo.Name(), "是文件")
}
~~~



**中级：**在指定目录检索特定文件 如 .jpg | .mp3 文件

~~~go
// 练习二：找到 .png图片文件
if !fileInfo.IsDir() {
	if strings.HasSuffix(fileInfo.Name(), ".png") {
		fmt.Println("图片文件有：", fileInfo.Name())
	}
}
~~~



**高级：**在指定目录下把指定类型文件拷贝到另一个目录

~~~go
// 练习三：指定类型文件拷贝到另一个目录下
if !fileInfo.IsDir() {
	if strings.HasSuffix(fileInfo.Name(), ".png") {
		cpPng(path+"/"+fileInfo.Name(), path+"/tmp/"+fileInfo.Name())
	}
}

.....
func cpPng(src, dst string) {
	// 1、打开读文件
	fread, err := os.Open(src)
	if err != nil {
		fmt.Println("open err:", err)
		return
	}
	defer fread.Close()

	// 2、创建写文件
	fwrite, err := os.Create(dst)
	if err != nil {
		fmt.Println("write err:", err)
		return
	}
	defer fwrite.Close()

	// 3、遍历读文件获取数据放入缓冲区
	buffer := make([]byte, 1024)

	for {
		n, err := fread.Read(buffer)
		if err != nil && err == io.EOF {
			fmt.Printf("读完。n=%d", n)
			return
		} else if err != nil {
			fmt.Printf("读取出错。n=%d", n)
			return
		}

		fwrite.Write(buffer[:n]) // 读多少就写多少
		return
	}
}
~~~



**高级**：统计指定目录下.txt文件内容的单词数

~~~go
/**
思路：1、打开一个 .txt文件，循环读取一行。reader := bufio.NewReader, reader.ReadBytes('\n')
2、拆分字符串，Split、Fields
3、遍历 []string 统计 单词出现的次数
*/
// 统计输入的目录下,.log所有文件中 Love或love出现的次数

func loveNum(fileName string) int {
	var total int = 0
	// 打开文件与关闭文件
	file, err := os.Open(fileName)
	if err != nil {
		return total
	}
	defer file.Close()

	//一行行读文件
	row := bufio.NewReader(file)
	for {
		buf, err := row.ReadBytes('\n')
		if err != nil && err == io.EOF {
			break
		}
		//fmt.Println(string(buf[:]))
		fmt.Println(string(buf)) //或者

		//判断当前行中是否有love
		rstString := strings.ToLower(string(buf))
		if strings.Contains(rstString, "love") {
			arr := strings.Fields(rstString)
			for _, str := range arr {
				if str == "love" {
					total++
				}
			}
		}
	}

	return total
}

func main() {
	// 1、获取输入目录
	fmt.Println("输入目录路径：")
	var path string
	fmt.Scan(&path)

	// 2、打开指定目录
	dirPath, _ := os.OpenFile(path, os.O_RDONLY, os.ModeDir)
	defer dirPath.Close()

	//3、读取目录
	if !strings.HasSuffix(path, "/") {
		path += "/"
	}
	var countLove int
	info, _ := dirPath.Readdir(-1)
	for _, file := range info {
		if !file.IsDir() && strings.HasSuffix(file.Name(), ".log") {
			fmt.Println("this is log")
			// 4、读取文件中的内容，统计 love出现的次数
			countLove += loveNum(path + file.Name())
		}
	}

	fmt.Println("love 统计的次数：", countLove)
}
~~~





Go从语言的层面就支持并行，并发程序的内存管理有时候非常复杂，而Go提供了自动垃圾回收机制。

GO语言中的并发程序主要使用两种手段来实现：goroutine和channel。



主go程退出，其他工作的goroutine也一起退出。

~~~go
func main() {
    go newTask()
    fmt.Println("main goroutine exit")
}
~~~



###### 第七章 并发

> 并行：借助多核 cpu 实现 （加钱买设备）

> 并发：宏观：用户体验上，程序在并行执行(可以打开多个应用同时跑)。

微观：多个计划任务，顺序执行。 轮换使用cpu的时间轮片。



进程并发：

​	程序：编译成功得到的二进制文件。占用磁盘空间

​	进程：运行起来程序。占用系统资源。(内存)

​	**程序：进程 = 1：n**



进程状态：初始态、就绪态、运行态、挂起(阻塞)态、终止(停止)状。

init  -> fork()  -> [一个或多个子进程 -> fork() ]....



还会产生：孤儿进程  和 僵尸进程

孤儿进程 ：父进程先于子进程终止。

僵尸进程：进程终止，父进程尚未回收，子进程残留资源存放于内核中，变成僵尸进程。



线程：轻量级进程 , 最小的执行单位。 --- cpu分配



协程并发：提高程序执行的效率。



互斥锁：建议锁，拿到锁以后，才能访问数据。没有拿到锁的线程，阻塞等待。等到拿锁的线程释放锁。

读写锁：一把锁(读属性、写属性)。写独占，读共享。写锁优先级高。



**进程(稳定性强)、线程(节省资源)、协程(效率高) 都可以完成并发。**



> **Go从语言层面就支持并行。主要是使用了两种手段：goroutine 和 channel**



~~~go
func sing() {
	for i:=0; i < 50; i++{
		fmt.Println("在唱歌")
		//time.Sleep(time.Second)
	}
}
func dance() {
	for i:=0; i < 50; i++{
		fmt.Println("在跳舞")
		//time.Sleep(time.Second)
	}
}
func main() {
	go sing()
	go dance()

	for { // 主 go 程
		;
		//time.Sleep(time.Second)
	}
}
~~~

三个协程在争夺 cpu 时间片

<img src="golang.assets/image-20201109190459826.png" alt="image-20201109190459826" style="zoom:50%;float:left;" />



以下程序是共同争夺 cpu 时间轮片，但是并不是轮流交叉打印结果：**[谁抢到资源就执行谁的函数]**

```go
func sing() {
   for i := 0; i < 50; i++ {
      fmt.Println("在唱歌")
      time.Sleep(time.Second)
   }
}
func dance() {
   for i := 0; i < 50; i++ {
      fmt.Println("在跳舞")
      time.Sleep(time.Second)
   }
}
func main() {
   go sing()
   go dance()

   var i int = 0
   for {
      i++
   }
}
```



Goroutine特性：

**主go程退出后，子 go 程也随之退出**

~~~go
func main() {
    go func(){
        for i := 0; i < 50; i++ {
          fmt.Println("在跳舞")
          time.Sleep(time.Second)
       }
    }()
    fmt.Println("-----main-----")
}
~~~



> runtime包

runtime Gosched() 用于让出CPU时间片，让出当前goroutine的执行权限，调度器安排其他等待的任努运行,并在下次再获得cpu时间轮片的时候，从该出让cpu的位置恢复执行。
有点像跑接力赛，A跑了一会碰到代码runtime .Gosched()就把接力棒交给B了，A先歇着，B继续跑。

~~~go
func main() {
	go func() {
		for {
			fmt.Println("this is goroutine test")
		}
	}()

	for {
		runtime.Gosched() //让出当前cpu时间片，如果没有这一行代码，程序执行时会一大片一大片是  打印 main 信息
		fmt.Println("this is main test")
	}
}
~~~



> runtime.Gosched()

出让当前go程所占用的cpu时间片。当再次获得cpu时，从出让位置继续回复执行。
			------时间片轮转调度算法。



以下程序中 **main函数**太快了，go还没有来得及打印信息。【即下面程序运行没有结果】

~~~go
func test() {
	defer fmt.Println("ccccc")
	fmt.Println("ddddd")
}
func main() {
	go func() {
		fmt.Println("aaaaa")
		test()
		defer fmt.Println("bbbbb")
	}()

	//time.Sleep(5 * time.Second) 如果需要打印结果
}
~~~



> runtime.Goexit() 

调用runtime.Goexit() 将立即终止当前goroutine 执行，调度器确保所有已注册defer 延迟调用被执行。

~~~go
func test() {
	defer fmt.Println("ccccc")
	runtime.Goexit() //退出当前 go 协程
	fmt.Println("ddddd") // 加不加 defer都不行执行
}
func main() {
	go func() {
		defer fmt.Println("aaaaa")
		test() // 调用函数，runtime.Goexit()是指退出当前的 goroutine；所以不会打印 ddddd字符串
		//把函数的代码回填这里就清晰了， ddddd 与 
		fmt.Println("bbbbb") //不会被打印 test()方法中已经退出了  当前 goroutine
	}()

	time.Sleep(5 * time.Second)
}
~~~



> runtime.GOMAXPROCS()

调用runtime.GOMAXPROCS()用来设置可以并行计算的CPU核数的最大值，并返回之前的值。

~~~go
func main() {
	for {
		go fmt.Print(0) // 子 go程
		fmt.Print(1)    // 主 go程
	}
	//这里默认是系统的核数；如果是多核，主与子 线程在争夺时间片，因为系统是多核，打印01交替都是短的
}


func main() {
	n := runtime.GOMAXPROCS(1) // 这里设置了 只有一个核数处理 主与子 go程；拿到资源会打印一大片相同的结果 再切到另一个go程，同样打印一大段000000000000.....或 111111111......
	fmt.Println("n = ", n) // 这里返回的是系统默认的核数
    
    //n := runtime.GOMAXPROCS(1)  如果再设置一次，这里是返回上一次设置的核数值，这里上一次设置的是 n = 1
	for {
		go fmt.Print(0) // 子 go程
		fmt.Print(1)    // 主 go程
	}
}
~~~



> runtime.Gosched()

出让当前go程所占用的cpu时间片。当再次获得cpu时,从出让位置继续回复执行。
          ---------- 时间片轮转调度算法。

> runtime.Goexit()：

return：返回当前函数调用到调用者那里去。return之前的defer注册生效。
Goexit0:结束调用该函数的当前go程。 Goexit() 之前注册的defer都生效。

> runtime.GOMAXPROCS()：

设置当前进程使用的最大cpu核数。返回上一次调用成功的设置值。首次调用返回默认值。



**runtime的其他函数简介**



文档链接

https://studygolang.com/pkgdoc

~~~go
fmt.Println(runtime.GOROOT()) // goroot 路径

fmt.Println(runtime.NumCPU()) // 返回设备的逻辑cpu数(非物理CPU数，一个物理CPU也可以分拆为多个逻辑CPU数，实际底层只有一个物理CPU)
~~~



###### 第八章 Channel

channel是Go语言中的一个核心类型，可以把它看成管道。并发核心单元通过它就可以发送或者接收数据进行通讯，这在一定程度上又进一步降低了编程的难度。

channel是一个数据类型，主要用来解决协程的同步问题以及协程之间数据共享(数据传递)的问题。

goroutine运行在相同的地址空间，因此访问共享内存必须做好同步。**goroutine 奉行通过通信来共享内存，而不是共享内存来通信。**

**引用类型 channel  可用于多个 goroutine 通讯。**其内部实现了同步，确保并发安全。



<img src="golang.assets/image-20201110085643702.png" alt="image-20201110085643702" style="zoom:50%;float:left;" />

Channel 是一种数据类型，对应一个 “管道” (/通道 FIFO)

> 定义channel变量

和 map类似，channel也是一个对应 make创建的**底层数据结构的引用** （函数传参数也是修改原值）

~~~go
make(chan Type)  //等价于 make(chan Type, 0)
make(chan Type, capacity)
~~~

容量 = 0 无缓冲 channel；容量 >0 有缓冲channel，写满时即挂起。



当我们复制一个channel或用于函数参数传递时，我们只是拷贝了一个**channel引用**,因此调用者和被调用者将引用同一个channel对象。和其它的引用类型-样，**channel 的零值也是nil。**

当参数capacity=0时，**channel 是无缓冲阻塞读写的**；当 capacity > 0 时，**channel 有缓冲、是非阻塞的，直到写满 capacity 个元素才阻塞写入**。

channel非常像生活中的管道，一边可以存放东西，另一边可以取出东西。channel 通过操作符 <-  来接收和发送数据，发送和接收数据



**Channel 一定是必须两端(写端与读端)同时满足条件，才能在channel上做数据的流动，否则阻塞**

**这可以解决加锁与写锁，锁是难把握 粒度**



**每当有一个进程启动时，系统会自动打开三个文件：标准输入(stdin =  0)、标准输出(stdout = 1)、标准错误(stderr = 2)**

当进程运行结束或超时，操作系统自动关闭三个文件。



问题引出：

以下一台打印机两个人同时在用的时候，打印结果不是预期要的

~~~go
// 定义一台打印机
func printer(str string) {
	for _, ch := range str {
		fmt.Printf("%c", ch)
		time.Sleep(time.Millisecond * 300)
	}
}
func person1() {
	printer("hello")
}
func person2() {
	printer("world")
}
func main() {
	go person1()
	go person2()

	for {
		;
	}
}
~~~



定义一个全局通道，用于阻塞使用

~~~go
var channel = make(chan int)

// 定义一台打印机
func printer(str string) {
	for _, ch := range str {
		fmt.Printf("%c", ch)
		time.Sleep(time.Millisecond * 300)
	}
}
func person1() {
	printer("hello")
	channel <- 1 //这一行需要写在这一行
}
func person2() {
	<-channel
	printer("world")
}
func main() {
	go person1() //只要获取到时间片就执行整个函数
	go person2() //如果 person2先获取到时间片，没获取到通道数据，会挂起，让出时间片给 person1执行

	i := 0
	for {
		i++
	}
}
~~~



定义 channel：make (chan 类型,  容量)

ch  :=  make( chan string )

写端： ch <- "hello" 	。写端写数据，读端不在读，阻塞。

读端：str := <- ch 		。读端读数据，同时写端不在写，读端阻塞。



channel同步----数据传递：

~~~go
func main() {
	// 如果不做什么处理，主 go程执行完程序结束，不会执行子 go 程
    go func(){
        for i:=0; i<2; i++ {
            fmt.Println("i = ", i)
		}
	}()
}
~~~



~~~go
func main() {
	ch := make(chan string) //无缓冲 channel
	fmt.Println("len(ch) = ", len(ch), " cap(ch) = ", cap(ch))
	// 长度为0 容量为0 
    // len(ch) channel中剩余未读取数据个数。如果是边写边写 都是0值

	go func() {
		for i := 0; i < 2; i++ {
			fmt.Println("len(ch) = ", len(ch), " cap(ch) = ", cap(ch))
		}
		ch <- "子go程执行完成"
	}()

	result := <-ch
	fmt.Println(result)
}
~~~



> 无缓冲管道

通道容量为0， len = 0；不能存储数据。

Channel 应用于 两个 go程中。一个读，另一个写。

具备同步的能力。读、写同步。

~~~go
func main() {
	ch := make(chan int) //无缓冲 channel

	go func() {
		for i := 0; i < 5; i++ {
			ch <- i
			fmt.Println("子go程，i = ", i)
		}
	}()

	for i := 0; i < 5; i++ {
		num := <-ch
		fmt.Println("主go程，num = ", num) //这里使用了IO,需要排队等待屏幕输出
	}
}

不一定是 一一对应输出结果，但是读取是一一对应的
/**
子go程，i =  0
主go程，num =  0
主go程，num =  1
子go程，i =  1
子go程，i =  2
主go程，num =  2
主go程，num =  3
子go程，i =  3
子go程，i =  4
主go程，num =  4
*/
~~~



<img src="golang.assets/image-20201110135845845.png" alt="image-20201110135845845" style="zoom:50%;float:left;" />



> 有缓冲channel

通道容量为非0， len(ch)：channel中剩余未读取数据个数。 Cap(ch)：通道的容量。

Channel 应用于 两个 go程中。一个读，另一个写。

缓冲区可以进行数据存储。存储到容量上限，阻塞。具备异步的能力。不需同时操作 channel缓冲区。



~~~go
func main() {
	ch := make(chan int, 3) //存满3个元素之前，不会阻塞
	fmt.Println("len=", len(ch), " cap=", cap(ch))

	go func() {
		for i := 0; i < 8; i++ {
			ch <- i
			fmt.Println("子 go 程 ：i=", i, "len=", len(ch), " cap=", cap(ch))
		}
	}()

	for i := 0; i < 8; i++ {
		num := <-ch
		fmt.Println("主go程读到：", num)
	}
}
/** 上面都是有 IO操作，耗时，都影响到我们看的预想结果了。
len= 0  cap= 3
子 go 程 ：i= 0 len= 0  cap= 3
子 go 程 ：i= 1 len= 1  cap= 3
子 go 程 ：i= 2 len= 2  cap= 3
子 go 程 ：i= 3 len= 3  cap= 3 //在主go打印信息的时候，子go又获取到了时间片
主go程读到： 0
主go程读到： 1
主go程读到： 2
主go程读到： 3
主go程读到： 4
.......
*/
~~~



> 关闭channel

确定不再向对端发送、接收数据。关闭 channel。使用 close(ch) 关闭 channel

对端可以判断 channel是否关闭：

~~~go
for {
    if num,ok := <- ch; ok ==  true {

        //如果对端已经关闭， ok --> false ，num无数据。

        //如果对端没有关闭， ok --> true ， num保存读到的数据。

    } else {
        break
    }
}
~~~



也可以使用 range 代替 ok

```go
for val := range ch {
   fmt.Println(val)
}
```

**总结：**

1、数据不发送完，不应该关闭 channel

2、已经关闭的 channel，不能再向其写数据，报错：panic：send on closed channel

3、写端已经关闭的 channel，可以从中读取数据。 读到 0 ，说明写端关闭了

​			读无缓冲channel：读到0 - 说明写端关闭

​			读有缓冲channel：如果缓冲区有数据，先读完数据，再读到是0

~~~go
func main() {
	var ch = make(chan int)
	go func() {
		for i := 0; i < 8; i++ {
			ch <- i
		}
		close(ch) //写端关闭通道
	}()

	for {
		if num, ok := <-ch; ok == true {
			fmt.Println("获取得数据：", num)
		} else {
            //n := <- ch 写端关闭了 还是可以读，只是读到默认值零值。有没有缓冲都可以读
			break
		}
	}
}
~~~



单向channel：

默认的 channel是双向的。**var ch chan int          ch = make(chan int)**

单向写 channel		**var sendCh chann <- int	 sendCh = make(chan <-  int)**

单向读 channel		**var recCh <- chan int           recCh = make(<- chan int)**



转换：

​	1、双向 channel 可以隐式转换为 任意一种单向 channel

​	2、单向 channel 不能转换为 双向 channel

传参： 传【引用】

<img src="golang.assets/image-20201110163134847.png" alt="image-20201110163134847" style="zoom:50%;float:left;" />



~~~go
func main() {
	ch := make(chan int) //双向channel

	var sendCh chan<- int = ch
	sendCh <- 123
	//<-sendCh  不能读

	var recCh <-chan int = ch
	<-recCh
	//recCh<- 123 不能写

	//反向赋值
	//var ch2 chan int = recCh 单向通道不能赋值给双向通道
}
~~~



作为参数传递：

~~~go
func send(in chan<- int) {
	in <- 123
    close(in)
}
func rec(out <-chan int) {
	num := <-out
	fmt.Println(num)
}
func main() {
	var ch chan int = make(chan int) //双向channel

	go send(ch) //双向转为写channel，传的是引用。
	rec(ch)
}
~~~



生产者消费模型：

​	缓冲区作用：1.解耦（降低生产者与消费者之前的耦合度） 

				   2. 并发 （生产者消费者数量不对等时，能保持正常通信）
   				   3. 缓存 （生产者和消费者 数据处理速度不一致时，暂存数据）

假如 写入数据是寄信人，消费者是收信人，如果没有缓冲区(邮局)，由快递人

<img src="golang.assets/image-20201110174450291.png" alt="image-20201110174450291" style="zoom:50%;float:left;" />



**模拟订单**

~~~go
// 模拟订单

type OrderInfo struct {
	Id int
}

func producer(in chan<- OrderInfo) {
	for i := 0; i < 10; i++ {
		order := OrderInfo{Id: i + 1} //模拟生成订单
		in <- order
	}
	close(in)
}

func consumer(out <-chan OrderInfo) {
	for order := range out {
		fmt.Println("订单ID为：", order.Id) //模拟处理订单
	}
}

func main() {
	ch := make(chan OrderInfo, 5) //双向channel
	go producer(ch)

	consumer(ch)
}
~~~



> **单次定时器**

Time.Timer

​	Timer是一个定时器，代表未来一个单一事件，你可以告诉timer你要等待多长时间。

~~~go
type Timer struct {
	C <-chan Time	//这是一个只读chan
	r runtimeTimer
}
~~~

它提供一个channel,在定时时间到达之前，没有数据写入timer.C 会一直阻塞。直到定时时间到，系统会自动向timer.C 这个channel 中写入当前时间，阻塞即被解除。



Timer：创建定时器，指定定时时长，定时到达后。系统会自动向定时器的成员C 写系统当前时间。（对 chan的写操作）

~~~go
type Timer struct {
	C <-chan Time  //这里的Time也是一种源码自己定义的类型
	r runtimeTimer
}
~~~



读取 Timer.C 得到 定时后的系统时间，并且完成一次  chan 的读操作。

time.After() 定时：

​		指定定时时长，定时到达后。系统会自动向定时器的成员写入 系统当前时间。

​		返回 可读 chan。读取、可获得系统写入时间。



**总结：**都是 time包。

**time.Sleep(时间值)**

**time.NewTimer(时间值)**

**time.After(时间值)**



三种设置定时器方法：

~~~go
func main() {
	// 1. sleep
	//time.Sleep(time.Second)

	// 2. Timer.C
	//fmt.Println("开始时间：", time.Now())
	//myTimer := time.NewTimer(time.Second * 2) // 创建定时器，指定 定时 时长
	//nowTime := <-myTimer.C                    // 定时满，系统自动写入系统时间
	//fmt.Println("现下时间：", nowTime)

	// 3.time.After
	fmt.Println("开始时间：", time.Now())
	nowTime := <-time.After(time.Second * 2)
	fmt.Println("现下时间：", nowTime)
}
~~~



停止定时器：myTimer.Stop()

~~~go
func main() {
	// 关闭定时器
	myTimer := time.NewTimer(time.Second * 3)
	go func() {
		<- myTimer.C //手动停止后，不会执行到这里的代码了
		fmt.Println("子 go 程，定时完毕")
	}()
	myTimer.Stop() //手动停止定时器，系统不再往 myTimer.C写入东西
	for {
		;
	}
}
~~~



重置时间：myTimer.Reset(新时间值)

~~~go
func main() {
	// 关闭定时器
	myTimer := time.NewTimer(time.Second * 3)
    myTimer.Reset(time.Second) //重新定时为1S
	go func() {
		<- myTimer.C
		fmt.Println("子 go 程，定时完毕")
	}()

	for {
		;
	}
}
~~~



总结：

1，创建定时器 myTimer  :=  time.NewTimer(2 * time.Second)

2，停止：myTimer.Stop   ----将定时器归零，  <-myTimer.C会阻塞

3，重置：myTimer.Reset(time.Second)



> **周期定时器**

myTicker := time.NewTicker(time.Second)
nowTime := <-myTicker.C

~~~go
func main() {
	quit := make(chan bool)
	fmt.Println("now：	", time.Now())
	myTicker := time.NewTicker(time.Second)

	i := 0
	go func() {
		for {
			nowTime := <-myTicker.C
			i++
			fmt.Println("nowTime：", nowTime)
			if i == 10 {
				quit <- true
				break //或者 runtime.Goexit()
			}
		}
	}()

	<-quit //阻塞状态，等着 子go程 写入值，然后整个 主go程 关闭退出
}
~~~



~~~go
//清理定时器
var timeNum = 5
var ticker = time.NewTicker(time.Second)
for tt := range ticker.C {
	timeNum --
	if timeNum < 0 {
		ticker.Stop() //关闭定时器 ticker.Stop() 终止定时器
		break
	}
	fmt.Println("执行任务：", tt)
}
~~~



总结：

单次定时器：time.Timer

周期定时器：time.Ticker



Select 作用：Go里面提供了一个关键字 select，通过select可以监听 channel上的数据流动。

~~~go
func main() {

	ch := make(chan int)
	quit := make(chan bool)

	go func() {
		for i := 0; i < 10; i++ {
			ch <- i
			time.Sleep(time.Second)
		}
		quit <- true
		runtime.Goexit()
	}()
	for {
		select {
		case num := <-ch:
			fmt.Println("取到数据：", num)
		case <-quit:
			return //这里不能使用 default，因为子 go程 休眠时 select得到的是空 都会进行 default分支
			//而且这里也不能 runtime.Goexit() 会报错，使用 return即可
		}

		fmt.Println("==========")
	}
}
~~~



select用法：参考switch case语句。**但 case后面必须是IO操作，不可以任意写判别表达式。**
注意事项：
1、监听的case中，没有满足监听条件，阻塞。

2、监听的case中，有多个满足监听条件，任选-个执行。

3、可以使用default来处理所有case都不满足监听条件的状况。通常不用(会产生忙轮询)

4、select自身不带有循环机制，需借助外层for来循环监听

5、break跳出select中的一个case选项。类似于switch中的用法。











https://www.bilibili.com/video/BV1TK4y1a7ex?p=57











正则表达式：

https://www.bilibili.com/video/BV1SJ41167HM?p=20



~~~go
golang大数据：
https://www.bilibili.com/video/BV17E41147ox?from=search&seid=4073232537706605890
~~~





![image-20200921161046317](golang.assets/image-20200921161046317.png)

![image-20200921161028474](golang.assets/image-20200921161028474.png)

![image-20200921161221905](golang.assets/image-20200921161221905.png)

![image-20200921161425235](golang.assets/image-20200921161425235.png)

























