###### 第一章 Tp6简介





> 基础架构

<img src="H:\笔记本\Thinkphp6.assets\image-20200629174249021.png" alt="image-20200629174249021" style="zoom:95%;float:left;" />



~~~php
<?php
namespace app\controller;
use app\BaseController;
use think\facade\Request;

//use think\Request;

class Index extends BaseController
{
    //public function demo(Request $request) {
    //    print_r($request->param());//注入方式调用
    //}

    public function test() {
        $params = Request::param();//门面方式调用
        print_r($params);
    }
}
~~~



~~~php
<?php
public function show() {
   $result = [
       'status' => 1,
       'message' => 'ok',
       'data' => [
           'user_id' => 1,
           'user_name' => 'chenglh'
       ]
   ];
   $header = ['token'=>"asdfasdfdcre"];
   
   return json($result, 201, $header);
}

public function request(/** Request $request*/) {
    //第一种：对象获取
    dump($this->request->post());
    dump($this->request->get());
    dump($this->request->param('age', 18, 'intval'));
    
    //第二种：方法参数中注入对象
    $request->param('age', 18, 'intval');
	
    //第三种,input方法
    vardump(input('name','clh','string'));
    
    //第四种，TP方法
    request()->param('name','clh','string');
    
    //第五种，门面模式
    Request::param('age', 18, 'intval');
}
~~~

