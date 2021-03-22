##### 第一章 Docker容器入门

###### 1.1 简介说明

> (1) Docker是什么？

​       Docker是一个开源的应用容器引擎，基于Go语言，并遵从Apache2.0协议开源；容器是完全使用沙箱机制，相互之间不会有任何接口，且性能开销极低；Docker从17.03版本之后分为CE(社区版)和EE(企业版)

```php
##官方官网
https://www.docker.com

##文档链接
https://docs.docker.com

##镜像仓库
https://hub.docker.com
```



> (2) Docker架构原理

**Docker三要素：镜像、容器、仓库**

```php
#1、镜像
Docker镜像(Image)就是一个"只读"的模板，它可以是一个可运行软件(nginx、tomcat、mysql等)，也可以是一个系统(centos)。一个镜像可以用创建多个Docker容器(如：一个类可以创建多个对象)

#2、容器
Docker利用容器(Container)独立运行的一个或一组应用，容器可以启动、开始、停止、删除等；每个容器都是相互隔离的、保证安全的平台

#3、仓库
仓库(Repository)是集中存放镜像文件的场所，类似GitHub存放项目代码一样
Docker Hub仓库地址(https://hub.docker.com)
```

~~~php
//Docker是 C/S 结构
//Docker deamon守护进程,接收命令创建一个个镜像容器(如：nginx、mysql、php、redis等)
//容器相当于New一个镜像对象
//第三个图是拉取远程镜像仓库(DockerHub)
~~~

<img src=".\Docker.assets\image-20200519114638987.png" alt="image-20200519114638987" style="zoom:120%;float:left;" />



> (3) Docker有什么用？

~~~bash
1、简化环境搭建，提高开发生命周期效率
2、大大简化运维工作量
3、微服务利器
~~~



> Docker容器与虚拟机区别？

Docker是一种轻量级的虚拟化技术，比传统的虚拟机性能更好

![image-20200519150207170](.\Docker.assets\image-20200519150207170.png)

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

> #docker安装文档
> https://docs.docker.com/engine/install/

```php
##Docker安装步骤##

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

<img src=".\Docker.assets\image-20200519175705831.png" alt="image-20200519175705831" style="float:left;" />



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
$ docker images -a   	   //显示出所有的，包括中间层
$ docker images -q   	   //显示镜像ID
$ docker images --digests  //显示镜像的摘要信息
$ docker images --no-trunc //显示完整的镜像信息
//例如：docker images -aq

#搜索镜像
$ docker search xxx  [OPTIONS]
//和到官方仓库搜索是一样的，https://hub.docker.com，这命令只是请求接口，
//OFFICIAL列判断是否是官方包
//[options] 可选参数
//    --no-trunc  显示完整的镜像描述
//    -s          列出收藏数不小于指定值的镜像
//    --automated 只列出DockerHub自动构建类型的镜像
//例如：docker search -s 40 tomcat --no-trunc

#下载镜像
$ docker pull xxx[:tag]
##【如果不加tag，默认是下载最新版本】
//例如：
// $ docker pull centos
// $ docker pull tomcat:8.5.55-jdk11-openjdk
/**
 * 在 docker pull拉取镜像时，镜像包会被拆分成多个小包，Tomcat镜像抽像层次结构如下图所示
 * Linux层微内核，JDK层，Tomcat层，类型千层饼一样组合而成
 */

#删除镜像
$ docker rmi xxx[:tag]
##如果不加tag，默认是删除最新版本；如果容器正在运行，docker rmi xxx是删除不了的，但-f可强制删除
$ docker rmi -f xxx //强制删除镜像，#其中i 表示image镜像
//例如：$ docker rmi -f hello-world

$ docker rmi -f 镜像1[:TAG] 镜像2[:TAG]   //删除多个镜像；空格隔开，Tag没指定默认latest
$ docker rmi -f $(docker images -qa)    //删除所有镜像

#查看容器元信息
$ docker inspect 容器ID
```

<img src=".\Docker.assets\image-20200521102129526.png" alt="image-20200521102129526" style="zoom:100%;float:left;" />

---

###### 1.6 Docker容器

