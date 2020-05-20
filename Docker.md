

##### 第一章 Docker容器入门

###### 1.1 简介说明

> (1) Docker是什么？

```php
#官方官网
https://www.docker.com

#文档链接
https://docs.docker.com

#仓库地址
https://hub.docker.com

Docker是一个开源的应用容器引擎，基于Go语言，并遵从Apache2.0协议开源；
容器是完全使用沙箱机制，相互之间不会有任何接口，且性能开销极低；
Docker从17.03版本之后分为CE(社区版)和EE(企业版)
```

> (2) Docker架构原理

```bash
#Docker三要素：镜像、容器、仓库

1、镜像
Docker镜像(Image)就是一个"只读"的模板，它可以是一个可运行软件(nginx、tomcat、mysql等)，也可以是一个系统(centos)。一个镜像可以用创建多个Docker容器(如：一个类可以创建多个对象)

2、容器
Docker利用容器(Container)独立运行的一个或一组应用，容器可以启动、开始、停止、删除等；每个容器都是相互隔离的、保证安全的平台

3、仓库
仓库(Repository)是集中存放镜像文件的场所，类似GitHub存放项目代码一样
Docker Hub仓库地址(https://hub.docker.com)
```

~~~php
//Docker是 C/S 结构
//Docker deamon守护进程,接收命令创建一个个镜像容器(如：nginx、mysql、php、redis等)
//容器相当于New一个镜像对象
//第三个图是一个远程镜像仓库(DockerHub)
~~~

<img src="H:\笔记本\Docker.assets\image-20200519114638987.png" alt="image-20200519114638987" style="zoom:120%;float:left;" />

> (3) Docker有什么用？

~~~bash
1、简化环境搭建，提高开发生命周期效率
2、大大简化运维工作量
3、微服务利器
~~~

> Docker容器与虚拟机区别？

Docker是一种轻量级的虚拟化技术，比传统的虚拟机性能更好

![image-20200519150207170](H:\笔记本\Docker.assets\image-20200519150207170.png)

```php
server    //表示真实电脑
Host OS   //真实电脑操作系统，如：Windows、Linux
Hypervisor//虚拟机平台，模拟硬件，如:VMWare、VirtualBox
Guest OS  //虚拟机平台上安装的操作系统，如：CentOS Linux
App		  //虚拟机操作系统上的应用，如:Nginx、MySQL等
    
Docker Engine //新一代虚拟化技术，不需要包含单独的操作系统(Docker引擎)
如果App要调用硬件资源，Docker是调用真实电脑的硬件资源
```

|            | Docker容器              | 虚拟机(vm)                  |
| :--------: | ----------------------- | --------------------------- |
|  操作系统  | 与宿主机共享OS          | 宿主机OS运行宿主机OS        |
|  存储大小  | 镜像小，便于存储和传输  | 镜像庞大(vmdk等)            |
|  运行性能  | 几乎无额外性能损失      | 操作系统额外的CPU、内存消耗 |
|   移植性   | 轻便、灵活、适用于Linux | 笨重、与虚拟化技术耠合度高  |
| 硬件亲和性 | 面向软件开发者          | 面向硬件运维者              |

总结：Docker优点-轻量级、速度快、运行应用隔离、方便维护等等



---

###### 1.2 软件安装

> #官网安装文档
> https://docs.docker.com/engine/install/

```php
# Docker安装步骤

//根据官网的步骤一步步安装
https://docs.docker.com/engine/install/ubuntu/

//...(省略)...
//获取ce社区版本
$ apt-cache madison docker-ce
docker-ce | 5:19.03.8~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
 docker-ce | 5:19.03.7~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
......

//选取版本号进行安装
$ sudo apt-get install docker-ce=5:19.03.8~3-0~ubuntu-bionic docker-ce-cli=5:19.03.8~3-0~ubuntu-bionic containerd.io

//检查安装是否成功
$ sudo docker run hello-world

//启动(并开机启动)
$ systemctl start docker
$ systemctl enable docker

//docker安装时默认创建了 docker用户组，将普通用户加入docker用户组就可以不使用sudo来操作docker
$ sudo usermod -aG docker chenglh  //加入用户组
$ newgrp - docker  //改变当前用户的有效群组，或重新登录才生效
```

测试命令

~~~bash
$ docker -v  或 docker version   #查看版本信息
$ docker images   #查看本地所有下载的镜像
~~~

HelloWorld运行原理

```php
$ sudo docker run hello-world
本地仓库未能找到该镜像，然后去远程仓库寻找及下载该镜像，也可以手动去拉取hello-world镜向
//默认是去仓库 https://hub.docker.com

$ docker pull hello-world
```

<img src="H:\笔记本\Docker.assets\image-20200519175705831.png" alt="image-20200519175705831" style="float:left;" />



---

###### 1.3 镜像仓库

配置阿里云镜像仓库

```php
#阿里云镜像
登录阿里云平台：dev.aliyun.com
进入控制面板 https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'  #利用tee命令把下面的配置写入daemon.json
{
  "registry-mirrors": ["https://j7qzahry.mirror.aliyuncs.com"]
}
EOF
//也可以vi命令编辑写入内容

#重载所有修改过的配置文件，扫描新的或有变动的单元
$ sudo systemctl daemon-reload

#重启docker
$ sudo systemctl restart docker

#检查是否成功
$ docker info
// Registry Mirrors:
//  https://j7qzahry.mirror.aliyuncs.com/
// Live Restore Enabled: false
```

```php
#下载redis
$ docker pull redis   //下载最新版本
$ docker pull redis:5.0.9  //下载指定版本
```



