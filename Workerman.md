Workerman

~~~php
课程链接
#https://www.bilibili.com/video/BV187411o7D4?from=search&seid=1851494477709103954
~~~



##### Workerman

###### 1.1 框架简介

Workerman是一款**开源高性能异步**PHP socket框架。

GatewayWorker框架，是对Workerman的进一步封装。

官网：https://www.workerman.net



###### 1.2 需求区别

如果你的项目是长连接并且需要客户端与客户端之间通讯，建议使用GatewayWorker。
短连接或者不需要客户端与客户端之间通讯的项目建议使用Workerman。
GatewayWorker不支持UDP监听，所以UDP服务请选择Workerman。
如果你是一个有多进程socket编程经验的人，喜欢定制自己的进程模型，可以选择Workerman。



###### 1.3  软件安装

~~~php
//###0、检查环境
$ curl -Ss http://www.workerman.net/check.php | php
PHP Version >= 5.3.3                  [OK] 
Extension pcntl check                 [OK] 
Extension posix check 

####1、安装核心包（不包含start_gateway.php start_businessworker.php等启动入口文件）
$ composer require workerman/gateway-worker

/** 下载聊天包demo */

####2、参照start.php
# vi start.php
<?php
/**
 * run with command
 * php start.php start
 */

ini_set('display_errors', 'on');
use Workerman\Worker;

if(strpos(strtolower(PHP_OS), 'win') === 0)
{
    exit("start.php not support windows, please use start_for_win.bat\n");
}

// 检查扩展
if(!extension_loaded('pcntl'))
{
    exit("Please install pcntl extension. See http://doc3.workerman.net/appendices/install-extension.html\n");
}

if(!extension_loaded('posix'))
{
    exit("Please install posix extension. See http://doc3.workerman.net/appendices/install-extension.html\n");
}

// 标记是全局启动
define('GLOBAL_START', 1);

require_once __DIR__ . '/vendor/autoload.php';

// 加载所有Applications/*/start.php，以便启动所有服务
foreach(glob(__DIR__.'/app/*/start*.php') as $start_file) //【修改这里文件名称 app】
{
    require_once $start_file;
}
// 运行所有服务
Worker::runAll();

####3、把demo里的文件夹/Applications/YouraApp复制到TP6的app目录下，并修改名称为chat

####4、修改 app/chat/start_gateway.php
$gateway = new Gateway("websocket://0.0.0.0:8282"); //【tcp修改为webstock协议】

//【以后所有业务基本是走app/chat/Events.php,其他几个文件很去动代码的】

####5、启动 (调试模式，后台守护进程模式)
$ php start.php start | stop | restart | reload

php start.php start -d
~~~



###### 1.4 开发请求

创建控制器

~~~php
# vi Index.php
<?php
namespace app\controller;

use app\BaseController;
use think\facade\View;

class Index extends BaseController {
    public function index() {
        return View::fetch('index');
    }
}
~~~

导入模板

~~~php
# /view/index/index

资源文件放置 /public/static目录下
~~~



实现简单的群聊功能

~~~html
# vi index.html
<script language="JavaScript">
  var ws = new WebSocket("ws://127.0.0.1:8282");

  ws.onmessage = function(e) {
    console.log(e)
  }

  $(".send").click(function() {
    var input_val = $(".input-value").val();
    ws.send(input_val)
    $(".input-value").val("");
  });
</script>
~~~



程序中的 app/chat/Events.php

~~~php
#自带了接入和群发消息功能
/**
 * 当客户端连接时触发
 */
public static function onConnect($client_id) {
  // 向当前client_id发送数据 
  Gateway::sendToClient($client_id, "Hello $client_id\r\n");
  // 向所有人发送
  Gateway::sendToAll("$client_id login\r\n");
}

/**
 * 当客户端发来消息时触发
 */
public static function onMessage($client_id, $message) {
  // 向所有人发送 
  Gateway::sendToAll("$client_id said $message\r\n");
}
~~~



###### 1.5 接口

官方文档：http://doc2.workerman.net/

常用的菜单

~~~
Events类的回调属性
    onWorkerStart
    onConnect
    ....
Lib/Gateway类提供的接口
    sendToAll
    sendToClient
    isOnline
    bindUid
    sendToUid
    ....
~~~



实现一个计数器，打印客户端连接信息

~~~php
# vi app/chat/Events.php
/**
 * 当客户端连接时触发
 */
public static function onConnect($client_id) {
  global $number;//设置全局变量

  Gateway::sendToClient($client_id, "Hello $client_id\r\n");
  Gateway::sendToAll("$client_id login\r\n");

  echo "connect : ", ++$number, ' : ', $client_id, PHP_EOL;
}
~~~



每次修改完 事件处理文件，都需要重启 workerman



1.6 类型划分

内容通过json来传递，使用opt来区分类型。

~~~php
$(".send").click(function() {
    var text = $(".input-value").val();
    var message = '{"data":"'+text+'","opt":"say"}';
    ws.send(message)
    $(".input-value").val("");
});
~~~



php接收处理

~~~php
public static function onMessage($client_id, $message) {
  $data = json_decode($message,true);
  if (empty($data)) {
    return ;
  }

  switch ($data['opt']) {
    case "say":
      $result = [
        'type'=>"text",
        'id'=>$client_id,
        'data'=>$data['data']
      ];

      // 向所有人发送
      Gateway::sendToAll(json_encode($result));
      break;
  }

  // 向所有人发送 
  //Gateway::sendToAll("$client_id said $message\r\n");
  return ;
}
~~~



html处理

~~~javascript
ws.onmessage = function(e) {
  var _html  = '<div class="bubble you">'
  _html += data.data
  _html += '</div>';
  $("#person1").append(_html);
}
~~~



