###### 准备工作

~~~php
# composer create-project swoft/swoft www.chenglh.com
# chmod -R 777 www.chenglh.com/
# chown -R chenglh:chenglh www.chenglh.com/
~~~

创建githbu仓库： www.chenglh.com



PHP编辑器：phpStorm

把 Linux服务器的代码同步到 本地目录，

初始化仓库代码。

清理优化代码

~~~php
app/Exception/*
app/Http/Controller/*
app/Http/Middleware/*
app/Listener/*
app/Model/Dao/*
app/Model/Entity/*
app/Model/Logic/*
app/Model/Data/*
app/Process/*
app/Task/*
~~~



Html编辑器：hbuilderx

https://www.dcloud.io/hbuilderx.html



创建数据库 swoft

~~~mysql
CREATE TABLE `hx_manager` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增ID',
  `username` varchar(16) NOT NULL COMMENT '登录账号',
  `password` varchar(32) NOT NULL DEFAULT '' COMMENT '登录密码',
  `password_salt` char(4) NOT NULL DEFAULT '' COMMENT '密码盐值',
  `role_id` int(11) NOT NULL COMMENT '所属分组',
  `is_login` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '登录状态,0未登录,1登录',
  `last_login_ip` varchar(20) NOT NULL DEFAULT '0.0.0.0' COMMENT '登录IP',
  `last_login_time` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最后登录时间',
  `login_status` tinyint(3) NOT NULL DEFAULT '1' COMMENT '状态,1正常;2禁用',
  `create_time` int(11) NOT NULL DEFAULT '0' COMMENT '创建时间',
  `update_time` int(11) NOT NULL DEFAULT '0' COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='管理员表';

CREATE TABLE `hx_manager_role` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增ID',
  `role_name` varchar(15) NOT NULL DEFAULT '' COMMENT '所属分组',
  `menu_set` text NOT NULL COMMENT '角色菜单',
  `create_time` int(11) NOT NULL COMMENT '创建时间',
  `role_desc` varchar(50) NOT NULL DEFAULT '' COMMENT '角色描述',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='管理员角色表';

-- 插入数据
INSERT INTO `hx_manager_menu` VALUES ('1', '系统管理', '0', '1', '', '0', '系统管理', 'layui-icon-home');
INSERT INTO `hx_manager_menu` VALUES ('2', '管理员列表', '1', '1', 'managers/index', '0', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('3', '管理员添加', '1', '2', 'managers/add', '1', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('4', '管理员修改', '1', '3', 'managers/update', '1', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('5', '管理员删除', '1', '4', 'managers/del', '1', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('6', '管理员信息', '1', '5', 'managers/get', '1', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('7', '角色列表', '1', '6', 'roles/index', '0', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('8', '角色添加', '1', '7', 'roles/add', '1', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('9', '角色修改', '1', '8', 'roles/update', '1', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('10', '角色删除', '1', '9', 'roles/del', '1', '系统管理', null);
INSERT INTO `hx_manager_menu` VALUES ('11', '角色信息', '1', '10', 'roles/get', '1', '系统管理', null);
~~~

修改数据库配置 app/bean.php

~~~php
'db' => [
    'class'    => Database::class,
    'dsn'      => 'mysql:dbname=swoft;host=127.0.0.1',
    'username' => 'root',
    'password' => '123456',
    'charset'  => 'utf8mb4',
	'prefix'	=> 'hx_'
],
'db.pool' => [
	'class'    => Pool::class,
	'database' => bean('db'),
],
~~~



下载开发者工具

查看最新版本，https://github.com/swoft-cloud/swoft-cli/releases，把以下{VERSION}替换最新版本号

> \# wget https://github.com/swoft-cloud/swoft-cli/releases/download/{VERSION}/swoftcli.phar

~~~php
# php swoftcli.phar -V
查看版本信息

##创建控制器
$ php swoftcli.phar gen:http-ctrl managers @app/Http/Controller/Manager -n App\\Http\\Controller\\Manager --prefix /managers
$ php swoftcli.phar gen:http-ctrl roles @app/Http/Controller/Manager -n App\\Http\\Controller\\Manager --prefix /roles
$ php swoftcli.phar gen:http-ctrl public @app/Http/Controller/Manager -n App\\Http\\Controller\\Manager --prefix /public
~~~



修改模板配置  src/config.js

~~~js
,name: '四叶草科技'
,tableName: 'xiyuechaoAdmin' //本地存储表名
,MOD_NAME: 'admin' //模块事件名
,debug: true //是否开启调试模式。如开启，接口异常时会抛出异常 URL 等信息
,interceptor: true//是否开启未登入拦截

/** 自定义参数start */
,website_url:'www.chenglh.com' //官方链接
,website_api_url:'http://www.chenglh.com:18306/v1/'//总后台管理链接
/** 自定义参数end */