---

###### 1.4 基本命令

~~~php
#启动Docker
$ systemctl start docker

#停止Docker
$ systemctl stop docker

#重启Docker
$ systemctl restart docker

#开机启动Docker
$ systemctl enable docker

#查看Docker概要信息
$ docker info

#查看Docker帮助文档
$ docker --help

#查看Docker版本信息
$ docker version
~~~



---

###### 1.5 Docker镜像

```php
#列表所有镜像
$ docker images
$ docker images -a   //显示出所有的，包括中间层
$ docker images -q   //显示镜像ID
$ docker images --digests  //显示镜像的摘要信息
$ docker images --no-trunc //显示完整的镜像信息

#搜索镜像
$ docker search xxx
和到官方仓库搜索是一样的，https://hub.docker.com， OFFICIAL列判断是否是官方包

#下载镜像
$ docker pull xxx[:tag]
如果不加tag，默认是下载最新版本

#删除镜像
$ docker rmi xxx[:tag]
如果不加tag，默认是删除最新版本；如果容器正在运行，docker rmi xxx是删除不了的，但-f可强制删除
$ docker rmi -f xxx //强制删除容器
//例如：$ docker rmi -f hello-world    #其中i 表示image镜像

$ docker rmi -f 镜像1[:TAG] 镜像2[:TAG]   //删除多个镜像；空格隔开
$ docker rmi -f $(docker images -qa)    //删除所有镜像
```



---

###### 1.6 Docker容器

```php
#创建并启动容器
$ docker run [options] image [command] [arg...]
--name="容器新名字"
-i 以交互模式运行容器，通常与-t同时使用
-t 为容器重新分配一个伪输入终端，常与-i同时使用
//案例：
$ docker images
/* REPOSITORY	TAG         IMAGE ID            CREATED             SIZE
 * redis       latest      987b78fc9e38        37 hours ago        104MB
 * redis       5.0.9       5120d23bad51        4 days ago          98.3MB
 */
$ docker run -it --name redis01 987b78fc9e38(即容器ID) 或者
$ docker run -it --name redis02 redis:5.0.9
//即运行容器有两种方法，1、容器ID；2、容器名称:tag

#【总结】 docker run -it --name 别名 镜像ID  //运行一个容器，取别名，交互模式运行，分配伪终端

#列出容器
$ docker ps          		  //列出正在运行的容器
$ docker ps -a       		  //列出所有容器
$ docker ps -n  3       	  //列出最近创建的3个容器
$ docker ps -f status=exited  //查看停止运行的容器

#退出容器
exit 容器停止退出
Ctrl + P + Q 容器不停止退出

#进入容器
$ docker attach 容器ID //or 容器名[:tag]

#启动容器
$ docker start 容器ID //or 容器名[:tag]

#重启容器
$ docker restart 容器ID //or 容器名[:tag]

#停止容器
$ docker stop 容器ID //or 容器名[:tag]
//暴力关闭，尽量少用  docker kill 容器ID or 容器名

#删除容器
$ docker rm 容器ID   //只能删除停止的容器ID
$ docker rm -f 容器ID   //强制删除运行或停止的容器ID

$ docker rm -f 镜像ID1 镜像ID2   //删除多个容器；空格隔开
$ docker rm -f $(docker ps -qa)    //删除所有容器,-a所有容器，-q显示容器编号
```



---

###### 1.7 容器与宿主机

> 启动交互式容器：$ docker run -it --name 别名  镜像ID
>
> 守护式创建并启动容器： $ docker run -di --name 别名 镜像ID
>
> > --name  指定别名
> > -i    以交互模式运行容器
> >
> > -t   为容器分配一个伪终端
> >
> > -d   后台运行容器并返回容器ID
> > -P  随机生成一个端口映射，容器内部端口随机映射到主机的端口
> > -p 指定端口映射，格式   宿主端口:容器端口

```php
//启动容器，并执行 /bin/bash命令
$ docker run -it --name 别名 镜像ID  /bin/bash
// docker run -it -p 6379:6379 --name myredis 容器ID 
```

```php
#宿主机和容器之间文件拷贝
$ docker copy

$ docker cp 宿主机文件或目录  容器名字:容器目录    //宿主机文件copy to容器内
$ docker cp 容器名字:容器目录  宿主机目录
/**
 * $ docker cp /home/chenglh/composer-setup.php a9905e1f223e:/tmp
 * $ docker cp /home/chenglh/composer-setup.php a9905e1f223e:/tmp/aa.php 改名
 */

#查看容器日志
$ docker logs
日志存放具体位置：/var/lib/docker/containers/
xxxxed557a2d7d731da02def4a5b88-json.log

#查看容器进程
$ docker top 容器ID

#进入容器执行命令
$ docker exec -it 容器名称/容器ID 执行命令
直接操作容器，执行完回到宿主主机终端；
我们一般用于 启动容器里的应用，比如：Tomcat、Nginx、Elasticsearch等等
#例如：ls -l  /var/wwwroot/
# docker exec -it 容器ID  ls -l /var/wwwroot/
```



























~~~
# 下载常用镜像
$ docker pull php

$ docker run -d -p 80:80 名称或ID
    //run  把镜像放入容器中；
	//-d   后台运行，并返回ID；
	//-p   端口映射

$ docker start 容器ID 
$ docker stop 容器ID
~~~



```php
#如果出现错误信息
IPv4 forwarding is disabled. Networking will not work.
解决办法：
$ sodu vi /etc/sysctl.conf
加入 net.ipv4.ip_forward=1
$ sudo systemctl restart network   //重启
```




