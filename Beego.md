#### 第一章

###### 1、Go语言介绍

​    Go 即 Golang，是 Google 公司 2009 年 11 月正式对外公开的一门编程语言。 

​    根据 Go 语言开发者自述，近 10 多年，从单机时代的 C 语言到现在互联网时代的 Java， 都没有令人满意的开发语言，而 C++往往给人的感觉是，花了 100%的经历，却只有 60%的 开发效率，产出比太低，Java 和 C#的哲学又来源于 C++。并且，随着硬件的不断升级，这些语言不能充分的利用硬件及CPU。因此，一门高效、简洁、开源的语言诞生了。 

​    Go 语言不仅拥有静态编译语言的安全和高性能，而且又达到了动态语言开发速度和易维护性。有人形容 Go 语言：**Go = C + Python** , 说明 Go 语言既有 C 语言程序的运行速度，又能达到 Python 语言的快速开发。

​    **Go** **语言是非常有潜力的语言**，是因为它的应用场景是目前互联网非常热门的几个领域， 比如 WEB 开发、区块链开发、大型游戏服务端开发、分布式/云计算开发。国内比较知名的也是使用 Go 语言开发的。

###### 2、Go 语言成功的项目

```
nsq：bitly 开源的消息队列系统，性能非常高，目前他们每天处理数十亿条的消息 
docker：基于 lxc 的一个虚拟打包工具，能够实现 PAAS 平台的组建 
packer：用来生成不同平台的镜像文件，例如 VM、vbox、AWS 等，作者是 vagrant 的作者 
skynet：分布式调度框架 
Doozer：分布式同步工具，类似 ZooKeeper 
Heka：mazila 开源的日志处理系统 
cbfs：couchbase 开源的分布式文件系统
tsuru：开源的 PAAS 平台，和 SAE 实现的功能一模一样
groupcache：memcahe 作者写的用于 Google 下载系统的缓存系统
god：类似 redis 的缓存系统，但是支持分布式和扩展性
gor：网络流量抓包和重放工具
```

###### 3、哪些大公司在用go语言？

**Google** 

这个不用多做介绍，作为开发 Go 语言的公司，当仁不让。Google 基于 Go 有很多优秀的项目，比如：https://github.com/kubernetes/kubernetes ，大家也可以在 Github 上https://github.com/google/ 查看更多 Google 的 Go 开源项目。 

**Facebook** 

Facebook 也在用，为此他们还专门在 Github 上建立了一个开源组织 facebookgo，大家可以通过 https://github.com/facebookgo 访问查看 facebook 开源的项目，比如著名的是平滑升级的 grace。

**腾讯**

腾讯作为国内的大公司，还是敢于尝试的，尤其是 Docker 容器化这一块，他们在 15 年已经做了 docker 万台规模的实践，具体可以参考http://www.infoq.com/cn/articles/tencent-millions-scale-docker-application-practice 

**百度**

目前所知的百度的使用是在运维这边，是百度运维的一个 BFE 项目，负责前端流量的接入。他们的负责人在 2016 年有分享，可以看下这个http://www.infoq.com/cn/presentations/application-of-golang-in-baidu-frontend

**阿里**

**京东**

京东云消息推送系统、云存储，以及京东商城等都有使用 Go 做开发。

**小米**

小米对 Golang 的支持，莫过于运维监控系统的开源，也就是 http://open-falcon.com/ 

此外，小米互娱、小米商城、小米视频、小米生态链等团队都在使用 Golang。 

**360** 

360 对一个是开源的日志搜索系统 Poseidon，托管在 Github 上， https://github.com/Qihoo360/poseidon

###### 4、Go 环境 win 环境搭建

1、下载安装 Golang Go 
官网下载地址：https://golang.org/dl/
官方镜像站：https://golang.google.cn/dl/

2、安装软件

a)、双击下一步下一步进行安装

b)、验证有没有安装成功

```
> go version
```

<img src="H:\笔记本\Beego.assets\image-20200505221043012.png" alt="image-20200505221043012" style="zoom:100%;float:left" />

c)、查看 go 环境

```
> go env
```

<img src="H:\笔记本\Beego.assets\image-20200505221401678.png" alt="image-20200505221401678" style="zoom:100%;float:left;" />

说明：Go1.11 版本之后无需手动配置环境变量，使用 go mod 管理项目，也不需要非得把项目放到 GOPATH 指定目录下，你可以在你磁盘的任何位置新建一个项目。 Go1.13 以后可以彻底不要 GOPATH 了。

###### 5、Go 语言开发工具 Vscode 配置

1、下载安装 vscode软件

 https://code.visualstudio.com/