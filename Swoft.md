##### 第一章 项目基础搭建

###### 1.1 准备工作

```php
# cd ~
# vi .bashrc
alias php='/usr/local/php/bin/php'
alias composer='/usr/local/php/bin/php /home/chenglh/composer.phar'
# source .bashrc
# php --ri swoole    //查看swoole版本
```



###### 1.2 升级swoole拓展

```PHP
下载和编译安装
# cd /home/chenglh/
# wget https://github.com/swoole/swoole-src/archive/v4.5.1.zip
# unzip v4.5.1.zip
# cd swoole-src-v4.5.1
# /usr/local/php/bin/phpize
# ./configure --with-php-config=/usr/local/php/bin/php-config
# make && make install
# service php-fpm reload

检查版本号
方法1：# php --ri swoole
方法2：浏览器访问：http://虚拟机IP地址/phpinfo.php
```



###### 1.3 安装composer

```php
下载composer
# php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
# php composer-setup.php
# ls
-rwxr-xr-x 1 root root 1973494 May 25 16:39 composer.phar (已经有可执行条件)
-rw-r--r-- 1 root root  277133 May 25 16:39 composer-setup.php
//需要定义别名，上面已定义
```

```
配置阿里云composer镜像
https://developer.aliyun.com/composer
```



###### 1.4 添加虚拟主机

```php
# cd /home/chenglh/oneinstack
# ./vhost.sh
www.chenglh.com //配置开发域名

#完整信息
Your domain: www.chenglh.com
Virtualhost conf: /usr/local/nginx/conf/vhost/www.chenglh.com.conf
Directory of: /data/wwwroot/www.chenglh.com
```



###### 1.5下载swoft框架

```php
# cd /data/wwwroot/
# rm -rf www.chenglh.com

# composer create-project swoft/swoft www.chenglh.com
# chmod -R 777 www.chenglh.com/
# chown -R chenglh:chenglh www.chenglh.com/    //不修改用户组，phpstorm上传文件会失败
```



###### 1.6 注意事项

```php
开放端口3306、18306
# iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
# iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 18306 -j ACCEPT
# iptables-save > /etc/iptables.up.rules

启动Swoft命令：
# php /data/www.chenglh.com/bin/swoft http:start

如果端口被占用，需要kill -9 + 端口号
# netstat -tlnp | grep 18306
```



---

##### 第二章 Swoft核心

###### 2.1 目录介绍

```
├── app/
│   ├── Annotation/         ----- 定义注解相关
│   ├── Aspect/             ----- AOP切面
│   ├── Common/           	-----一些具有独立功能的class bean
│   ├── Console/            ----- 命令行代码目录
│   ├── Exception/          ----- 定义异常类目录
│   │   └── Handler/        ----- 定义异常处理类目录
│   ├── Http/               ----- HTTP 服务代码目录
│   │   ├── Controller/
│   │   └── Middleware/
│   ├── Helper/             ------助手函数
│   ├── Listener/           ------事件监听器目录
│   ├── Models/             ------模型、逻辑等代码目录
│   │   ├── Dao/		    ------数据操作层
│   │   ├── Data/           ------缓存层
│   │   ├── Logic/          ------逻辑层
│   │   └── Entity/         ------实体层
│   ├── Rpc/                ------RPC 服务代码目录
│   │   └── Service/
│   │   └── Middleware/
│   ├── WebSocket/          ----- WebSocke服务代码目录
│   │   ├── Chat/
│   │   ├── Middleware/
│   │   └── ChatModule.php
│   ├── Tcp/                ----- TCP 服务代码目录
│   │   └── Controller/     ----- TCP 服务处理控制器目录
│   ├── Application.php     ----- 应用类文件继承自swoft核心
│   ├── AutoLoader.php      ----- 项目扫描等信息(应用本身也算是一个组件)
│   └── bean.php
├── bin/
│   ├── bootstrap.php
│   └── swoft                ----- Swoft 入口文件
├── config/                  ----- 应用配置目录
|   ├── app.php
│   ├── base.php             ----- 基础配置
│   └── db.php               ----- 数据库配置
├── public/                  ----- 公共目录
├── resource/                ----- 应用资源目录
│   ├── language/            ----- 语言资源目录  
│   └── view/                ----- 视图资源目录  
├── runtime/                 ----- 临时文件目录（日志、上传文件、文件缓存等）
├── test/                    ----- 单元测试目录
│   └── bootstrap.php
├── composer.json
├── phar.build.inc
└── phpunit.xml.dist
```



