> Swoole是一个为PHP用C和C++编写的基于事件的高性能异步&协程并行网络通信引擎。

事件驱动与异步，例如，ajax异步请求

~~~javascript
$.ajax({
    url:'xxx',
    data:yyy,
    success:function(data) {
    	//成功时
    },
    error:function() {
    	//失败时
    },
    complete:function(){
        //请求完成时，(请求成功或失败时均调用)
    }
});
console.log('ajax注册完事件后，进入异步处理，先执行这里打印信息');
~~~



