##### 第一章 项目基础搭建

###### 1.1 基础准备工作

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
# chown -R chenglh:chenglh www.chenglh.com/    //如果不修改用户组，phpstorm上传文件失败
```



###### 1.6 注意事项

```php
开放端口
# iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
# iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 18306 -j ACCEPT
# iptables-save > /etc/iptables.up.rules

启动Swoft命令：
# php /data/www.chenglh.com/bin/swoft http:start

如果端口被占用，需要kill -9 + 端口号
# netstat -tlnp | grep 18306
```



---

##### 第二章 项目实战

###### 2.1 配置数据库

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

