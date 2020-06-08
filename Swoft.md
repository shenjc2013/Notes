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

安装了注解插件可提供注解命名空间自动补全，注解属性代码提醒，注解类跳转等非常有助于提升开发效率的功能



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

(二)、Inject()注入

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
# vi OrderListener.php  //如：下单加减库存

<?php declare(strict_types=1);
namespace App\Listener; //第①步，命名空间

use Swoft\Event\Annotation\Mapping\Listener;
use Swoft\Event\EventHandlerInterface;
use Swoft\Event\EventInterface;
use Swoft\Task\Task;

/**
 * Class OrderListener
 * @Listener("order.*")
 * 第③步打上 Listener注解，并命名事件
 */
class OrderListener implements EventHandlerInterface  // 第②步，定义类 并且 继承 EventHandlerInterface; 再 Alt + 回车键 引入handle接口方法
{
    /**
     * @param EventInterface $event
     */
    public function handle(EventInterface $event): void
    {
        // TODO: Implement handle() method.
		//第⑤步监听事件，打印参数
        print_r($event);
        //$event->getName()事件名字
		//$event->getTarget()事件标识(来源) --常用"控制器.方法"来命名来追寻来源
        //$event->getParams()获取所有参数

        //$event->getParam(0) //索引数组传递
        //$event->getParam('name') //关联数组传递
    }
    /** 第五步可以不实现代码，可由第六步，事件消费实现具体业务 */
}
```

手动触发事件

```php
<?php declare(strict_types=1);

namespace App\Http\Controller;

use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;

/**
 * @Controller()
 */
class IndexController {
    /**
     * @RequestMapping(route="/index")
     */
    public function index() {
        //第④步，手动触发事件
        \Swoft::trigger('order.setInc',null,$goods_id=1254,$number=8);//索引传不定参
        \Swoft::triggerByArray('order.setDec',null,['name'=>'chenglh','age'=>19]);
        //数组传参
    }
}
```

事件分组：

OrderListener的事件处理里面可以通过$event->getName获取到具体名字，@Listener("order.*")使用 * 来监听所有 order前缀开头的事件。

> 事件名称建议放置在一个专用类的常量中，方便进行管理和维护。

```php
# mkdir app/Constant
# vi app/Constant/EventConstants.php

<?php
namespace App\Constant;

/**
 * 自定义事件名称类
 * @package App\Constant
 */
class EventConstants
{
    /** 订单事件 */
    const ORDER_SETINC = 'order.setInc';
    const ORDER_SETDEC = 'order.setDec';
    
    /** 用户事件 */
    ......
}
```

```php
# 手动触发事件
<?php declare(strict_types=1);

namespace App\Http\Controller;

use App\Constant\EventConstants;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;

/**
 * @Controller()
 */
class IndexController {
    /**
     * @RequestMapping(route="/index")
     */
    public function index() {
        //手动触发事件
        \Swoft::triggerByArray(EventConstants::ORDER_SETINC,null,['name'=>'chenglh','age'=>19]);
        \Swoft::triggerByArray(EventConstants::ORDER_SETDEC,null,['name'=>'chenglh','age'=>20]);
    }
}
```

```php
<?php declare(strict_types=1);
namespace App\Listener;

use Swoft\Event\Annotation\Mapping\Listener;
use Swoft\Event\EventHandlerInterface;
use Swoft\Event\EventInterface;

/**
 * Class OrderListener
 * @Listener("order.*")
 */
class OrderListener implements EventHandlerInterface
{
    /**
     * @param EventInterface $event
     */
    public function handle(EventInterface $event): void
    {
        // TODO: Implement handle() method.
    }
}
```

事件消费者

```php
<?php
namespace App\Event;// 第一步 命名空间

use App\Constant\EventConstants;
use Swoft\Event\Annotation\Mapping\Subscriber;
use Swoft\Event\EventInterface;
use Swoft\Event\EventSubscriberInterface;
use Swoft\Event\Listener\ListenerPriority;

/**
 * Class OrderSubscriber
 * @Subscriber()  第三步  打上Subscriber注解
 */