//自定义响应字段
,response: {
	statusName: 'code' //数据状态的字段名称
	,statusCode: {
		ok: 200 //数据状态一切正常的状态码
		,logout: 201 //登录状态失效的状态码
	},
	msgName: 'msg' //状态信息的字段名称
	,dataName: 'data' //数据详情的字段名称
}
~~~

调用配置参数

~~~javascript
<script type="text/html" template>
{{ layui.setter.website_url }}
{{ layui.setter.manager_url }}
</script>
~~~

登录请求接口

~~~js
//请求登入接口
admin.req({
	url: setter.website_api_url + 'login' //实际使用请改成服务端真实接口
	,data: obj.field
	,type: 'POST'   //使用POST方式提交
	,done: function(res) {

		//请求成功后，写入 access_token
		layui.data(setter.tableName, {
			key: setter.request.tokenName,
			value: res.data.access_token
		});
~~~

composer安装验证码（跨域，有点难解决sessionID同步问题，暂时没使用了）

~~~php
# composer require easyswoole/verifycode=3.x
# vi app/Http/Controller/Manager/Public.php

<?php declare(strict_types=1);
/**
 * 公共模块
 * @Author：chenglh
 * @Time：2020-06-07
 */

namespace App\Http\Controller\Manager;

use EasySwoole\VerifyCode\Conf;
use EasySwoole\VerifyCode\VerifyCode;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;
use Swoft\Http\Server\Annotation\Mapping\RequestMethod;
use Swoft\Http\Message\Request;
use Swoft\Http\Message\Response;

/**
 * Class PublicController
 * @Controller(prefix="/v1/public")
 * @package App\Http\Controller\Manager
 */
class PublicController{
	/**
	 * @RequestMapping(route="captcha",method={RequestMethod::GET})
	 */
	public function captcha(Response $response): Response {
		$verifyConf = new Conf();
		$verifyConf->setCharset('23456789AaBbCcdEeFfGgHhiJjKkLMmNnPpQqRrSsTtUuVvWwXxYyZz')
			->setBackColor('#3A0000') //设置背景色
			->setBackColor('CCC')
			->setBackColor([30, 144, 255])
			->setUseNoise(); //开启或关闭混淆噪点
		$captcha = new VerifyCode($verifyConf);

		//生成验证码
		return $response->withHeader('Content-Type','image/png')
			->withContent($captcha->DrawCode()->getImageByte());
	}
}
~~~

创建中间件：

~~~php
# php swoftcli.phar gen:http-mdl
Please input class name(no suffix and ext. eg. test): Api
Target File: app/Http/Middleware/ApiMiddleware.php
~~~

配置全局中间件 app/bean.php

```
\App\Http\Middleware\ApiMiddleware::class, //全局中间件
```

修改中间件，使其支持跨域调用

```

```



在config/app.php定义变量

~~~php
<?php
$envVal = env('WAREHOUSE_CODE');

return [
    // app.warehouseCode
    // 'warehouseCode' => ['a', 'b'],
    'warehouseCode' => $envVal ? explode(',', $envVal) : [],

	'website' => 'http://www.tladmin.com',
];
~~~

自定义变量调用方式

~~~php
config('app.website')
~~~

创建实体：

```
# php ./bin/swoft entity:create -d manager,manager_menu,manager_role --remove_prefix=hx_
```

配置 redis

```
 'redis'  => [
        'class'    => RedisDb::class,
        'host'     => '127.0.0.1',
        'port'     => 6379,
        'database' => 0,
        'option'   => [
            'prefix' => 'hx:',
			'serializer' => 0 //不需要序列化
        ]
    ],
'redis.pool' => [
    'class'       => Swoft\Redis\Pool::class,
    'redisDb'     => bean('redis'),
    'minActive'   => 10,
    'maxActive'   => 20,
    'maxWait'     => 0,
    'maxWaitTime' => 0,
    'maxIdleTime' => 60,
],
```

~~~
# composer require firebase/php-jwt
~~~



























Models分层结构

Swoft的Models里面分为四层：Dao、Data、Entity、Logic。

采用分层结构可以让每一个代码文件的代码量达到最小化，每一层各司其职。

Controller->Logic

Controller->Service->Data->Dao

* Logic：逻辑层
* Service：业务处理
* Data：数据缓存层
* Dao：数据操作层
* Entity：实体



Logic 负责接收Controller传递过来的请求参数进行校验和重新组织数据。



Data 当Logic层进行过数据判断和逻辑分发之后，这里负责数据缓存处理，我们可以在这层决定我们是从缓存获取数据，还是从其他数据源获取数据。



Dao这里负责对数据库的操作，它的下一层不一定需要调用Entity可以直接使用查询器进行查询，因为这层只是对数据库的增删改查而已。



Entity

每一张数据表的实体。

Swoft并不要求开发者按照上面的调用链去走，也可以直接在Controller操作Entity或QueryBuilder进程数据操作，分层只是为了在大项目里面代码结构更清晰。

再 加多一层Service做逻辑处理



Controller

~~~php

~~~















