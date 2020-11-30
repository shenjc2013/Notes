##### 第一章 Socket愉快学习篇

###### 第1讲-开启Socket学习之旅

> IP地址 + 协议(TCP/UDP) + 端口 = 套接字
> 套接字就能区分：谁谁谁
>
> 打电话例子：
> 1、打电话(客户端)，接电话(服务端)
> 2、接电话(服务端)可以同时接好几个人电话
> 3、服务端一直是开机“等待状态”(socket等待客户端接入)
> 4、通过区号+电话号码(即套接字)，中转机才能区分出你和你要和哪个人通话
> 5、协议要相同，(即通话语言)



**官方文档 (函数库)**

```php
https://www.php.net/manual/zh/ref.sockets.php
```

<img src=".\Websocket.assets\image-20200515142636903.png" alt="image-20200515142636903" style="float:left;" />

```php
//1、创建并返回一个套接字，也称作一个通讯节点
/**
 * $domain 通讯协议；AF_INET IPv4 网络协议；AF_INET6 IPv6 网络协议；AF_UNIX 本地通讯协议(进程间)
 * $type 套接字使用的类型; 常用 SOCK_STREAM(TCP)、SOCK_DGRAM(UDP)、SOCK_SEQPACKET(全双工)等
 * $protocol 套接字下的具体协议；SOL_TCP和SOL_UDP
 */
socket_create ( int $domain , int $type , int $protocol ) : resource
```

```php
<?php
//服务端
$socket = socket_create(AF_INET, SOCK_STREAM,SOL_TCP);//购买电话机

socket_bind($socket, '127.0.0.1', 9100);//绑定电话号码

socket_listen($socket, 5);//开机，第二个参数：最多传入5个连接将排队等待处理

while(true) {
    $client = socket_accept($socket);//有人打电话进来
    $buffer = socket_read($client, 1024);//读取电话内容，1024个字符
    socket_write($client, 'Hello socket');//向对方传递内容
    socket_close($client);//对话完挂机，关闭客户端通话
}

socket_close($socket);//关机

#PHP的控制台要运行当前PHP，然后浏览器打开
```