//第二步  定义类，并继承 EventSubscriberInterface
class OrderSubscriber implements EventSubscriberInterface
{
    //#public const EVENT_INC = 'order.setInc';
    //#public const EVENT_DEC = 'order.setDec';

    /**
     * @return array
     * [
     *  'event name' => 'handler method'
     *  'event name' => ['handler method', ListenerPriority::HIGH]
     * ]
     */
    public static function getSubscribedEvents(): array
    {
        /*return [
            self::EVENT_INC => 'stockSetInc',
            self::EVENT_DEC => 'stockSetDec',
        ];*/
        return [
            EventConstants::ORDER_SETINC => 'stockSetInc',
            EventConstants::ORDER_SETDEC => 'stockSetDec',
        ];
    }

    public function stockSetInc(EventInterface $evt): void
    {
        print_r($evt);
        echo "库存自增",$evt->getParam('age'),PHP_EOL;
    }

    public function stockSetDec(EventInterface $evt): void
    {
        print_r($evt);
        echo "库存自减:",$evt->getParam('age'),PHP_EOL;
    }
}
//具体业务可以使用 “注入” 对应模块来实现
```



###### 2.6 Swoft命令行

```php
#命令行模式
# php ./bin/swoft -h
Available Commands:
  agent      
  app        
  dclient    
  demo       
  dinfo      
  dtool      
  entity     //实体类
  http       //http Server启动、重启、关闭
  migrate    
  process    
  rpc        
  tcp        
  test       
  ws     
# php ./bin/swoft  http    //http相关命令
reload
restart
start
stop 
```

```php
#下一级命令帮忙信息
# php ./bin/swoft app
  bean        //Bean容器里的实例
  components  
  http-routes //http路由
  init        
  tcp-routes  
  ws-routes
# php ./bin/swoft app:http-routes
```



###### 2.7 Swoft开发者工具

开发者工具

**Swoft CLI**是一个独立的命令行应用，提供了一些内置的功能方便开发者使用：

- 生成 Swoft 应用类文件，例如：HTTP 控制器，WebSocket 模块类等
- 监视用户Swoft 项目的文件更改并自动重新启动服务器
- 快速创建新应用或组件
- 将一个 Swoft 应用打包成 **Phar** 包



下载 **swoftcli.phar**包

> \# cd  /data/wwwroot/www.chenglh.com/
>
> \# wget https://github.com/swoft-cloud/swoft-cli/releases/download/{VERSION}/swoftcli.phar

* 注意：｛VERSION｝ 替换成最新版本，版本号在如下链接找

  https://github.com/swoft-cloud/swoft-cli/releases

```php
##检查包是否可用，打印版本信息
# php swoftcli.phar -V
PHP: 7.4.0, Swoft: 2.0.9, Swoole: 4.5.1

##显示帮助信息
#php swoftcli.phar -h
  client         
  gen            
  new            
  phar           
  self-update    
  serve          
  system         
```

**常用命令**

```php
# php swoftcli.phar gen
  cli-command     
  crontab         		//计划任务
  http-controller 		//创建控制器(alias: ctrl, http-ctrl)
  http-middleware 		//创建中间件(alias: http-mdl, httpmdl, http-middle)
  listener        		//监听器
  process         		//自定义进程
  rpc-controller  
  rpc-middleware  
  task            		//任务投递
  tcp-controller  
  tcp-middleware  
  ws-controller   
  ws-middleware   
  ws-module       
