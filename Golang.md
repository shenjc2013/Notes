```
【老男孩教育】GO语言 基础+就业班 #新版
https://www.bilibili.com/video/BV1QJ411V73q/?p=3
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
import "fmt"
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