###### 2.2 Swoft Bean容器

IoC 即控制反转(Inversion of Control)

DI   即依赖注入(Dependency Injection)

Swoft的核心内容就是Bean容器，每一个Bean就是一个类的对象实例，容器是一个巨大工厂存放和管理Bean。在HttpServer启动的时候会去扫描带有@Bean注解的类。

> 为什么使用Bean容器？

​       传统的PHP框架没有常驻内存，因此每次请求进来都需要把所有用到的类实例化一次，每次实例化对象都需要申请内存，当请求处理完成之后又需要释放，这样不断申请和释放是非常浪费资源的。

​       而使用Swoft之后只有在HttpServer启动的时候就把这些类实例化预先放在内存里，并不需要每次请求都实例化对象，减少创建对象的时间。

Swoft的Bean容器池(Mysql类、Route类、Cache类等)，消费者直接去容器里取出来使用，如下图所示：



<img src="H:\笔记本\Swoft.assets\image-20200601102148814.png" alt="image-20200601102148814" style="zoom:90%;float:left" />


Swoft底层是一个BeanFactory管理着Container。

> 具体路径 vendor/swoft/Bean/src/Container/get()方法



自定义容器，创建目录 app/Bean

```php
# mkdir /app/Bean
# vi /app/Bean/Chenglh.php

<?php
namespace App\Bean;

use Swoft\Bean\Annotation\Mapping\Bean;
/**
 * @Bean(name="chenglh")
 */
class Chenglh {
    private $name;
    private $age;

    public function __construct() {
        echo "开启容器：chenglh",PHP_EOL;//测试使用
    }

    public function setName(string $name) {
        $this->name = $name;
        return $this;
    }

    public function setAge(int $age) {
        $this->age = $age;
        return $this;
    }

    public function getName() : string {
        return $this->name .' time:' . time();//测试观察，带上时间戳
    }

    public function getAge() : string {
        return $this->age .' time:' . time();
    }
}

/**
 * 在启动Swoft时 # php ./bin/swoft http:start
 * 程序扫描注解的时候，会在控制台中打印信息 开启容器：chenglh
 */
```



(一)、通过@Bean注解声明

1、name：Bean容器的名字，如果不写默认为带命名空间的类名，如 App/Bean/Chenglh::class

2、scope：注入Bean的类型是否每次都创建还是使用单例。

- 单例：Swoft\Bean\Annotation\Scope::SINGLETON
- 每次都创建：Swoft\Bean\Annotation\Scope::PROTOTYPE



(二)、通过方法获取

- \Swoft::getBean("name")
- BeanFactory::getBean("name")

```php
#创建IndexController控制器
# vi app/Http/Controller/IndexController.php
<?php declare(strict_types=1);

namespace App\Http\Controller;

use App\Bean\Chenglh;
use Swoft\Bean\BeanFactory;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;

/**
 * @Controller()
 */
class IndexController {
    /**
     * @RequestMapping(route="/v1/index")
     */
    public function index() {
        /** @var $chenglh \App\Bean\Chenglh */
        //$chenglh = \Swoft::getBean(Chenglh::class);
        $chenglh = BeanFactory::getBean("chenglh");

        $chenglh->setName('cheng li hui');
        $chenglh->setAge(25);

        return [
            'name'=>$chenglh->getName(),
            'age'=>$chenglh->getAge()
        ];
    }
}
```

