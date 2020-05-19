##### 第一章 Nginx简介

###### 1.1 nginx简介

```bash
1、Nginx( enginex)是一个高性能的WEB服务器和反向代理服务器，也可以作为邮件代理服务器；
2、Nginx是由俄罗斯人采用C语言开发编写的
3、Nginx特点是占有内存小，并发处理能力强，以高性能、低系统资源消耗而闻名，Nginx官方测试并发5W
4、与Nginx同类型的WEB服务器还有Apache、Lighttpd、Tengine(阿里)等
5、Nginx的并发处理能力在同类型的web服务器中表现极好，全世界范围内大量使用Nginx
6、Nginx是免费开源，同时也有收费的商业版本
```



###### 1.2 综合参数对比

<img src="H:\笔记本\Nginx.assets\image-20200505121803182.png" alt="image-20200505121803182" style="zoom:100%;float:left" />



###### 1.3 正向反向代理

<img src="H:\笔记本\Nginx.assets\image-20200505124004810.png" alt="image-20200505124004810" style="zoom:100%;float:left;" />

##### 第二章 软件安装

###### 2.1 软件下载

```bash
官方网站：http://nginx.org
去到download页面，分为主线版本、稳定版本、历史版本

# cd ~
# wget http://nginx.org/download/nginx-1.18.0.tar.gz
```

```php
#安装前准备

//ubuntu下依赖检查
# apt-get update
# apt-get install gcc
//安装nginx时依赖库zlib，openssl，pcre，openssl安装
# dpkg -l | grep zlib                   ##查看zlib是否安装
# apt-get install openssl libssl-dev    ##解决依赖包openssl安装
# apt-get install libpcre3 libpcre3-dev ##解决依赖包pcre安装
# apt-get install zlib1g-dev            ##解决依赖包zlib安装

//centos下安装依赖
# yum instal gcc openssl openss-devel pcre pcre-deve zlib zlib-devel -y
//或者分步安装(分别检查和安装)
# yum list instaled | grep gcc
# yum install gcc -y
# yum list instaled | grep openssl
# yum install openssl openssl-devel -y
# yum list installed | grep pcre
# yum install pcre pcre-devel -y
# yum list installed | grep zlib
# yum install zlib zlib-devel -y
```

```php
#解压和安装Nginx
# tar -zxvf nginx-1.18.0.tar.gz
# cd nginx-1.18.0
# ./configure --prefix=/usr/local/nginx
# make && make install

/** 安装提示：./configure: 10: .: Can't open auto/options在重新下载过新包，解压又正常可以安装了 */

# ls /usr/local/nginx/
conf  html  logs  sbin
```



###### 2.2  启停命令

启动Nginx

```php
1、普通启动命令
# /usr/local/nginx/sbin/nginx
```

```php
2、通过配置文件启动
# /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```

```php
3、检查Nginx是否启动成功
# ps -ef | grep nginx     //有Master维护进程和worker进程是对请求进行实际处理
root 71220 1 0 21:14 ? 00:00:00 nginx: master process ......
nobody 71222  71220  0 21:14 ? 00:00:00 nginx: worker process
```

关闭Nginx

```php
# ps -ef | grep nginx  #找出主进程(Master)PID

//1、优雅关闭
# kill -QUIT 主pid    

//2、快速关闭
# kill -TERM 主pid
```

重启Nginx

```
# /usr/local/nginx/sbin/nginx -s reload
```

其他命令

```php
# cd /usr/local/nginx/sbin/
# ./nginx -s stop 		//快速关闭nginx
# ./nginx -s quit 		//平稳关闭nginx
# ./nginx -s reload 	//重新加载配置而重启
# ./nginx -s reopen 	//重新打开日志文件
# ./nginx -t 			//测试配置文件
# ./nginx -v 			//显示nginx的版本
# ./nginx -V 			//显示nginx的版本及编译器版本和配置参数

//检查指定配置文件
# /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf -t
```



###### 2.3 配置文件

```php
##基础配置##
user  nobody; #配置worker进程运行用户
worker_processes  1;  #配置工作进程数目 = cpu数量或2倍CPU数量

#配置全局错误日志及类型，[debug、info、notice、warn、error、crit]
error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid		logs/nginx.pid; #配置进程的pid文件

##EVENTS配置##
events {
    worker_connections  1024;#配置每个worker进程连接数上限
    #Nginx总连接数 = worker_processes x worker_connections
}

#配置http服务器，利用它的反向代理功能提供负载均衡支持
http {
    include       mime.types;#配置nginx支持哪些多媒体类型，可以在conf/mime.typs查看支持类型
    default_type  application/octet-stream;#默认文件类型，“流类型”

    #配置日志格式
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
	#配置日志及存放路径，并使用上面定义的main日志格式
    #access_log  logs/access.log  main;

    sendfile        on;#开启高效文件传输模式，默认开启
    #tcp_nopush     on;#防止网络阻塞

    #keepalive_timeout  0;
    keepalive_timeout  65;#长连接超时时间，单位是秒

    #gzip  on;#优化参数项：开启gzip压缩输出，默认关闭；即本来要返给客户端10M数据，压缩后4M，提高了
    //传输效率，但是会消耗服务器资源

    ##配置虚拟机##
    server {
        listen       80;  #配置监听端口
        server_name  localhost;#配置服务名

        #charset koi8-r;  #俄罗斯字符集

        #access_log  logs/host.access.log  main;

        location / {
            root   html;#默认网站根目录位置
            index  index.html index.htm;#配置首页文件的名称
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html; #配置50x错误页面
        location = /50x.html { #精准匹配
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        #PHP 脚本请求全部转发到FastCGI处理
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        #禁止访问 .htaccess文件
        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    #配置另一个虚拟主机
    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

	#配置HTTPS服务
    # HTTPS server
    #
    #server {
    #    listen       443 ssl; #默认监听端口 "443"
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
}
```