```php
#创建并启动容器
$ docker run [options] 容器ID/容器名:TAG [command] [arg...]
//--name 容器新名字
//-i 以交互模式运行容器，通常与-t同时使用
//-t 为容器重新分配一个伪输入终端，常与-i同时使用
//案例：
$ docker images
/* REPOSITORY	TAG         IMAGE ID            CREATED             SIZE
 * redis       latest      987b78fc9e38        37 hours ago        104MB
 * redis       5.0.9       5120d23bad51        4 days ago          98.3MB
 */
$ docker run -it --name redis01 987b78fc9e38   //容器ID
$ docker run -it --name redis02 redis:5.0.9	   //容器名:tag
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
$ docker rm 容器ID   //只能删除停止的容器ID，如果正在运行是会报错误信息的
$ docker rm -f 容器ID   //强制删除运行或停止的容器ID

$ docker rm -f 容器ID1 容器ID2   //删除多个容器；空格隔开
$ docker rm -f $(docker ps -qa)    //删除所有容器,-a所有容器，-q显示容器编号
```



---

###### 1.7 容器与宿主机

> (1)、启动交互式容器：$ docker run -it --name 别名  镜像ID
>
> (2)、守护式创建并启动容器： $ docker run -di --name 别名 镜像ID

> 参数说明：
>
> --name  指定别名
> -i    		以交互模式运行容器
> -t   		 为容器分配一个伪终端
> -d   		后台运行容器并返回容器ID
> -P  		 随机生成一个端口映射，容器内部端口随机映射到主机的端口
> -p 		  指定端口映射，格式   宿主端口:容器端口



**Docker容器宿主机端口映射**

```php
//启动容器，并执行 /bin/bash命令
$ docker run -it --name 别名 镜像ID  [/bin/bash]   //默认是/bin/bash进入容器模式，加不加都可
//例如：
// $ docker run -it -p 6379:6379 --name myredis 987b78fc9e38  //#创建并进入容器
// $ docker run -di --name mycentos 470671670cac    //#创建并返回容器ID，不进容器，后台运行
// $ docker run -di -p 8888:8080 --name mytomcat 1b6b1fe7261e

//查看开放端口：$ netstat -tlpn

//随机生成一个端口映射
$ docker run -it -P tomcat:8.5
//0.0.0.0:32768->8080/tcp  tomcat:8.5容器的端口是dockerfile已经定义好了的，宿主机端口才是随机生成的

//iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 8888 -j ACCEPT
//service iptables save #保存iptables规则
```

Docker宿主机与容器间文件拷贝

```php
#宿主机和容器之间文件拷贝
//$ docker copy 命令
$ docker cp 宿主机文件或目录  容器名字:容器目录    //宿主机文件copy to容器内
$ docker cp 容器名字:容器目录  宿主机目录
/**
 * $ docker cp /home/chenglh/composer-setup.php a9905e1f223e:/tmp
 * $ docker cp /home/chenglh/composer-setup.php a9905e1f223e:/tmp/aa.php //改名
 */

#查看容器日志
$ docker logs
##日志存放具体位置：/var/lib/docker/containers/
//xxxxed557a2d7d731da02def4a5b88-json.log

#查看容器里进程
$ docker top 容器ID

#进入容器执行命令(回到宿主机)
$ docker exec -it 容器名称/容器ID 执行命令
##直接操作容器，执行完回到宿主主机终端；
//我们一般用于 启动/重启/关闭容器里的应用，比如：Tomcat、Nginx、Elasticsearch等
#例如命令：ls -l  /var/wwwroot/
# docker exec -it 容器ID  ls -l /var/wwwroot/

#进入容器(不会回到宿主机)
$ docker exec -it 容器ID /bin/bash
//例如：docker exec -it 739161c5902b /bin/bash
/**
 * 如果报错误信息：Error loading config file XXX.dockerconfig.json - stat /home/XXX/.docker/config.json: permission denied
 * docker的文件夹的权限问题，解决方法执行以下命令：
 * $ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
 * $ sudo chmod g+rwx "/home/$USER/.docker" -R
 */
```



---

###### 1.8 Docker commit

提交运行时容器成为镜像，即一个镜像配置完成后，可以把当前镜像提交生成另一个镜向。

~~~php
$ docker exec -it 739161c5902b /bin/bash   //进入tomcat容器
//需求：修改了一些配置信息，现生成镜像，传输到另一台服务器使用
$ docker commit -a="作者" -m="备注信息" 运行时容器ID  新镜像名称

//例如：
$ docker commit -a="cheng lh" -m="test mytomcat" 739161c5902b java1234/tomcat:1.2
//如果不写TAG，默认值是 lastest
$ docker images
//REPOSITORY       TAG       IMAGE ID      CREATED             SIZE
//java1234/tomcat  1.2       68f4a56a9459  6 seconds ago       647MB
//tomcat           latest    1b6b1fe7261e  5 days ago          647MB
~~~

