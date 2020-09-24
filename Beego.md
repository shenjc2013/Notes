```go
Beego框架学习与使用
```

##### 第一章 基础入门

###### 1.1  Go语言

为什么使用Go?

语法简洁，语言优势。

- 静态类型，编译型语言
- 原生支持http服务
- 原生支持并发编程
- 对微服务有较好支持
- 支持长链接



Go官网链接

~~~go
https://golang.google.cn
~~~



Go下载安装

~~~go
https://golang.google.cn/dl
~~~



Mac环境搭建Go

- GOROOT 就是Go的安装环境
- GOPATH 就是工作目录，包含 src、pkg、bin文件夹
- 注意：不要把gopath设置成安装目录

~~~go
# mkdir /usr/local/go
# cd /usr/local/go

//1、下载
# wget https://golang.google.cn/dl/go1.15.2.darwin-amd64.tar.gz

//2、解压
# tar -zxvf go1.15.2.darwin-amd64.tar.gz

//3、配置
# vim ~/.bash-profile
export GOROOT=/usr/local/go
export GOPATH=/Users/chenglh/GoRoot
export PATH=/usr/local/go/bin:$GOPATH/bin:$PATH

//4、生效
source ~/.bash_profile
~~~



检测Go是否安装成功

~~~go
go version
~~~



###### 1.2 框架介绍

Beego 简单化：RESTful 支持、MVC 模型；内置了强大的模块，包括 Session、缓存操作、日志记录、配置解析、性能监控、上下文操作、ORM 模块、请求模拟等强大的模块；支持高并发等。



框架地址：

~~~go
https://beego.me
~~~



Beego安装

~~~go
//Beego安装是典型的go安装包安装，命令行
go get github.com/astaxie/beego
~~~

Bee工具安装

~~~go
//bee 工具是一个为了协助快速开发beego项目而创建的项目
go get github.com/beego/bee
~~~

Bee可执行文件默认存放在$GOPATH/bin里面，所以需要把$GOPATH/bin配置到环境变量。

~~~go
$ bee version
~~~



Bee命令

~~~go
bee new 创建一个新的beego项目

bee api	只是用于api，不会创建static和views目录

bee run	运行beego项目

bee pack 打包编译生成文件，注意 mac下打包到linux 使用命令：bee pack -be GOOS=linux
~~~

创建项目：

~~~go
//bee new fyouku  //前端
bee api fyoukuApi //接口
~~~



路由设置两种方式：

~~~go
//方法一：
beego.Router("/", &controllers.MainController{})

//方法二：
//@router /channel/list:key [get]
~~~



LiteIDE x

~~~go
//打印输出字符串
func (c *MainController) GetHello() {
    var (
		title string
	)
	title = "Hello world"
    c.Ctx.WriteString(title)
}
~~~

配置路由方法一： routers/router.go

~~~go
func init() {
	...
    beego.Router("hello", &controllers.MainController{}, "get:GetHello")
}
~~~