##### 第三章 Nginx应用

###### 3.1 静态网站

Nginx是一个http的web服务器，可以将服务器上的静态文件(如Html、图片等)通过http协议返回给浏览器服务端；（例如：新闻类网站常常是生成静态页面）

```php
server {
    listen 80; #监听端口号
    location / {
        root /data/wwwroot/static/; #静态文件路径
        index index.html index.htm;
    }
}

#规则：ip + port 等于 root (或者自己测试一下路径)
#http://192.168.92.128:80/ = root = /data/wwwroot/static/
#http://192.168.92.128:80/ace = root = /data/wwwroot/static/ace/ace
```

###### 3.2 负载均衡

```php
#负载均衡概述
1、网建初期，单一服务器对外提供集中式服务；
2、随着业务量增大，一台服务器不够用，此时就要把多台机器组成一个集群对外提供服务；
3、网站对外只提供一个访问入口，如：www.website.com
4、用户访问 www.website.com 时，将请求分发到集群中不同的服务器机器节点上执行，实现负载均衡
```

```php
#负载均衡实现方式
//1、硬件负载均衡
比如：F5、深信服、Array等
优点：有厂商专业的技术服务团队提供支持，性能稳定
缺点：费用昂贵，对于规模较小的网络应用成本太高

//2、软件负载均衡
比如：Nginx、LVS、HAProxy等
优点：免费开源，成本低廉
```

```php
#Nginx负载均衡
Nginx如果实现负载均衡：(通过在Nginx的nginx.conf配置文件进行配置即可实现)
```

<img src="H:\笔记本\Nginx.assets\image-20200514110510134.png" alt="image-20200514110510134" style="zoom:100%;float:left;" />

```php
#具体配置如下：(2个步骤)
1、在http模块添加：
upstream www.website.com { //upstream(代理转发) 定义名字；
    server 127.0.0.1:9100 weight=3;
    server 127.0.0.1:9200 weight=1;
    #其中 weight表示权重，用于后端服务器性能不均的情况，访问比率约等于权重之比，权重越大访问机会越多
    #upstream是配置nginx与后端服务器负载均衡非常重要的一个模块，并且它还能对后端服务器的健康状态进行检查，	  #其后端服务器中的一台发生故障，则前端请求不会转发到该故障的机器
}

2、在server模块里添加
location / {
    proxy_pass http://www.website.com;  //http://规定的
	#其中www.website.com字符串需要与upstream后的字符串相等
}

#=======需要实现PHP的负载均衡操作=======
```

```php
#Nginx负载均衡策略
1、轮询(默认) 。每个请求轮流分配到不同的后端服务器，如果后端服务器down掉，将自动剔除。
upstream www.xxx.com {
    server 192.168.10.1;
    server 192.168.10.2;
}

2、权重。每个请求按一定比例分发到不同的后端服务器，weight值越大访问比例越大，用于后端服务器性能不均情况。
upstream www.xxx.com {
    server 192.168.10.1 weight=5;
    server 192.168.10.2 weight=3;
}

3、ip_hash。ip_hash叫IP绑定，每个请求按访问IP的hash值分配，这样每个访问客户端会固定访问一个后端服务器，可解决会话session丢失问题
upstream www.xxx.com {
    ip_hash;  //hash("125.207.55.82") % 2 = 0 或 1
    server 192.168.10.1;
    server 192.168.10.2;
}

4、最少连接。web请求会被转发到连接数最少的服务器上
upstream www.xxx.com {
    least_conn;
    server 192.168.10.1;
    server 192.168.10.2;
}
```

```php
#负载均衡其他配置
upstream www.xxx.com {
    server 192.168.10.1;
    server 192.168.10.2 backup;//其他所有的机器down掉，才请求backup机器；备份服务器
}

upstream www.xxx.com {
    server 192.168.10.1;
    server 192.168.10.2 dwon;//标志server机器down状态，不参与负载均衡
}
```

###### 3.3 静态代理

```
把所有静态资源的访问改为访问Nginx，而不是访问tomcat，因为Nginx更擅长于静态资源的处理，性能更好，效率更高。
实际应用中，我们将静态资源比如图片、css、html、js等交给Nginx处理
```

<img src="H:\笔记本\Nginx.assets\image-20200514152915817.png" alt="image-20200514152915817" style="zoom:100%;float:left;" />

Nginx静态代理如何实现？(通过修改Nginx的nginx.conf文件进行配置即可实现)

```php
例如：我们将静态资源放入 /data/wwwroot/www.xxx.com/static

方式一：
#当访问静态资源，则从Linux服务器 /www.xxx.com/static目录下获取
location ~ .*\.(js|css|html|htm|gif|jpg|jpeg|png|bmp|swf|ico|rar|zip|txt|flv|doc|ppt|pdf|xls|mp3|wma)$ {
    root /www.xxx.com/static;
}
/**
 * ~ 正则匹配，即说后面的内容可以是正则表达式
 * . 表示任意字符
 * * 表示一个或多个字符
 * \.转义字符
 * | 表示或者
 * $ 表示结尾
 */

方式二：
#通过

```

