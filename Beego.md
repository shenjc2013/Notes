```
Golang教程_Go语言Beego入门实战教程-2020年4月录制每周更新5讲
https://www.bilibili.com/video/BV1Vi4y1t71d?from=search&seid=192166541219073018
```



#### 第一节 基础介绍



###### 1.1 语言介绍

> Go语言即Golang，是Google公司2009年11月正式对外公开的一门编程语言。2012年第一个正式版本Go1.0发布

(1)、根据Go语言开发者自述，近10多年，从单机时代的C语言到现在互联网时代的Java， 都没有令人满意的开发语言，而 C++往往给人的感觉是，花了100%的经历，却只有60%的开发效率，产出比太低，Java和C#的哲学又来源于 C++。并且随着硬件的不断升级，这些语言不能充分的利用硬件及CPU。因此，一门高效、简洁、开源的语言诞生了。 

(2)、Go语言拥有静态编译语言的安全和高性能，还达到了动态语言开发速度和易维护性。
**Go语言 = C + Python**，即既有C语言程序的运行速度，又有Python语言的开发速度



###### 1.2  应用场景

Go语言是非常有潜力的语言，在是目前互联网非常热门的几个领域， 比如：

- WEB开发
- 区块链开发
- 大型游戏服务端开发
- 分布式 / 云计算开发



###### 1.3 开源项目

```php
nsq			//bitly 开源的消息队列系统，性能非常高，目前他们每天处理数十亿条的消息 
docker		//基于 lxc 的一个虚拟打包工具，能够实现 PAAS 平台的组建 
packer		//用来生成不同平台的镜像文件，例如 VM、vbox、AWS 等，作者是 vagrant 的作者 
skynet		//分布式调度框架 
Doozer		//分布式同步工具，类似 ZooKeeper 
Heka		//mazila 开源的日志处理系统 
cbfs		//couchbase开源的分布式文件系统
tsuru		//开源的PAAS平台，和SAE实现的功能一样
groupcache	//Memcahe作者写的用于Google下载系统的缓存系统
god			//类似Redis的缓存系统，支持分布式和扩展性
gor			//网络流量抓包和重放工具
```



###### 1.4 公司应用

> 哪些大公司在用Go语言？

```php
#1、Google基于Go有很多优秀的项目，比如：
https://github.com/kubernetes/kubernetes
https://github.com/google        //【查看更多Google的Go开源项目】 

#2、Facebook 专门在Github上建立了一个开源组织facebookgo
https://github.com/facebookgo    //【查看facebook开源的项目，如著名的是平滑升级的grace】

#3、腾讯，尤其是Docker容器化这一块，他们在15年已经做了docker万台规模的实践，可参考
http://www.infoq.com/cn/articles/tencent-millions-scale-docker-application-practice 

#4、百度，运维的一个BFE项目，负责前端流量的接入
http://www.infoq.com/cn/presentations/application-of-golang-in-baidu-frontend

#5、阿里

#6、京东
京东云消息推送系统、云存储，以及京东商城等都有使用Go做开发。

#7、小米对Golang支持，运维监控系统的开源
http://open-falcon.com

#此外，小米互娱、小米商城、小米视频、小米生态链等团队都在使用Golang

#8、360对一个是开源的日志搜索系统Poseidon，托管在Github上
https://github.com/Qihoo360/poseidon
```



###### 1.5 开发环境

```php
#1、下载安装
https://golang.org/dl		#官网地址
https://golang.google.cn/dl	#官方镜像

#2、安装
双击一步步安装即可

#3、验证(cmd或Git命令面板)
$ go version       #查看版本信息
go version go1.14.2 windows/amd64
$ go env		   #查看Go环境参数
set GO111MODULE=
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\Administrator\AppData\Local\go-build
set GOENV=C:\Users\Administrator\AppData\Roaming\go\env
set GOEXE=.exe
set GOFLAGS=
...
```



> 注意：Go1.11版本之后无需手动配置环境变量，使用go mod管理项目，也不需要把项目放到GOPATH指定目录下，可以在你磁盘的任何位置新建一个项目。

```php
##开发工具Vscode

#1、下载安装vscode软件
https://code.visualstudio.com
```



~~~go
//自定义一个"字符串"，输出里边的"字符"
var str = "this"
fmt.Printf("值：%v 原样输出：%c  类型：%T", str[2],str[2],str[2]) //值：105 原样输出：i  类型：uint8

//4、一个汉字占用3个字节(utf-8)，一个字母占用1个字节
//不能使用 unsafe.Sizeof() 来查看string类型数据所占用的存储空间
var userName, ename = 'i', "this"
fmt.Printf("%v ; %v\n", unsafe.Sizeof(userName),unsafe.Sizeof(ename))
// 4 ; 16

//使用len(str)为测试字节长度
//golang中汉字使用的是utf-8编码， 编码后的值就是int类型，
~~~



~~~go
//字符串遍历
for // byte
range //rune

//修改字符串
//先将其转换成[]rune或[]byte，完成后再转换成 string，需要重新分配内存，并复制字节数组

//英文字符串
str := "big"
byteStr := []byte(str) //强制类型转换
byteStr[0] = 'p'
fmt.Println(string(byteStr))

//中英字符串
str1 := "你好,hello"
byteStr1 := []rune(str1)
byteStr1[0] = '我'
fmt.Println(string(byteStr1))
~~~



Go语言中的字符有以下两种：

1. uint8类型，或者叫  byte 型，代表 ASCII码的一个字符
2. urne类型，代表一个 utf-8字符 //处理中英，韩日等utf-8



**类型转换**

~~~go
//整型
var a int8 = 20
var b int16 = 40
c := int16(a) + b

//浮点型
var a float32 = 20
var b float64 = 40
c := float64(a) + b 

//整型与浮点型
var a float32 = 20.23
var b int = 40
c := a + float32(b)

//建议从低位向高位转换，如果转换不成功就会溢出。出现和我们预想的不一样结果
~~~



**其他类型转string**



> 1、使用 Sprintf

~~~go
//Sprintf 使用时 int - %d ; float - %f ; bool - %t ; byte - %c

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
值："20",类型：string
值："12.542",类型：string
值："true",类型：string
值："a",类型：string
*/
~~~



> 2、strconv包，把其他类型转换成 string类型

~~~go
var i int64 = 20


str1 := strconv.FormatInt(i, 10) // 参数1：int64数值，参数2：进制数
fmt.Printf("值：%#v,类型：%T\n", str1, str1)
~~~