```



> 1、命令行创建控制器

```php
# php swoftcli.phar gen:http-ctrl users @app/Http/Controller/Manager -n App\\Http\\Controller\\Manager --prefix /users
```



> 2、生成中间件

```php
# php swoftcli.phar gen:http-mdl
Please input class name(no suffix and ext. eg. test): Api
Target File: app/Http/Middleware/ApiMiddleware.php
```



> 3、生成实体类

```php
# php ./bin/swoft entity:create -d user --remove_prefix=hx_
```



##### 第三章 Swoft基本使用

###### 3.1 控制器

Swoft适用于微服务，但是也是一个MVC框架，也能用来开发web和api接口。

```php
##创建控制器
# php swoftcli.phar gen:http-ctrl users @app/Http/Controller/Manager -n App\\Http\\Controller\\Manager --prefix /users
```

> **控制器注解**

* @Controller：声明控制器

     	prefix：路由前缀

* @RequestMapping：路由

    	 route：路由地址(如果 "/" 开头即是重写路由)

   	  method：接受的请求方式(method={RequestMethod::GET，RequestMethod::POST})

* @View：模板

  ​	   template：模版静态文件

    	 layout：模版布局

* @Middleware：单个中间件

* @Middlewares：多个中间件

* @Inject：注入
        name：bean容器名称

>  注意事项：
>
>  不要在控制器基类来写公共的变量，这样易造成数据污染，第二个人进来依然会请求到这个变量。
>
>  因为常驻内存并且为单例，所以不会被释放掉，容易造成内存泄漏，程序会跑着跑着就“崩溃”了

```php
##错误写法##
/**
 * @Controller()
 */
class BaseController {
    protected $num;
    protected $arr;
}

/**
 * @Controller(prefix="/v1/index")
 */
class IndexController extends BaseController {
    /**
     * @RequestMapping(route="index")
     */
    public function index(Request $request) {
        //$this->num++;
        //echo $this->num."\n"; 并发进来数字会一直累加
        $this->num++;
        $this->arr[$this->num] = $request;
        return $this->arr; //内存泄漏
    }
}
```



###### 3.2 Http对象

**请求 Request 与响应 Response **

请求与响应对象存在于每次 HTTP请求

**Request    ：Http请求对象，Swoft\Http\Message\Request;
Response ： Http响应对象，Swoft\Http\Message\Response;**



> 获取请求对象
>
> 方法一：通过控制器方法注入，public function action(Request $request)
>
> 方法二：通过上下文环境获取，Context::get()->getRequest()



**调用 Request 对象方法**

- query		获取get参数
- post          获取post参数
- input         获取get、post参数(通用方法)
- <u>无需关心 json、xml请求，会自动解析为php的数组，通过以上三种方式获取</u>
- raw	       获取raw数据(postman的数据格式)   $data = $request->raw();
- server       获取server数据
- getUploadedFiles      获取上传文件



表单提交的格式：**form-data、x-www-form-urlencoded、raw、binary类型**

| 格式                  | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| form-data             | 以标签为单元，用分隔符分开；如key:value格式，属性multipart/form-data可以上传文件 |
| x-www-form-urlencoded | 将表单内的数据转换为键值对,如：name=leyangjun&age =28        |
| raw                   | 可上传任意格式的文本，可以上传text、json、xml、html等各种文本类型 |
| binary                | 等同于Content-Type:application/octet-stream，只可上传二进制数据 |

**接受http请求对象**

打印请求头信息

```php
public function get2(Request $request) {
    $headers = $request->getHeaders();
    foreach ($headers as $name => $values) {
       echo $name . ": " . implode(", ", $values).PHP_EOL;
    }
    echo $request->header('Host');
}
```

打印 SERVER 数据

```php
$data = $request->getServerParams();
/** 常用的
 * [request_method] => GET
 * [request_uri] => /users/get2
 * [path_info] => /users/get2
 * [remote_addr] => 192.168.211.1
 */
$remote_addr = $request->server('remote_addr', '');//客户端IP地址
```

获取 GET/POST 数据

```php
<?php declare(strict_types=1);

namespace App\Http\Controller\Manager;

use Swoft\Http\Message\Request;
use Swoft\Http\Message\Response;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;
use Swoft\Http\Server\Annotation\Mapping\RequestMethod;
use Swoft\Context\Context;

/**
 * Class UsersController
 * @Controller(prefix="/users")
 */
class UsersController {
    /**
     * @RequestMapping(route="get",method={RequestMethod::GET})
     * 访问方式：http://www.chenglh.com:18306/users/get?name=chenglh&age=19
     */
    public function get(Request $request) {
        //$getData = $request->get();
        //$getName = $request->get('name', 'default');

        $getData = $request->query();
        $getName = $request->query('name', '');
        return ['getData'=>$getData, 'getName'=>$getName];
    }

