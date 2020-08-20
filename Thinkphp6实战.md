##### Thinkphp6实战



复制 dist 、start、src目录到 public/admin目录下

~~~html
# vi public/admin.html

<script>
    location.href='admin/start'
</script>
~~~



修改项目开发环境

~~~html
# vi start/index.html
<script>
  layui.config({
    base: '../src/' //指定 layuiAdmin 项目路径，本地开发用 src，线上用 dist
    ,version: new Date().getTime() //  缓存设置
  }).use('index');
</script>
~~~



修改配置文件

~~~html
# vi src/config.js
,name: 'blmCMS'
,tableName: 'layuiAdmin' //本地存储表名
,MOD_NAME: 'admin' //模块事件名
~~~



获取路由上参数

~~~html
# vi src/views/category.html
<script type="text/html" template>
    {{ layui.router().search.id }}
</script>

<script>
    var r = layui.router().search;
    layui.use('admin', function(){
       var admin = layui.admin;
       admin.on('hash(id)', function(router){
           //
       });
    });
</script>
~~~



获取数据

~~~html

~~~