(三)、通过注入

- @Inject

```php
<?php declare(strict_types=1);

namespace App\Http\Controller;

use Swoft\Bean\Annotation\Mapping\Inject;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;

/**
 * @Controller()
 */
class IndexController {
    /**
     * @Inject(name="chenglh")
     * @var $chenglh \App\Bean\Chenglh
     */
    private $chenglh;
    
    /**
     * @RequestMapping(route="/v1/index")
     */
    public function index() {
        $this->chenglh->setName('cheng li hui');
        $this->chenglh->setAge(25);
        return [
            'name'=>$this->chenglh->getName(),
            'age'=>$this->chenglh->getAge()
        ];
    }
}
```



(四)、验证是否是单例模式

```php
<?php declare(strict_types=1);

namespace App\Http\Controller;

use App\Bean\Chenglh;
use Swoft\Bean\BeanFactory;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;

/**
 * @Controller()
 */
class IndexController {
    /**
     * @RequestMapping(route="/v1/index")
     */
    public function index() {
//      $chenglh = \Swoft::getBean('chenglh');
//      $chenglh1 = BeanFactory::getBean('chenglh');
//      var_dump($chenglh===$chenglh1);  // true

        $chenglh = \Swoft::getBean('chenglh');
        $chenglh1 = new Chenglh();
        var_dump($chenglh===$chenglh1); // false
    }
}
```



###### 2.3 Swoft注解使用

>  什么是注解？

​       注解其实通过反射把注释当作代码的一部分。PHP可以通过`ReflectionClass`获取一个类的信息，从而通过类里的信息实现一些操作。例如：IOC反转控制就是通过反射实现的，还有依赖注入等。

```
#PHP ReflectionClass类文档
https://www.php.net/manual/zh/class.reflectionclass.php
```



在Swoft里面的容器、路由、定时器、任务处理、进程、控制器等大量使用了注解方便我们操作。
Swoft有很多注解，具体的使用可以看类里的实现。

> 具体代码路径在每个组件的：Bean/src/Annotation下面
>
> 如：Process的注解代码就在vendor/swoft/process/src/Annotation



**PHPstorm IDE下载`PHP Annotations`注解插件**

File  >>  Setting >> Plugins  搜索Annotations

安装步骤：下载  >> 安装/更新 >> 重启IDE


我们实现一下通过这个注解类库来实现一下基础功能。

> https://www.bilibili.com/video/BV12J411j721?p=7



###### 2.4 Swoft注入

使用注入可以让我们直接注入Bean里面的实例，也可以在方法注入http请求参数

(一)、在控制器方法使用注入

```php
//这里的ID使用注入从http请求获取参数
/**
 * @RequestMapping(route="/index/{id}")
 * @param int $id
 * @return string
 */
function userInfo(int $id) {
    return "GET id :" . $id;
}
```

(二)、Inject()

```php
class User{
    /**
     * @Inject()
     * @var \App\Models\Logic\UserLogic
     */
    private $userLogic;
    
    public function getName() {
        return $this->userLogic->getName();
    }
}
```



###### 2.5 Swoft事件

>  1、Swoole中的事件(已封装好，慎重使用，防止事件被覆盖，程序崩溃)
>
>  2、Swoft中的事件
>
>  3、用户自定义事件



用户自定义监听事件

```php
# mkdir app/Listener
# vi OrderListener.php


```





###### 2.6 Swoft命令行



###### 2.7 Swoft开发者工具























###### 配置数据库

```php
配置文件：app/bean.php
'db' => [
    'class'    => Database::class,
    'dsn'      => 'mysql:dbname=tswoft;host=127.0.0.1',
    'username' => 'root',
    'password' => '123456',
    'charset'  => 'utf8mb4',
],
'db.pool' => [
    'class'    => Pool::class,
    'database'  => bean('db'), //连接池要与上面的key对应
],
```