    /**
     * @RequestMapping(route="get1",method={RequestMethod::GET})
     * 访问方式：http://www.chenglh.com:18306/users/get1?name=chenglh&age=20
     * 使用命名空间：Swoft\Context\Context
     */
    public function get1() {
        $request = Context::get()->getRequest();
        $getData = $request->get();
        $getName = $request->get('name', 'default');
        return ['getData'=>$getData, 'getName'=>$getName];
    }

    /**
     * @RequestMapping(route="post",method={RequestMethod::POST})
     * 访问方式：http://www.chenglh.com:18306/users/post
     */
    public function post(Request $request) {
        $getData = $request->post();
        $getName = $request->post('name', '');
        return ['getData'=>$getData, 'getName'=>$getName];
    }
}
```

获取上传文件

```php
$file = $request->getUploadedFiles();
```

```php
##辅助方法
$request->isAjax()
$request->isGet()
$request->isPost()
$request->isPut()
......
```



**调用 Response 对象方法**

> 获取响应对象
>
> 方法一：通过控制器方法参数注入 (Response $response)
>
> 方法二：通过请求上下文获取 context()->getResponse()

```php
#设置响应状态
return $response->withStatus(404);

#输出字符串
return $response->withContent("Hello Swoft2.0");

#输出数组
return $response->withData(['name'=>'Swoft2.0']);

#重定向
return $response->redirect("http://www.swoft.org");