> 一、推送镜像到Hub服务器


上面订制打包了镜像java1234/tomcat ,tag是1.2，现在需要把镜像发布到Hub

步骤一：

https://hub.docker.com/ 注册账号，得到docker id和密码

步骤二：
用docker login登陆hub服务器

```php
$ docker login
//输入 账号密码
```

步骤三：
docker push推送

$ docker push  java1234/tomcat:1.2

然后在 hub.docker.com个人中心的仓库可以看到当前推送的镜像，可以下载，删除等操作

> 二、推送阿里云镜像服务器

很多时候，中小公司为了方便搭建私有仓库，直接使用稳定的阿里云镜像仓库，方便公司内部业务系统直接摘取镜像；

```php
#步骤一：
进入 https://cr.console.aliyun.com 阿里云镜像控制台，需要使用支付宝登录即可；

#步骤二：
在左侧菜单中：容器镜像服务 > 访问凭证  设置获取凭证 --> 固定密码

#步骤三：
在左侧菜单中：容器镜像服务 > 命名空间 添加命名空间，如：clhphp

#步骤四：
在左侧菜单中：容器镜像服务 > 镜像仓库，然后创建镜像仓库，选择或填写命名空间、仓库名称、仓库类型、摘要
//如图下所示
下一步的代码源，选择“本地仓库” ，点击完成

#步骤五：
在左侧菜单中：容器镜像服务 > 镜像仓库  在列表中选择“管理”，如下图所示。

里边有具体操作命令：
##1.登录阿里云Docker Registry
$ sudo docker login --username=2572670000@qq.com registry.cn-hangzhou.aliyuncs.com
Password: *********
//用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

##2.将镜像推送到Registry
$ sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/phpclh/php1234:[镜像版本号]
$ sudo docker push registry.cn-hangzhou.aliyuncs.com/phpclh/php1234:[镜像版本号]

##3.从Registry中拉取镜像
$ sudo docker pull registry.cn-hangzhou.aliyuncs.com/phpclh/php1234:[镜像版本号]
```

<img src=".\Docker.assets\image-20200522160048909.png" alt="image-20200522160048909" style="zoom:100%;float:left;" />



![image-20200522160745393](.\Docker.assets\image-20200522160745393.png)



---

###### 1.9 容器目录挂载

> 【简介】容器目录挂载
> 即可以在创建容器的时候，将宿主机的目录与容器内的目录进行映射，我们就可以实现宿主机和容器目录的双向数据同步。

> 【作用】使用cp命令来实现数据传递，但这种方式比较麻烦；
> 我们通过容器目录挂载，能够轻松实现代码上传，配置修改，日志同步等需求；

> 【实现】
> 语法： $ docker run -it -v /宿主机目录:/容器目录  镜像名
>
> 多目录挂载：$ docker run -it  -v  /宿主机目录:/容器目录    -v  /宿主机目录2:/容器目录2     镜像名
>
> 注意：如果你同步的是多级目录，可能会出现权限不足的提示；
> 这是因为Linux的安全模块 sellinux把权限禁掉了，我们需要添加 --privlleged=true来解决挂载的目录没有权限问题

```php
#挂载目录只读 “ro”
$ docker run -it -v  /宿主目录:/容器目录:ro  镜像ID

#网站根目录挂载
$ docker run -it -v /home/chenglh/www.tswoft.com/:/var/wwwroot/www.tswoft.com/ 470671670cac   //镜像ID
$ docker exec -it 437965bada89 /bin/bash   //容器ID
```



---

##### 第二章 项目部署

Mac 更换镜向地址

~~~php
cd ~
cd .docker
vi daemon.json
 
