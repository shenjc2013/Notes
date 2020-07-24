~~~php
##Laravel6.x从入门到进阶，深入浅出
https://www.bilibili.com/video/BV1eg4y1q7r7?p=42

##基于PHP Laravel 框架的项目实战全程实录
https://www.bilibili.com/video/BV1kJ411i7RY?p=1
~~~



~~~php
#下载安装
composer create-project --prefer-dist laravel/laravel blog

~~~

~~~php
//分布式锁
public function index() {
    $block = Cache::lock('lock', 10); //(key,10s缓存)，返回是RedisLock对象
    if ($block->get()) {
        //TODO执行业务逻辑
        $block->release(); //释放锁
        return '业务执行完成';
    }
}
~~~



~~~php
> php artisan make:controller tradeController

> php artisan make:job trade

<?php
......

class tradeController extends Controller
{
    public function trade() {
        //订单参数
        $data = [
            'order_id' => date('YmdHis').uniqid()
        ];
        
        $job = new trade();
       
    }    
}
~~~



~~~
 //https://www.bilibili.com/video/BV1cZ4y1u7xM?p=14
~~~