#设置响应头信息
$response->withHeader('Access-Control-Allow-Origin', 'http://mysite')
```




###### 3.3 中间件

在请求到达控制器之前对参数进行过滤、权限校验、数据初始等操作；在数据返回给用户之前，可以进行数据更改、日志记录等操作。

<img src="H:\笔记本\Swoft.assets\image-20200603161456230.png" alt="image-20200603161456230" style="float:left;" />



> 命令行生成中间件

```php
# php swoftcli.phar gen:http-mdl
Please input class name(no suffix and ext. eg. test): Api
Target File: app/Http/Middleware/ApiMiddleware.php
```



> 设置全局中间件，在 app/bean.php 

```php
'httpDispatcher'    => [
     // Add global http middleware
     'middlewares'      => [
         //\App\Http\Middleware\FavIconMiddleware::class,
         \App\Http\Middleware\ApiMiddleware::class, //全局中间件
         ......
```

**注意：设置全局中间件，不需要在控制器中单独引入。**



> 中间件注解使用

* @Middleware：单个中间件(如果写多个@Middleware会覆盖上面的，只有一个生效)
* @Middlewares：使用多个中间件



@Middleware 对应的注解类：Swoft\Http\Server\Annotation\Mapping\Middleware

@Middlewares 对应的注解类：Swoft\Http\Server\Annotation\Mapping\Middlewares



创建用户认证中间件，先移出bean.php中的全局中间件：ApiMiddleware

```php
# php swoftcli.phar gen:http-mdl
Please input class name(no suffix and ext. eg. test): Auth
Target File: app/Http/Middleware/AuthMiddleware.php
```



中间件使用

```php
##单个中间件：
@Middleware(ApiMiddleware::class)

##多个中间件：
@Middlewares({
	@Middleware(ApiMiddleware::class),
	@Middleware(AuthMiddleware::class)
})

##控制器代码
/**
 * @RequestMapping(route="get2",method={RequestMethod::POST})
 * @Middlewares({
 *     @Middleware(ApiMiddleware::class),
 *     @Middleware(AuthMiddleware::class)
 * })
 */
 public function get2(Request $request) {
    echo "控制器controller",PHP_EOL;
    return "test";
 }
```



中间件流程打印结果

> 全局中间件before request handle
> Auth中间件before request handle
> 控制器controller
> Auth中间件after request handle
> 全局中间件before request handle



中间件解决跨域设置

~~~php
public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        if ('OPTIONS' === $request->getMethod()) {
            $response = Context::mustGet()->getResponse();
            return $this->configResponse($response);
        }
        $response = $handler->handle($request);
        return $this->configResponse($response);
    }

    private function configResponse(ResponseInterface $response)
    {
        return $response
            ->withHeader('Access-Control-Allow-Origin', 'http://mysite')
            ->withHeader('Access-Control-Allow-Headers', 'X-Requested-With, Content-Type, Accept, Origin, Authorization')
            ->withHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, PATCH, OPTIONS');
    }
~~~



###### 3.4 集成jwt

> 什么是jwt

传统的session、cookie模式需要服务端保存session文件，如果多机器部署，那么session共享就会成为一个问题，我们可以使用redis来实现分布式的session共享。

`jwt`全称 Json Web Token现在比较流行的一种身份验证方式，服务器不需要保存任何文件，所有的信息都存在一个字符串中，请求接口的时候在header携带上这个token，接口去解析这个token获取到响应的数据。



> JWT由三个部分组成

1. JWT头
2. 有效载荷
3. 签名



参考文章

```
https://blog.csdn.net/cjs5202001/article/details/80228937
```



> 第一步：安装JWT组件

在composer官网上搜索查找  firebase/php-jwt，可以去到 github 仓库

```php
# composer require firebase/php-jwt
```



> 第二步：新建 jwt 配置文件

~~~php
# vi ./config/jwt.php
<?php

return [
    //*****自己生成openssl*****
    'privateKey' => '-----BEGIN RSA PRIVATE KEY-----
MIICXQIBAAKBgQDa+rOwz+O7MjpLcsa6bMN58FG3I7LnhYQ5HvqPDUiB+j+cNQFf
m+wVMZUi20EiRhKsMjM/8gx+0wAsGK6bcoOfbEiXjp413PyCMMnaprt9I4gNmeAQ
cB1hSfEfbcDCbVG2IMOMGnvRx36naydDWdLXHse6yJK9a0RRxpS3aNCRyQIDAQAB
AoGBAJGQZ9SYTS0KFYBD+uDAHi032Eoim/GVarDB7BMd5F4qqRBAl/ojXwszm4zB
LQoIhK8c676NO0svHgUyHxfMRrt/KzFL9vzN7AWMpGh3x4U10HTfT4uu9oyRdJvh
3yr4iWhDoQk7V0VMVjBFQjoCgzZVA1cKmqLKXPQyI0t6thQBAkEA9lo9P16dYaBe
FWAro8PAgQvuSCFUVMY+mHGSvr358a9ie+cbh9fCd4Gge+6+JHcgQaKTcFaCTxIK
mccdFgTFSQJBAOOOCbEmwgoYgJiE3dyvEKPWnmyvcdtntZmpu7zv9lxbuvXhpZ2h
5kNZ/fn0LXunpIFKAmadzd1B0Uda6vOn6IECQEUqUMfZ6JXgUInv1lDEROf2UZAu
y16BylFCkdC7xdD1TNE8sZ4SFac33bbt8LSMPaIv4vVHVI6eohtKq//ilwECQQCL
D1gY7GiUJtkfW8MBg/KVTSjPnn/j5wLxfup90d8qHdypOlYteKzw5+PvhirtcEt1
vzasYy9VUU2FX6hJcokBAkAaIW9MaZnud3fQ+tsOGW6bUZeyJTDWXkCi9gpPHG+n
Whqlzml7k03kt5zzSsPwRuba4iiAewyieIHPW17DjJPa
-----END RSA PRIVATE KEY-----',
    'publicKey'  => '-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDa+rOwz+O7MjpLcsa6bMN58FG3
I7LnhYQ5HvqPDUiB+j+cNQFfm+wVMZUi20EiRhKsMjM/8gx+0wAsGK6bcoOfbEiX
jp413PyCMMnaprt9I4gNmeAQcB1hSfEfbcDCbVG2IMOMGnvRx36naydDWdLXHse6
yJK9a0RRxpS3aNCRyQIDAQAB
-----END PUBLIC KEY-----',
    'type' => 'RS256'
];
~~~



> 第三步：创建认证控制器，签发Token

```php
# php swoftcli.phar gen:http-ctrl account @app/Http/Controller/Manager -n App\\Http\\Controller\\Manager --prefix /account

##验证登录，生成token
# vi ./app/Http/Controller/Manager/AccountCtroller.php

<?php declare(strict_types=1);
namespace App\Http\Controller\Manager;

use Firebase\JWT\JWT;
use Swoft\Http\Message\ContentType;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;
use Swoft\Http\Server\Annotation\Mapping\RequestMethod;
use Swoft\Http\Message\Request;
use Swoft\Http\Message\Response;

/**
 * Class AccountController
 * @Controller(prefix="/account")
 */
class AccountController
{
    /**
     * 用户登录
     * @RequestMapping(route="login",method={RequestMethod::POST})
     */
    public function login(Request $request, Response $response) : Response {
        $payload = array(
            "iss" => "http://www.example.com",//签发者可选
            "aud" => "http://www.example.com",//接收方可选
            "iat" => time(),//签发时间
            //"nbf" => time(),//某个时间点后才能访问 如time() + 10 ，10秒后才生效
            //"exp" => time() + 7200,//过期时间，设置2个小时
            "user" => [
                "user_id" => 10001,
                "user_name" => 'chenglh',
                "age" => 18,
            ],
        );

       $token = JWT::encode($payload, \config('jwt.privateKey'), \config('jwt.type'));
        $result = [
            'code' => 200,
            'message' => '请求成功',
            'data' => [
                'token'=>$token
            ]
        ];

        return $response->withContentType(ContentType::JSON)->withData($result);
    }
}
```



> 第四步：写入header头信息

通过方法登录接口：http://www.chenglh.com:18306/account/login
获取 token值

~~~json
{
    "code": 200,
    "message": "请求成功",
    "data": {
        "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC93d3cuZXhhbXBsZS5jb20iLCJhdWQiOiJodHRwOlwvXC93d3cuZXhhbXBsZS5jb20iLCJpYXQiOjE1OTEyNTU1NjMsInVzZXIiOnsidXNlcl9pZCI6MTAwMDEsInVzZXJfbmFtZSI6ImNoZW5nbGgiLCJhZ2UiOjE4fX0.GlbspQ6RibWP4Mdt8Dd_-lS3BeW72utIYh-7ZhJTEpsZiZlZXRUKIMz2mcgck4viHTXLxRFlhHy4MQ1kBTzl3cUEKdnPldX2T9_8zd-2I3IJ3aOvK_6qiGzfPhgCTEQMJyKcWXNawdF_OWaxq7yHHS_bf9xtZ12PB9HjCuzkuJE"
    }
}
~~~

将 token值写入头信息，如下图所示：

![image-20200604152945508](H:\笔记本\Swoft.assets\image-20200604152945508.png)



> 第五步：Token解密

1、获取 token ，    $token = $request->getHeaderLine('token');
2、解密数据，        $auth = JWT::decode($token, \config('jwt.publicKey'), [\config('jwt.type')]);
3、把用户信息设置到httpRequest对象中，$request->user = $auth->user;

```php
# vi ./app/Http/Middleware/AuthMiddleware.php

<?php declare(strict_types=1);
namespace App\Http\Middleware;

use Firebase\JWT\JWT;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\RequestHandlerInterface;
use Swoft\Bean\Annotation\Mapping\Bean;
use Swoft\Context\Context;
use Swoft\Exception\SwoftException;
use Swoft\Http\Message\ContentType;
use Swoft\Http\Message\Request;
use Swoft\Http\Server\Contract\MiddlewareInterface;
use function context;

/**
 * Class AuthMiddleware - Custom middleware
 * @Bean()
 * @package App\Http\Middleware
 */
class AuthMiddleware implements MiddlewareInterface
{
   /**
     * Process an incoming server request.
     * @param ServerRequestInterface|Request  $request
     * @param RequestHandlerInterface $handler
     * @return ResponseInterface
     * @throws SwoftException
     * @inheritdoc
     */
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        // before request handle
        $token = $request->getHeaderLine('token');
        try {
            $auth = JWT::decode($token, \config('jwt.publicKey'), [\config('jwt.type')]);
            $request->user = $auth->user; //把用户信息注入到全局变量中
        } catch (\Exception $e) {
            $result = ['code'=>101,'msg'=>'授权失败','data'=>null];
            return \context()->getResponse()->withContentType(ContentType::JSON)->withData($result);
			/**
             * /** @var $response \Swoft\Http\Message\Response */
			 * $response = Context::get()->getResponse();
			 * $response->withContentType(ContentType::JSON)->withData($result);
			 */
        }

        return $handler->handle($request);
        // after request handle
    }
}
```



###### 3.5 生成证书

> 手动生成openssl 证书

```php
##检查是否安装openssl
# openssl version -a
```

需要安装openssl(系统一般内置了)

**生成公钥**

~~~php
# openssl genrsa -out rsa_private_key.pem 1024
~~~

**生成私钥**

~~~php
# openssl rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
~~~

**公私钥会生成保存到到当前目录下。**



###### 3.6 异常处理

> Swoft 的异常处理定义在 app/Exception 目录
>
> 如果没有主动拦截异常，系统类自动接管异常类 app/Exception/Handler/HttpExceptionHandler.php
> 例如：验证器中的异常会被系统自动接管



**自定义异常类**

第一步：自定义类接管 PHP 系统异常类，不需要任务操作

~~~php
# vi app/Exception/ApiException.php

<?php declare(strict_types=1);
namespace App\Exception;

use Exception;

/**
 * Class ApiException
 *
 * @since 2.0
 */
class ApiException extends \Exception
{
}
~~~

第二步：自定义处理异常




**主动抛出异常**

```php
throw new ApiException('接口处理异常');
```



###### 3.7 验证器

**系统验证器**

> 1、创建验证器

**打上注解并命名验证器 @Validator(name="login")**

~~~php
# vi app/Http/Validator/Manager/LoginValidator.php

<?php declare(strict_types=1);
namespace App\Http\Validator\Manager;

use Swoft\Validator\Annotation\Mapping\IsString;
use Swoft\Validator\Annotation\Mapping\Length;
use Swoft\Validator\Annotation\Mapping\Pattern;
use Swoft\Validator\Annotation\Mapping\Required;
use Swoft\Validator\Annotation\Mapping\Validator;

/**
 * Class LoginValidator
 * @Validator(name="login")
 */
class LoginValidator
{
    /**
     * @IsString(message="请正确填写手机号")
     * @Length(min=11,max=11,message="手机号码长度是11位数字")
     * @Pattern(regex="/^13[0-9]{1}[0-9]{8}$|^14[57]{1}[0-9]{8}$|^15[^4]{1}[0-9]{8}$|^17[0-9]{1}[0-9]{8}$|^18[0-9]{9}$|^199[0-9]{8}$|^198[0-9]{8}$|^166[0-9]{8}$/",message="手机号码不正确")
     * @Required()
     * @var string
     */
    protected $mobile = '';

    /**
     * @IsString(message="短信验证码必须是数字")
     * @Length(min=4,max=6,message="短信码长度是4-6位")
     * @Required()
     */
    protected $msgcode = '';
}

~~~



> 2、使用验证器

**控制器中的方法上打上注解 @Validate(validator="login")**

```php
<?php declare(strict_types=1);
namespace App\Http\Controller\Manager;

use Firebase\JWT\JWT;
use Swoft\Http\Message\Request;
use Swoft\Http\Message\Response;
use Swoft\Http\Message\ContentType;
use Swoft\Validator\Annotation\Mapping\Validate;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;
use Swoft\Http\Server\Annotation\Mapping\RequestMethod;

/**
 * Class AccountController
 * @Controller(prefix="/account")
 */
class AccountController
{
    /**
     * 用户登录
     * @RequestMapping(route="login",method={RequestMethod::POST})
     * @Validate(validator="login")
     */
    public function login(Request $request, Response $response) : Response {
		//TODO
    }
}
```



**自定义验证器**





###### 

```php
##配置文件：app/bean.php
'db' => [
    'class'    => Database::class,
    'dsn'      => 'mysql:dbname=tswoft;host=127.0.0.1',
    'username' => 'root',
    'password' => '123456',
    'charset'  => 'utf8mb4',
],
'db.pool' => [
    'class'     => Pool::class,
    'database'  => bean('db'), //连接池要与上面的key对应
],
```





###### 3.7 MySQL

###### 3.8 Redis



###### 3.9 消息队列

