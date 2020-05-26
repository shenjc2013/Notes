#### 第一节 基础介绍

###### 1.1 语言介绍

```
Go语言即Golang，是Google公司2009年11月正式对外公开的一门编程语言。
2012年第一个正式版本Go1.0发布

(1)、根据Go语言开发者自述，近10多年，从单机时代的C语言到现在互联网时代的Java， 都没有令人满意的开发语言，而 C++往往给人的感觉是，花了100%的经历，却只有60%的开发效率，产出比太低，Java和C#的哲学又来源于 C++。并且随着硬件的不断升级，这些语言不能充分的利用硬件及CPU。因此，一门高效、简洁、开源的语言诞生了。 

(2)、Go语言拥有静态编译语言的安全和高性能，还达到了动态语言开发速度和易维护性。
Go语言 = C + Python，即既有C语言程序的运行速度，又有Python语言的开发速度
```

###### 1.2  应用场景

```
Go语言是非常有潜力的语言，在是目前互联网非常热门的几个领域， 比如：
WEB开发
区块链开发
大型游戏服务端开发
分布式/云计算开发
```

###### 1.3 成功项目

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

```bash
##哪些大公司在用Go语言

#1、Google基于Go有很多优秀的项目，比如：
https://github.com/kubernetes/kubernetes
https://github.com/google        //查看更多Google的Go开源项目 

#2、Facebook 专门在Github上建立了一个开源组织facebookgo
https://github.com/facebookgo     //查看facebook开源的项目，如著名的是平滑升级的grace

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

```bash
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
......
```

> 注意：Go1.11版本之后无需手动配置环境变量，使用go mod管理项目，也不需要把项目放到GOPATH指定目录下，可以在你磁盘的任何位置新建一个项目。Go1.13以后可以彻底不要GOPATH 了

###### 

```bash
##开发工具Vscode

#1、下载安装vscode软件
https://code.visualstudio.com


```

