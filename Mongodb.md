##### 第一章 入门基础

```
企业级Mongodb数据库全实战视频教程
https://www.bilibili.com/video/BV1aE411i7Qm?from=search&seid=14363050426909956376

实战环境
centos7、64位
iptables和selinux关闭
```

###### 1.1 基础简介

> mongodb是一个非关系型数据库，但操作与关系型数据库类似
> mongodb是面向文档存储的非关系型数据库，数据以json格式存储
> mongodb可用来永久存储，也可用来缓存数据
> mongodb提供副本集和分片集群功能，操作简单



###### 1.2 软件安装

mongodb官网：https://www.mongodb.com

直接使用二进制包，免编译安装

菜单栏 > Software > Community Server  【社区版本】

<img src="H:\笔记本\Mongodb.assets\image-20200526170253002.png" alt="image-20200526170253002" style="float:left;" />

```php
下载二进制包
# wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu1804-4.2.6.tgz
# tar -zxvf mongodb-linux-x86_64-ubuntu1804-4.2.6.tgz
# mv mongodb-linux-x86_64-ubuntu1804-4.2.6  /usr/local/mongodb

查看安装信息
# /usr/local/mongodb/bin/mongod --help     //帮助命令
# /usr/local/mongodb/bin/mongod --version  //版本信息
db version v4.2.6
......
```



```

##配置目录及文件
# mkdir -pv /data/mongodb/27017/        //创建文件夹,-p递归；-v显示创建结果
mkdir: created directory '/data'
mkdir: created directory '/data/mongodb'
mkdir: created directory '/data/mongodb/27017/'

##mongodb单例配置文件
# vi /data/mongodb/27017/mongodb.conf
systemLog:
 destination: file
 logAppend: true
 path: "/data/mongodb/27017/mongodb.log"
storage:
 dbPath: "/data/mongodb/27017/"
 journal:
  enabled: true
processManagement:
 fork: true
net:
 port: 27017
 bindIp: 127.0.0.1

/** 注意层次，不能使用tab键,key: val有个空格键 */
/** # /usr/local/mongodb/bin/mongod --help 可查看参数类型 */
```



```php
#启动mongodb
# /usr/local/mongodb/bin/mongod -f /data/mongodb/27017/mongodb.conf
about to fork child process, waiting until server is ready for connections.
forked process: 108417
child process started successfully, parent exiting

//查看是否启动成功
# ls -l /data/mongodb/27017/
会多了一些文件
# ps -ef | grep mongodb
# netstat -tulnp | grep mongod    //查看监听端口
tcp 0 0 127.0.0.1:27017 0.0.0.0:* LISTEN 108417/mongod

#关闭
# kill -9 PID  //暴力关闭，可能数据丢失

```



























































课程地址：https://www.bilibili.com/video/BV1aE411i7Qm?from=search&seid=10915866929929003702