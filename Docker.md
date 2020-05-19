##### 第一章 Docker容器入门

###### 1.1 Docker安装

```php
#官方链接
https://docs.docker.com

#安装Docker
https://docs.docker.com/engine/install/

//根据官网的步骤一步步安装
https://docs.docker.com/engine/install/ubuntu/

//..省略..
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



###### 1.2 基本命令

```
$ docker -v
$ docker images
```

```php
#内置镜向
https://hub.docker.com

#阿里云镜向
登录阿里云平台：dev.aliyun.com
进入控制面板 https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'  #利用tee命令把下面的配置写入daemon.json
{
  "registry-mirrors": ["https://j7qzahry.mirror.aliyuncs.com"]
}
EOF
$ sudo systemctl daemon-reload   #重载所有修改过的配置文件，扫描新的或有变动的单元
$ sudo systemctl restart docker  #重启docker
```

```php
# 下载常用镜向
$ docker pull php

$ docker run -d -p 80:80 名称或ID
    //run  把镜像放入容器中；
	//-d   后台运行，并返回ID；
	//-p   端口映射
$ docker ps          //查看正在运行的容器
$ docker ps -a       //查看所有开启过的容器
$ docker start 容器ID 
$ docker stop 容器ID
```



```php
#如果出现错误信息
IPv4 forwarding is disabled. Networking will not work.
解决办法：
$ sodu vi /etc/sysctl.conf
加入 net.ipv4.ip_forward=1
$ sudo systemctl restart network   //重启
```




https://blog.51cto.com/12123659/2301479