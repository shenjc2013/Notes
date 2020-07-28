~~~php
##Laravel6.x从入门到进阶，深入浅出
https://www.bilibili.com/video/BV1eg4y1q7r7?p=42

##基于PHP Laravel 框架的项目实战全程实录
https://www.bilibili.com/video/BV1kJ411i7RY?p=1
~~~



###### 1.1 分布式锁

> 下载安装

~~~php
#下载安装Laravel
composer create-project --prefer-dist laravel/laravel blog

#安装predis
composer require predis/predis
~~~



> 分布式锁：使用Redis来做互拆锁

如服务器A、B、C三台，一般不建议把Redis部署到某一台服务器(如B)上，对于用户访问B时没有延迟，但对于A、C有延迟，会造成不公平。可以把Redis单独部署一台服务器，对于服务器A、B、C是公平获取锁的。

<img src="H:\笔记本\Laravel.assets\image-20200728135737509.png" alt="image-20200728135737509" style="float:left;" />



> laravel已经封装了-分布式锁

**配置**

~~~php
# vi .env
将缓存驱动 CACHE_DRIVER=file 修改成 CACHE_DRIVER=redis
~~~



**分布式锁**

~~~php
# /vendor/laravel/framework/src/Illuminate/Events/RedisLock.php

//当前路径下还有其他的锁，如MemcachedLock.php缓存锁等
class RedisLock extends Lock{  //继承锁类
    //....
}

#对应的缓存执行器：/vendor/laravel/framework/src/Illuminate/Events/RedisStore.php
public function lock($name, $seconds = 0, $owner = null) {
    return new RedisLock($this->connection(), $this->prefix.$name, $seconds, $owner);
}
~~~



**原理**

~~~php
setnx key value
exprie key 10s
    
Redis单线程
    事件的分配器
    命令执行器
~~~



**Laravel框架**

~~~php
//创建控制器
> php artisan make:controller RedisLockController

# vi routes/web.php 添加路由
Route::get('index', 'RedisLockController@index');
~~~



举个栗子：

~~~php
use Illuminate\Support\Facades\Cache;

class RedisCacheController extends Controller {
    public function index() {
        $block = Cache::lock('stock', 10); //(key,10s缓存)，返回是Redis Lock对象
        //$block 对象获取锁
        if ($block->get()) {
            //TODO执行业务逻辑
            $block->release(); //释放锁
            return '执行完成';
        }
    }
}
~~~



> 问题：会不会互删锁，如A服务请求会不会删除B服务请求的锁，A缓存锁时间到10s释放了，B服务请求进来，已经上锁，但是A服务器执行到  $block->release()，即把B的锁给删除了

~~~php
#laravel底层 /vendor/laravel/framework/src/Illuminate/Events/RedisLock.php
public function release() {
    $this->redis->eval(LuaScripts::releaseLock(), 1, $this->name, $this->owner);
}

//链接到对应的函数
public static function releaseLock()
{
    return <<<'LUA'
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
LUA;
}
/** 从而A服务器的锁，删除加到A对应的锁
 * 所以不会乱删除锁
 */
~~~



###### 1.2 消息队列

> 秒杀、排队处理业务、应用解耦、流量削峰等

~~~php
#创建控制器
> php artisan make:controller TradeController

#创建任务类
> php artisan make:job Trade

<?php
//...
class TradeController extends Controller {
    public function trade() {
        //订单参数
        $data = [
            'order_id' => date('YmdHis').uniqid()
        ];
        
        $job = new Trade();
        $job->dispatch($job)->onQueue('trade'); //分发到队列，且命名为trade
        return '订单的方法';
    }    
}

/** 命令行生成文件原理 */
/**
 * 1.在 index.php 文件里
 * $app = require_once __DIR__.'/../bootstrap/app.php';
 *
 * 2.代码跟踪到 app.php 文件
 * $app->singleton(
 *    Illuminate\Contracts\Http\Kernel::class,   "走路由模式"
 *    App\Http\Kernel::class
 *	);
 * $app->singleton(
 *   Illuminate\Contracts\Console\Kernel::class,  "走命令行模式"
 *   App\Console\Kernel::class   //点进去看代码，去到第三步
 * );
 *
 * 3. App\Console\Kernel.php
 * use Illuminate\Foundation\Console\Kernel as ConsoleKernel; //再点进去第4步
 * class Kernel extends ConsoleKernel {...}
 * 
 * 4.拿到这个实例(容器)，进行操作
 * public function __construct(Application $app, Dispatcher $events){
 * 	   $this->app = $app;
 *     ......
 * }
 * 
 * 5.其实是来到 /vendor\laravel\framework\src\Illumiate\Foundation\Console\JobMakeCommand.php
 * protected $name = 'make:job';
 * return $this->option('sync')
 *                 ? __DIR__.'/stubs/job.stub'
 *                 : __DIR__.'/stubs/job-queued.stub';
 * 里边有对应的文件模板，即命令行调用模板生成指定文件
 */
~~~



> 修改配置

~~~php
# vi .env
QUEUE_CONNECTION=redis
~~~



> 队列任务

~~~php
<?php
namespace App\Http\Controllers;

use App\Jobs\Trade;
use Illuminate\Http\Request;

class TradeController extends Controller
{
    public function index() {
        //订单参数
        $data = [
            'order_id' => date('YmdHis').uniqid()
        ];

        $job = new Trade($data);
        $job->dispatch($job)->onQueue('trade'); //分发到队列，且命名为trade
        return '订单的方法';
    }
}


~~~





~~~
 //https://www.bilibili.com/video/BV1cZ4y1u7xM?p=14
~~~