#将文件内容替换为下面内容
{
    "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
~~~



**下载 centos**

~~~php
docker pull centos:7
~~~



~~~php
# cd /Users/chenglh/docker
# mkdir conf
# cd conf
# vi php.ini
# mkdir conf.d
~~~



###### 2.1 安装PHP

~~~php
docker pull php:7.1-fpm

//先启动容器获取原生的配置文件
# cd /Users/chenglihui/docker
# docker cp 404422aadfasf容器ID:/usr/local/etc/php php

docker run -itd -p 9000:9000 -v /Users/chenglihui/working:/usr/share/nginx/html -v /Users/chenglihui/docker/php:/usr/local/etc/php --name php-fpm7.1 404422fc039e
//挂载，将主机项目中的目录挂载到容器的/usr/share/nginx/html
~~~



<img src="Docker.assets/image-20210306190828645.png" alt="image-20210306190828645" style="zoom:50%;float:left;" />



###### 2.2 安装Nginx

~~~php
docker pull nginx

//先运行一个 nginx 容器，目的是获取它原生的配置文件
docker run -itd  -p 8081:80 -v /Users/chenglihui/working:/usr/share/nginx/html --name nginx f6d0b4767a6c

在宿主主机
# cd /Users/chenglihui/docker/nginx
# docker cp 8c92517383be容器ID:/etc/nginx/conf.d conf.d
这里是拿回来default.conf文件

删除当前nginx容器
docker stop 8c92517383be
docker rm -f 8c92517383be

docker run -itd  -p 8081:80 -v /Users/chenglihui/working:/usr/share/nginx/html -v  /Users/chenglihui/docker/nginx/conf.d:/etc/nginx/conf.d  --name nginx f6d0b4767a6c
//挂载，将主机项目中的目录挂载到容器的/usr/share/nginx/html

docker exec -it 64482af15417 /bin/bash

进入容器后需要安装 vim
# apt-get update
# apt-get install vim
~~~

~~~php
location ~ \.php$ {
    root           /usr/share/nginx/html;
    //注意这里
    fastcgi_pass   phpfpm:9000; 
    fastcgi_index  index.php;
    #fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    //注意这里，和phpfpm挂载目录一致
    fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html/$fastcgi_script_name;
    include        fastcgi_params;
}

//也可以使用 php容器的IP地址
~~~



###### 2.3 安装MySQL5.7

```php
#到hub查找下载mysql
$ docker pull mysql:5.7
```

```php
#第一步，运行MySQL5.7
$ docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 MySQL镜像ID
##开放Mysql端口
//iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT

#第二步，进行MySQL容器
$ docker exec -it 容器ID /bin/bash
##确认以下目录路径
//	/etc/mysql/conf.d     //配置目录
//  /var/log/mysql        //日志目录
//  /var/lib/mysql/mysql  //数据库目录

#宿主机里/home目录下新建mysql目录，把上面三个路径映射到mysql目录下
$ docker cp  容器ID:/etc/mysql/conf.d  /home/mysql/conf/
$ docker cp  容器ID:/var/log  /home/mysql/log/
$ docker cp  容器ID:/var/lib/mysql/  /home/mysql/mysql/

//以上步骤是先拿到MySQL环境数据本地化，第三步操作是启动并同步对应文件数据
//关闭容器或删除容器

#第三步：启动MySQL容器
$ docker run --name mysql57 -p 3306:3306 -d -v /etc/mysql/conf.d/:/home/mysql/conf/
-v /var/log/:/home/mysql/log/ -v /var/lib/mysql/:/home/mysql/mysql/ -e MYSQL_ROOT_PASSWORD=123456  MySQL镜像ID

##在容器内登录客户端
$ docker exec -it 容器ID /bin/bash
$ mysql -u root -p
Password:123456
mysql > ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

#第四步：连接数据库(Navicat工具连接)
使用客户端工具连接数据库，Host使用宿主机的IP地址

#第五步：程序连接数据库
注意：运行的项目里的数据库连接地址，要写docker里分配给mysql容器所在的虚拟IP地址，容器间通信IP

##查看容器元信息
$ docker inspect 容器ID
//  "IPAddress": "172.17.0.2",
```





































~~~php
docker network ls

//bridge 桥接方式

docker exec -it mynginx /bin/bash

//ifconfig 命令没有
cat /etc/hosts
~~~



compose的安装

~~~php
https://docs.docker.com/compose/install/
~~~



mac直接就有compose

~~~php
/usr/local/bin/docker-compose

docker-compose -version

cd /User/chenglh/docker/compose
    
vim docker-compose.yml

docker-compose up --help

docker-compose up -d

docker-compose rm

nginx:
 container_name:nginx
 image:
 ports: - 90:80
 privileged: true
 volumes:
 - /xxx/nginx/conf/nginx.conf:/etc/nginx/nginx.conf


docker network ls

docker network inspect compose_default
~~~





~~~php
http://www.bejson.com/validators/yaml_editor/

版本设置
https://docs.docker.com/compose/compose-file/compose-file-v3/
~~~









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



~~~php
version: '3.8'
services:
    nginx:
        # 容器名称
        container_name: "nginx"
        image: f6d0b4767a6c
        #restart: always
        # 端口映射
        ports:
            - "9080:80"
        privileged: true
        environment:
            - TZ=Asia/Shanghai
        # 依赖关系 先跑php
        depends_on:
            - "php"
        # 数据卷
        volumes:
            # 映射主机./conf.d目录到容器/etc/nginx/conf.d目录
            - "/Users/chenglihui/docker/nginx/conf.d:/etc/nginx/conf.d"
            - "/Users/chenglihui/docker/nginx/html:/usr/share/nginx/html"
        networks:
            - "app-net"
            #    ipv4_address: 192.168.1.2
    mysql:
        image: a70d36bc331a
        ports:
            - "3306:3306"
        volumes:
            - /Users/chenglihui/docker/mysql/data:/var/lib/mysql:rw
            - /Users/chenglihui/docker/mysql/conf:/etc/mysql/conf.d
            - /Users/chenglihui/docker/mysql/logs:/data/mysql/logs
        #restart: always
        # 环境变量
        environment:
            - TZ=Asia/Shanghai # 设置时区
            # mysql密码
            - MYSQL_ROOT_PASSWORD=12345678
        container_name: "mysql"
        networks:
           - "app-net"
           #     ipv4_address: 192.168.1.4     
    redis:
        # 指定镜像
        image: 621ceef7494a
        # restart: always
        ports:
        # 端口映射
            - 6379:6379
        volumes:
        # 目录映射
            - /Users/chenglihui/docker/redis.conf:/usr/local/etc/redis/redis.conf:ro
            - /Users/chenglihui/docker/redis/data:/data
        #command:
         # 执行的命令
        entrypoint: ["redis-server", "/usr/local/etc/redis/redis.conf"]
        environment:
            - TZ=Asia/Shanghai # 设置时区
        container_name: "redis"
        networks:
            - "app-net"
            #    ipv4_address: 192.168.1.5
    php:
        image: 404422fc039e
        #restart: always
        ports:
            - "9000:9000"
        volumes:
            - /Users/chenglihui/docker/nginx/html:/var/www/html
            - /Users/chenglihui/docker/php:/usr/local/etc/
        # stdin_open: true
        # tty: true   #这两条是防止启动php失败
        # links:
        #    - "mysql"
        environment:
            - TZ=Asia/Shanghai # 设置时区
        container_name: "php"
        networks:
            - "app-net"
            #    ipv4_address: 192.168.1.3
networks:
    # 配置docker network
    app-net:
        external:
            name: "dscmall"
        #driver: bridge
        #ipam:
        #    config:
        #        # 子网络
        #        - subnet: 192.168.1.0/16 
~~~





第一步：各个容器启动拿回来配置文件

修改配置：

~~~php
server {
    listen       80;
    listen  [::]:80;
    server_name  d-dscmall.xxx.cn;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.php index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    location ~ \.php$ {
        #root           /usr/share/nginx/html;
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /var/www/html/$fastcgi_script_name;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
~~~



www.conf

~~~php
vi /Users/chenglihui/docker/php/php-fpm.d/www.conf  监听的端口
listen=0.0.0.0:9000
~~~



第二步：

~~~php
version: '3.8'
services:
    nginx:
        # 容器名称
        container_name: "nginx"
        image: f6d0b4767a6c
        #restart: always
        # 端口映射
        ports:
            - "9080:80"
        privileged: true
        environment:
            - TZ=Asia/Shanghai
        # 依赖关系 先跑php
        depends_on:
            - "php"
        # 数据卷
        volumes:
            # 映射主机./conf.d目录到容器/etc/nginx/conf.d目录
            - "/Users/chenglihui/docker/nginx/conf.d:/etc/nginx/conf.d"
            - "/Users/chenglihui/docker/nginx/html:/usr/share/nginx/html"
        networks:
            - "app-net"
            #    ipv4_address: 192.168.1.2
    mysql:
        image: a70d36bc331a
        ports:
            - "3306:3306"
        volumes:
            - /Users/chenglihui/docker/mysql/data:/var/lib/mysql:rw
            - /Users/chenglihui/docker/mysql/conf:/etc/mysql/conf.d
            - /Users/chenglihui/docker/mysql/logs:/data/mysql/logs
        #restart: always
        # 环境变量
        environment:
            - TZ=Asia/Shanghai # 设置时区
            # mysql密码
            - MYSQL_ROOT_PASSWORD=12345678
        container_name: "mysql"
        networks:
           - "app-net"
           #     ipv4_address: 192.168.1.4 
    redis:
        # 指定镜像
        image: 621ceef7494a
        # restart: always
        ports:
        # 端口映射
            - 6379:6379
        volumes:
        # 目录映射
            - /Users/chenglihui/docker/redis.conf:/usr/local/etc/redis/redis.conf:ro
            - /Users/chenglihui/docker/redis/data:/data
        #command:
         # 执行的命令
        entrypoint: ["redis-server", "/usr/local/etc/redis/redis.conf"]
        environment:
            - TZ=Asia/Shanghai # 设置时区
        container_name: "redis"
        networks:
            - "app-net"
            #    ipv4_address: 192.168.1.5
    php:
        image: 404422fc039e 
        #restart: always
        ports:
            - "9000:9000"
        volumes:
            - /Users/chenglihui/docker/nginx/html:/var/www/html
            - /Users/chenglihui/docker/php:/usr/local/etc
        # stdin_open: true
        # tty: true   #这两条是防止启动php失败
        # links:
        #    - "mysql"
        environment:
            - TZ=Asia/Shanghai # 设置时区
        container_name: "php"
        networks:
            - "app-net"
            #    ipv4_address: 192.168.1.3
networks:
    # 配置docker network
    app-net:
        external:
            name: "dscmall"
        #driver: bridge
        #ipam:
        #    config:
        #        # 子网络
        #        - subnet: 192.168.1.0/16 
~~~



需要将扩展目录也拿回本地的

~~~php
/usr/local/lib/php/extensions/no-debug-non-zts-20160303/

/usr/local/bin/phpize
~~~



~~~php
最后一个包  博客上没有写出来，在以下链接下载得到
https://blog.csdn.net/kina100/article/details/106949987

http://www.ijg.org/

http://www.ijg.org/files/jpegsrc.v9d.tar.gz


错误信息：
configure: error: ZLib not installed

https://blog.csdn.net/john_f_lau/article/details/24838171

configure和安装完 libjpeg后

下载对应的php源码包解压：

cd /var/www/test/php-7.1.33/ext/gd

第一步：
# /usr/local/bin/phpize

第二步：
# ./configure \
--with-php-config=/usr/local/bin/php-config \
--with-jpeg-dir=/usr/local/libjpeg \
--with-png-dir=/usr/local/libpng \
--with-freetype-dir=/usr/local/freetype \
--with-zlib-dir=/usr/local/zlib

这里如果报如下图错误信息：
//http://www.111com.net/sys/linux/87746.htm
# ln -s /usr/include/freetype2/freetype/ /usr/include/freetype

# apt-get install python-dev libfreetype6-dev


docker-php-ext-install mysqli pdo pdo_mysql
~~~



![image-20210320232859572](Docker.assets/image-20210320232859572.png)



这里是安装完 apt-get install python-dev libfreetype6-dev就可以编译通过。



![image-20210320234539600](Docker.assets/image-20210320234539600.png)







~~~php
// 可以看到这个压缩包也是打包后再压缩，外面是xz压缩方式，里层是tar打包方式。
$ xz -d ***.tar.xz
$ tar -xvf ***.tar
或者直接使用如下命令来解压
$ tar xvJf ***.tar.xz
~~~





~~~php
wget http://www.zlib.net/zlib-1.2.11.tar.gz
tar -zxvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure
make && make install

wget https://nchc.dl.sourceforge.net/project/libpng/libpng16/1.6.37/libpng-1.6.37.tar.xz
tar -zxvf libpng-1.6.37.tar.xz
cd libpng-1.6.37
./configure --prefix=/usr/local/libpng
make && make install


wget https://nchc.dl.sourceforge.net/project/libpng/libpng16/1.6.37/libpng-1.6.37.tar.xz
tar xvJf libpng-1.6.37.tar.xz
cd libpng-1.6.37
./configure --prefix=/usr/local/libpng
make && make install


wget http://www.ijg.org/files/jpegsrc.v9d.tar.gz
tar -zxvf jpegsrc.v9d.tar.gz
cd jpeg-9d
./configure --prefix=/usr/local/libjpeg --enable-shared
make && make install


cd php-7.1.33/ext/gd/
/usr/local/bin/phpize
./configure --with-php-config=/usr/local/bin/php-config --with-jpeg-dir=/usr/local/libjpeg --with-png-dir=/usr/local/libpng --with-freetype-dir=/usr/local/freetype --with-zlib-dir=/usr/local/zlib
make && make install
~~~



连接docker 数据库

![image-20210321085958903](Docker.assets/image-20210321085958903.png)