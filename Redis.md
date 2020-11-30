##### 第一章：Redis简介

###### 1.1 官网地址

```php
https://redis.io
https://github.com/antirez/redis
```



------

###### 1.2 NoSQL介绍

> 解决问题

NoSQL数据库的产生是为了解决大规模数据集合和多种数据类型带来的挑战，尤其是大数据应用难题。

**NoSQL分类**

1. 键值(Key-Value)存储数据库，常见：Redis
2. 列存储数据库，常见：Hbase
3. 文档型数据库，常见：MongoDB
4. 图形(Graph)数据库，常见：Neo4J



------

###### 1.3 适用场景

1. 数据模型比较简单
2. 需要灵活性更强的IT系统
3. 对数据库性能要求较高
4. 不需要高度的数据一致性
5. 对于给定KEY,比较容易映射复杂的环境

  ```php
实用案例
/*
1、数据缓存，提升网站访问速度，降低数据库压力；

2、排行榜，如京东月度销量榜、商品按时间上新排行等。有序集合数据应用；

3、计数器 电商商品浏览量、视频网站播放量等。为了保证数据实效性，减轻数据库压力。Redis提供的 incr命令实现计数器功能；

4、分布式会话集群模式下，在应用不多的情况下一般使用容器自带的session复制功能就能满足；当应用增多相对复杂的系统中，一般都会搭建以Redis等内存数据库为中心的session服务，session不再由容器管理，而是由session服务及内存数据库管理；

5、分布式锁分布式技术带来的技术挑战是对同一个资源的并发访问，如全局ID、减库存、秒杀等场景；并发量不大的场景可以使用数据库的悲观锁、乐观锁来实现；但在并发量高的场合中，利用数据库锁来控制资源并发访问是不太理想的，大大影响数据库性能。使用利用Redis和setnx功能来编写分布式的锁，如果设置返回获取锁成功，否则获取锁失败。

6、社交网络点赞、踩、关注/被关注、共同好友等是社交网站的基本功能，社交网站的访问量通常较大，而且传统的关系数据库类型不适合存储这种类型的数据，Redis提供的哈希、集合等数据结构能很方便实现这些功能。

7、最新列表 Redis列表结构、LPUSH可以在列表头部插入一个内容ID作为关键字，LTRIM可用来限制列表的数量

8、消息系统，消息队列是大型网站必用中间件，如ActiveMQ、RabbitMQ、Kafka等流行的消息队列中间件，主要用于业务解耦、流量峭峰及异步处理实时性低的业务。Redis提供了发布/订阅及阻塞队列功能，能实现一个简单的消息队列系统。*/

Redis与其他Nosql产品对比
1、支持数据的持久化，可以将内存中数据保存到磁盘中，重启的时候可再次加载到内存

2、不仅支持简单key-value类型，还支持 list、set、 zset、hash等数据结构类型

3、支持数据备份，集群等高可用功能 
  ```

------

###### 1.4 Redis特点

1. 读写速度快：读取速度11W/秒；写速度8.1W/秒
2. 提供丰富数据类型：String 、List 、Set 、ZSet、Hash等
3. 具备原子性 Redis所有操作都是原子性，要么执行成功要么执行失败，多个操作也支持事务，即原子性；通过Multi和Exce指令代码块
4. 丰富特性支持 publish/subscribe，通知key过期等特征



------

###### 1.5 Redis缺点

消耗内存，占用内存过高

```php
//持久化 。Redis 可以使用两种方式实现持久化过程
第一种-定时快照（snapshot）, 每间隔一段时间将整个数据库写到磁盘上，每次均是全部数据，代价非常高；
第二种-基于语句追加（AOF）只追踪变化的数据，但是追加的log 可能过大，同时所有的操作均重新执行一遍，恢复速度慢。
```



##### 第二章：Redis基本操作

###### 2.1 软件安装

```php
/** 更新系统环境 */
# apt-get update
# apt-get install gcc automake autoconf libtool make

源码安装
# wget http://download.redis.io/releases/redis-5.0.8.tar.gz
//tar -xzf redis-5.0.8.tar.gz -C /chenglh/software/  // -C解压如果指定到目录
# tar -xzf redis-5.0.8.tar.gz
# cd redis-5.0.8
# make  &&  make PREFIX=/usr/local/redis install

Docker下安装
镜向下载：https://hub.docker.com/_/redis/                           

# docker pull redis   //下载redis最新镜向 
# docker images       //查看已下载镜向

创建并运行容器
# docker run -d --name redis-6379 -p 6379:6379 redis --requirepass "123456"
# docker ps  //列出所有容器
# docker exec -it redis-6379 bash     //进入redis-6379容器
# docker exec -it redis-6379 redis-cli -a 123456  //进入客户端
```

###### 2.2 启动命令

```php
# cd /usr/local/redis/bin/
# ls -l
redis-benchmark //性能测试工具
redis-check-aof //aof文件损坏修复
redis-check-rdb //rdb文件损坏修复
redis-cli       //客户端工具
redis-sentinel  //哨兵集群
redis-server    //启动服务器

启动命令
# /usr/local/redis/bin/redis-server 启动Redis服务端
# ps aux | grep redis
# /usr/local/redis/bin/redis-server -v
# /usr/local/redis/bin/redis-server /usr/local/redis/redis.conf  指定配置文件启动

查看是否正确启动
# ps -ef | grep 6379
```

###### 2.3 客户端连接

```php
# /usr/local/redis/bin/redis-cli -v    //查看版本号
# /usr/local/redis/bin/redis-cli       //启动Redis客户端
# ./redis-cli -h IP地址 -p 端口 -a 密码  //默认IP本机，端口6379，无密码
```



###### 2.4 远程工具连接

```php
1、客户端工具访问（下载工具：RedisDesktopManager）

2、需要修改配置文件redis.conf，将 bind 127.0.0.1 注释去掉

3、开放端口
# iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 6379 -j ACCEPT
# iptables-save > /etc/iptables.up.rules 
```



###### 2.5 关闭Redis

```php
1、断电或非正常关闭，容易丢失数据，还没到达触发数据持久化机制
# ps -ef | grep redis
# kill -9 PID

2、/usr/local/redis/bin/redis-cli shutdown 或者连接客户端命令行下
redis > shutdown

如果设置了密码需要先通过客户端登录，然后执行shutdown命令即可关闭服务

在onestack中
# service redis-server {start|stop|status|restart}
```



##### 第三章：Redis配置详解

###### 3.1 配置路径

```php
# cp /home/chenglh/redis-5.0.5/redis.conf  /usr/local/redis/
```



###### 3.2 配置解读

==配置文件中大小写不敏感。==

> 网络

~~~php
#units are case insensitive so 1GB 1Gb 1gB are all the same.

1. Redis默认不是以守护进程的方式运行，修改配置项， yes启用守护进程                       
//daemonize no   //终端退出，Redis进程也会中止
daemonize yes    //yes，开启守护进程模式

2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，通过pidfile指定
pidfile  /var/run/redis.pid

3. 指定Redis默认监听端口为6379                                                             
port 6379

4. 绑定的主机地址 (在开发环境下，可以注释掉，允许外网直接访问)
bind 127.0.0.1

5. 当客户端闲置时间多长后关闭联机，如果指定为0，表示关闭该功能
timeout 0 //300 表示空闲5分钟即关闭
~~~



> 通用配置

```php
//daemonize yes    //yes，开启守护进程模式

6. 指定日志记录级别，Redis总共支持四个级别：//debug,verbose,notice,warning，默认为notice
loglevel notice

7. 日志记录方式，默认标准输出，如果配置Redis为守护进程方式运行， 而这里有配置为日志记录方式为
标准输出，则日志将会发送给 /dev/null
logfile /usr/local/redis/var/redis.log

8.设置数据库的数量，默认连接数据库为0号， 可以使用SELECT <dbid>命令在链接指定数据库ID
database 16
```



> 快照，即持久化到文件，.rdb或.aof

~~~php
9. 快照，指定多长时间内，有多少个更新操作，就会将数据同步到数据文件，可以多个条件配合。
save <seconds> <changes>
Redis 提供了三个条件内存持久化
save 900 1        (900s，即15min内有1个更改)
save 300 10       (300s，即5min内有10个更改)
save 60  10000    (60s， 即1min内有1W个更改)

10. 指定存储至本地数据库文件时是否压缩数据，默认为yes,Redis采用LZF压缩算法，如果为了节省CPU时  
间可以关闭该选项，但会导致数据库文件变得巨大，//默认为压缩数据
rdbcompression yes

11. 指定本地数据库文件名，默认为dump.rdb
dbfilename dump.rdb

12. 指定本地数据库存放目录
dir ./

13. 设置本地slave服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行
数据同步
#replicaof <masterip> <masterport>

14. 当master服务设置了密码保护时，slave从服务器连接master的密码
#masterauth <master-password>

15. 设置Redis连接密码，如果设置了连接密码，客户端在连接Redis时需要通过AUTH <password> 命
令提供密码，默认关闭
#requirepass foobared

16. 设置同一时间最大客户端连接数，默认无限制，即maxclients=0，当客户端连接数到达限制时，Redis会关闭新的连接并给客户端返回max number of clients reached错误信息
#maxclients 10000

17. 指定Redis最大内存限制，Redis在启动的时候会把数据加载到内存中，达到最大内存后，Redis会先清除已经到期或即将到期的Key，在此处理方法之后，仍然到达最大的内存设置，将无法在进行写入操作，但仍然可以进行读取操作，Redis 新的vm机制，会把Key放入内存，Value会放入swap区
maxmemory 120000000

18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。Redis本身同步数据文件时按上面save条件来进行同步的，所以一段时间内只存在内存中
appendonly no  #默认是不开启aof模式的，默认是使用rdb方式持久化，大部分情况下rdb完全够用了

19. 指定更新日志文件名，默认为appendonly.aof
appedfilename appendonly.aof #持久化文件名

20. 指定更新日志条件，一共有3个可选值
# appendfsync always /** 每次修改都会sync，消耗性能，并完成磁盘同步（慢，安全） */
appendfsync everysec /** 每一秒写入aof文件，并完成磁盘同步（折中，默认值） */
# appendfsync no     /** 不执行sync，管理员自己手动同步，不等待磁盘同步（快） */

21.Redis中的内存维护策略
# maxmemory-policy noeviction
Redis作为优秀的中间件缓存，时常会存储大量的数据，即使采取了集群部署来动态拓容，也应该即时的整理内存，维持系统性能（如果数据一直新增，内存则很快会占满）

 两种解决方案
 1)、为数据设置超时时间，设定内存，建议不要超过1G，可设置256M-512M
      使用案例：(例如验证码)
      1、expire key time
      2、setex(String key, int seconds, String value)
 2)、采用LRU算法动态删除不用的数据
     内存管理的一种页面置换算法，对于在内存中但又不用的数据块(内存块)叫做LRU，操作系统会根据哪些数据属于LRU而将其移出内存而腾出空间来加载另外的数据

#volatile-lru          设定超时时间的数据中，删除最不常用的数据(常用)
#allkeys-lru           查询所有的key中最不常使用的数据进行删除，这是应用最广泛的策略(常用)
#volatile-lfu          从所有配置了过期的时间的键中驱逐使用频率最少的键
#allkeys-lfu           从所有键中驱逐使用频率最少的键
#volatile-random       在已经设定了超时的数据中随机删除
#allkeys-random        查询所有的key之后随机删除
#volatile-ttl          查询全部设定超时时间的数据，追后马上排序，将马上将要过期的数据进行删除操作
#noeviction            【默认】如果设置为该属性，则不会进行删除操作，如果内存溢出则报错返回
~~~





###### 3.3 自定义配置参数

```php
# vim /usr/local/redis/redis.conf
#daemonize no
daemonize yes

#bind 127.0.0.1

#设置密码(最好设置强大密码来提高安全，因为redis在1s内能进行15W暴力破解密码尝试)
requirepass  123456

使用指定配置文件启动Redis
# /usr/local/redis/bin/redis-server /usr/local/redis/redis.conf

查询进程
# ps -ef | grep redis

客户端登录 (# redis-cli -h host -p port -a pwd)
# /usr/local/redis/bin/redis-cli -h 127.0.0.1 -p 16379 -a 123456
```



##### 第四章：Redis命令

###### 4.1 Redis常用命令

```php
用命令管理Redis的键

DEL key           //删除key操作，返回1或0
DUMP key          //序列化给定key，返回被序列化的值
EXISTS key        //检查给定的key是否存在,返回1或0
EXPIRE key seconds//给定的key设置过期时间
PEXPIRE key milliseconds //设置key的过期时间以毫秒计
TTL Key           //以秒为单位，返回给定key的剩余的过期时间。-1永久有效;-2无效的键
PTTL key          //以毫秒为单位返回key的剩余过期的时间
PERSIST key       //移除key的过期时间，key将永不过期
KEYS pattern      //查找符合所有给定模式（pattern）的key,*代表所有;？代表匹配一个字符
RANDOMKEY         //随机返回一个key
RENAME key newkey //重命名key
TYPE key          //返回key所存储的值的类型

数据库操作
select              //选择数据库(0-15)
move key dbindex    //将当前数据库中的key 转移到其他数据库
dbsize              //查看数据库key数量
info                //查看数据库信息
flushdb             //清空当前数据库
flushall            //清空所有数据库
```



###### 4.2 Redis应用场景

```
1. 限时优惠活动
2. 网站数据缓存（对于一些需要定时更新的数据，例如：积分排榜榜）
3. 手机验证码
4. 限制网站访客频率（例如：1分钟最多访问10次）
```



###### 4.3 Key的命名建议

```
0、单个key最大存入数据为512M 大小
1、key不要太长，尽量不要超过1024字节，这不仅消耗内存，而且会降低查找的效率；
2、key也不要太短，太短的话，key的可读性会降低；
3、在一些项目中，key最好使用统一的命名模式，例如 user:123:passwd
4、Key的名称需要注意区分大小写
```



##### 第五章：Redis数据类型

###### 5.1 String字符串

String类型是二进制安全的，即在传输数据时，信息安全，也就是不被篡改、破译等，如果被攻击，能够即时检测出来，String具有如下特点：

> 1. 编码、解码发生在客户端完成，执行效率高
>
> 2. 不需要频繁的编解码，不会出现乱码

```php
#赋值
SET KEY VALUE        //新增或重写，如果key已经存储值，SET就覆盖旧值，且无视类型
SETNX  KEY VALUE     //只有key不存在设置key的值，具有原子性，可以解决分布式锁问题
MSET KEY VALUE [key vlue ...] //设置一个或多个键值对

#取值
GET KEY                 //获取指定key
GETRANGE KEY start end  //获取key值的部分值,start默认0；end -1结尾
MGET key1 [key2...]     //获取所有(一个或多个)指定的值
GETSET KEY VALUE        //获取key旧值(不存在返回nil)，并且重新重写value
STRLEN key              //字符串长度

#删除
DEL key                 //删除key操作，返回1或0

#自增/减(可作计数器)
INCR KEY				//key值自增1
INCRBY KEY incrment  	//设置步长

DECR KEY				//key值自减1
DECRBY KEY incrment  	//设置步长

#字符串拼接/追加
APPEND KEY VALUE
```



###### 5.2 Hash类型

```php
Hash介绍：
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象；
Redis 中每个 hash 可以存储 2^32 -1 键值对（40多亿）可以看成是具有 key 和 value 的 MAP 容器；
该类型非常适合于存储对象的信息如：uname, upass, age等，该类型的数据仅占很少的磁盘空间（相对于JSON）

使用场景：
1、适合存一个对象，比如存储用户信息，订单信息等
2、Hash是最接近关系型数据结构的数据类型，可以将一个库一条记录或存续中一个对象转换为Hashmap存放在Redis中

为什么不用String存储一个对象？
用普通的key/value结构来存储，主要有2种方式。
(1)、将user_id作为查找KEY把其他信息封装成一个对象以序列化的方式存储，缺点：增加了序列化，反序列化的开销，并且在需要修改其中一项信息的时候，需要把整个对象取出，进行修改操作需要对并发进行保护，引入CAS等复杂问题。
user:1200 = {"user_id":1200,"age":"20","sex":"nam"}

(2)、将用户对象有多少个成员就存成多少个key-value对，虽然省取了序列化开销和并发问题，但是user_id为重复存储，如果存在大量这样的数据，内存浪费会非常大。
例如：如果一个会员表的数据这样表示，开销会很大
users:12000:name chenglh
users:12000:age 18
```

```php
#赋值语法
HSET KEY FIELD VALUE //为指定key设置FIELD/VALUE
HMSET KEY FIELD VALUE [FIELD1 VALUE1 ......] //设置key下多个field/value
例如：hmset user:10223 user_id 10223 user_nickname chenglh user_age 18

#取值语法
HGET KEY FIELD   //获取key下的field值
HMGET KEY FIELD [FIELD1...] //获取key下一个或多个field的值
HGETALL KEY      //返回HASH表中key的所有字段和值
HLEN KEY         //返回HASH表中key的字段数量
例如：
127.0.0.1:6379> HGETALL user:12000
1) "user_id"
2) "12000"
3) "user_nickname"
4) "chenglh"

#删除语法
HDEL KEY field1 [field2....]  //删除一个或多个HASH表字段

#其他语法
HSETNX KEY field value          //只有在field不存在时，才设置HASH字段值
HINCRBY KEY field increment     //HASH表field字段自增
HINCRBYFLOAT KEY field increment//浮点型增加增量
HEXISTS KEY field               //判断KEY字段是否存在
```



###### 5.3 List类型

```php
List介绍：
List类型是一个链表结构的集合，其主要功能有 push、pop 、获取元素等，是一个双向列表结构，可以进行集合的
头部或者发问添加和删除元素，可以作为栈，也可以作为队列。
一个列表最多可以包含2^32 - 1个元素(4294967295，每个列表超过40亿个元素)

应用场景
项目常应用于：1.对数据量大的集合数据删减；2.任务队列
1.对数据量数集合数据删减
  列表数据显示、关注列表、粉丝列表、留言评价等...分页、热点新闻（top5）等；
  利用LRANGE还可以很方便的实现分页功能，在博客系统中，每篇博文的评论也可以存入一个单独的list中；
2.任务队列
  List通常用来实现一个消息队列，而且可以确保先后顺序，不必像MySQL那样还需要通过ORDER BY来进行排序
  任务队列介绍(生产者和消费者模式)

  在处理Web客户端发送的命令请求时，某些操作的执行时间可能会比我们预期的更长一些通过将待执行任务的相关信息放入队列里面，并在之后对队列进行处理，用户可以通过推迟执行那些需要一段时间才能完成的操作，这种将工作交给任务处理器来执行的做法成为任务队列(task queue)
```

```php
#赋值语法
LPUSH KEY value1 [value2]  //将一个或多个值插入到列表左侧
RPUSH KEY value1 [value2]  //将一个或多个值插入到列表右侧

LPUSHX KEY value1 [value2] //KEY存在才能插入成功，否则false
RPUSHX KEY value1 [value2]
使用案例：
> LPUSH order:list 202005091100 202005091111 202005091122 202005091133
//202005091133 202005091122 202005091111 202005091100

#取值语法
LLEN KEY    //获取列表长度
LINDEX KEY INDEX //通过索引下标去获取列表的Value
LRANGE KEY start end //获取列表指定范围内的元素，-1表示最右边，负表示从右边算起

#删除语法
LPOP KEY  //列表的左侧删除一个元素
RPOP KEY  //列表的右侧删除一个元素

BLPOP KEY1 [KEY2...] timeout //移除并获取列表的左侧第一个元素,依次顺序监听列表，哪个列表有数据就返回，否则阻塞列表直到等待超时或发现可以弹出的元素为止
BRPOP KEY1 [KEY2...] timeout //移除并获取列表的右侧第一个元素,依次顺序监听列表.....

LTRIM KEY start stop   //对一个列表进行剪切(trim),保留指定区间内的元素,不在指定区间内的元素都被删除

#修改语法
LSET KEY index value  //通过索引设置列表元素值
LINSERT KEY BEFORE|AFTER wString value //在列表元素wString前(左侧)|后(右侧)插入元素value，如果wString元素不存在即插入不成功

#高级语法
RPOPLPUSH source destination  //移除列表source最后一个元素，并将该元素添加到destination列表中并返回当前元素值
BRPOPLPUSH source destination timeout //监听列表移除并插入，如果列表没有元素会阻塞直到超时
```



###### 5.4  Set类型

```
简介：
Redis的Set是String类型的无序集合，集合成员是唯一的，这就确定这集合中不能出现重复的数据。
Redis中集合通过哈希表来实现的，所以添加，删除，查找的复杂度都是 O(1)
Redis集合中最大的成员数量是2^(32-1)(294967295，每个集合可以存储40多亿个成员)

常用场景：两个集合之间的数据[计算]进行交集、并集、差集运算
1、非常方便的实现如共同关注、共同喜好、二度好友等功能。对上面的所有集合数据操作， 你还可以使用不同的命令选择将结果返回给客户端还是存集到一个新的集合中。
2、利用唯一性，可以统计访问网站的所有独立IP
```

```php
#赋值语法
SADD KEY member [member...]  //向集合添加一个或多个成员，集合会去重

#取值语法
SCARD KEY      //获取集合中元素个数
SMEMBERS KEY   //获取集合中所有元素
SISMEMBER KEY member //判断member元素是否在集合中
SRANDMEMBER KEY [count]  //从集合中返回一个或多个随机元素

#删除语法
SREM KEY member1 [member2] //从集合中删除一个或多个元素，返回成功删除元素的个数
SPOP KEY [count] //移除并返回集合中的一个或多个随机元素
SMOVE source destination member  //把元素从一个集合移到另一个集合中

#运算语法
//差集语法
SDIFF key1 [key2...]  //求出集合key1中所有没有在key2中出现过的元素，即key1中去重出现在key2中的元素
SDIFFSTORE destination key1 [key2...] //获得从key1中去重出现在key2中的元素，然后保存到destination集合中

//交集语法
SINTER key1 [key2...]   //返回给定所有结合的交集(共有数据)
SINTERSTORE destination key1 [key2]  //求交集并存储起来

//并集语法
SUNION key1 [key2]     //返回所有给定集合的并集
SUNIONSTORE destincation key1 [key2]  //求并集并存储起来
```



###### 5.5 Zset类型

```php
简介
1.Redis有序集合和集合一样也是string类型的集合，且不允许重复的成员
2.不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序
3.有序集合的成员是唯一的，但分数(score)却是可以重复
4.集合是通过哈希表实现的，所以添加，删除，查找的复杂度是O(1)

特点
Redis的ZSet是有序的、且不重复
很多时候，我们都将redis中的有序集合叫做zsets, 这是因为redis中，有序集合相关的操作都是z开头的。

```

```php
#赋值语法
ZADD KEY score member [score1 member1...]   //向有序集合添加一个或多个成员，或更新已存在成员的分数

#取值语法
ZCARD  KEY           //获取有序集合的成员个数
ZCOUNT KEY MIN  MAX  //计算有序集合中指定分数范围内成员的个数
ZRANK  KEY member    //获取成员member的索引,(名次+1)

ZRANGE KEY start stop [WITHSCORES]//通过索引返回有序集中指定区间内成员，分数从低到高;-1表示返回全部
ZREVRANGE KEY start stop [WITHSCORES] //指定区间内成员分数从高到低返回结果

ZRANGEBYSCORE KEY min max [WITHSCORES] //-inf +inf相当于正负无穷
ZREVRANGEBYSCORE KEY max min [WITHSCORES]  //+inf -inf

案例使用：
127.0.0.1:6379> ZADD chengji:1 102 chenglh 100 flp 134 zhangsan 122 lisi
127.0.0.1:6379> ZRANGEBYSCORE chengji:1 -inf +inf
1) "flp"
2) "chenglh"
3) "lisi"
4) "zhangsan"
127.0.0.1:6379> ZRANGEBYSCORE chengji:1 -inf +inf withscores
1) "flp"
2) "100"
3) "chenglh"
4) "102"
5) "lisi"
6) "122"
7) "zhangsan"
8) "134"
127.0.0.1:6379> ZREVRANGEBYSCORE chengji:1 +inf -inf
1) "zhangsan"
2) "lisi"
3) "chenglh"
4) "flp"
127.0.0.1:6379> ZREVRANGEBYSCORE chengji:1 +inf -inf withscores
1) "zhangsan"
2) "134"
3) "lisi"
4) "122"
5) "chenglh"
6) "102"
7) "flp"
8) "100"
127.0.0.1:6379> ZRANK chengji:1 zhangsan
(integer) 3

#删除语法
DEL KEY       //删除key
ZREM KEY member [member...]    //移除key下的一个或多个成员
ZREMRANGEBYRANK KEY start stop //移除KEY下指定索引范围的成员(排序是低到高)
ZREMRANGEBYSCORE KEY min max   //移除KEY下指定分数范围内的成员
```

```
使用场景：排行榜(奥运金银铜排行榜，小游戏排行榜,如跳一跳，步数统计排行等)
1、比如twitter的public timeline可以发表时间作为score来存储，这样获取的时候就是自动按照时间排序的结果集
2、比如一个存储全班同学的sorted set,其集合value可以是同学的学好，而score就可以是其考试得分，这样在数据
插入集合的时候，就已经进行了天然的排序。
3、还可以用sorted set来做待权重的队列，比如消息的score为1, 重要的score为2，然后工作县城可以选择按score
来倒序获取工作任务。让重要的事情优先执行。
```



###### 5.6 特殊类型(地理/基数/位图)

**geospatial地址位置**

```bash
朋友的定位，附近的人，打车距离计算?
Redis的GEO可以推算地理位置的信息，两地之间的距离，方圆几里的人
```

```php
#赋值语法
GEOADD KEY longitude latitude member  //将指定的地理空间位置（纬度、经度、名称）添加到指定的key中
/**有效的经度从-180度到180度。
 * 有效的纬度从-85.05112878度到85.05112878度。
 * 当坐标位置超出上述指定范围时，该命令将会返回一个错误
 * 规则：两极无法直接添加，一般会下载城市数据，直接通过java程序一次性导入
 */
127.0.0.1:6379> GEOADD china:city 113.20881481481933 23.150519411873386  12001
(integer) 1
127.0.0.1:6379> GEOADD china:city 113.207932 23.141899 10906 113.194351 23.115681 16002 113.248306 23.11466 15002 113.206589 23.15303 10021
(integer) 4

#取值语法
GEOPOS KEY member [member...]  //查询KEY下一个或多个成员的经纬度
127.0.0.1:6379> GEOPOS china:city 10021 10099
1) 1) "113.20658773183822632"
   2) "23.15302895444717279"
2) 1) "113.40082794427871704"
   2) "23.11376105424668737"
GEODIST KEY member1 member2 [unit]   //返回两个给定位置之间的距离
#指定单位的参数 unit 必须是以下单位的其中一个：
#m 表示单位为米[默认]
#km 表示单位为千米
#mi 表示单位为英里
#ft 表示单位为英尺
127.0.0.1:6379> GEODIST china:city 12001 10021 //两个用户间的距离
"360.1904"

//我附近的人？(获得所有附近的人地址，定位)通过半径来查询
GEORADIUS KEY longitude latitude radius [unit] [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count 限制几个记录]    //通过半径来查询
127.0.0.1:6379> GEORADIUS china:city 113.2 23.1 5 km  //113.2 23.1为当前用户所在的经纬度
1) "16002"
2) "10906"
127.0.0.1:6379> GEORADIUS china:city 113.2 23.1 5 km withdist
1) 1) "16002"  //用户id
   2) "1.8373" //距离km
2) 1) "10906"
   2) "4.7304"

GEORADIUSBYMEMBER KEY member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]  //找出位于指定元素周围的其他元素
127.0.0.1:6379> GEORADIUSBYMEMBER china:city 12001 1 km
1) "12001"
2) "10021"
3) "10906"

#GEO底层的实现原理其实就是zset，可以使用zset命令来操作geo
127.0.0.1:6379> ZRANGE china:city 0 -1
1) "16002"
2) "15002"
3) "10906"
4) "12001"
5) "10021"
6) "10099"

#如果要清除位置退出附近功能
127.0.0.1:6379> ZREM china:city 10099    #清除成员
(integer) 1
127.0.0.1:6379> ZRANGE china:city 0 -1
1) "16002"
2) "15002"
3) "10906"
4) "12001"
5) "10021"
```

**Hyperloglog基数统计**

```bash
什么是基数？即不重复的元素
A{1, 3, 5, 8, 7}
B{1, 3, 7, 8}
基数 = 5
```

**Redis Hyperloglog基数统计的算法！**

优点：占用的内存是固定，2^64不同的元素的技术，只需要12KB内存空间

页面的UV(一个人访问一个网站多次，但是还是算一个人)

传统的方式，set保存用户的id，然后就可以统计set中的元素数量作为标准判断

这个方式如果保存大量的用户ID，就会比较麻烦，且占用大量空间资源，我们的目的是为了计数，而不是保存用户ID

0.81%错误率！统计UV任务，可以忽略不计的。

```php
#赋值语法
PFADD KEY element     //Hyperloglog的KEY里增加一个或多个成员

#取值语法
PFCOUNT KEY          //统计数量
PFMERGE destkey  sourcekey [sourcekey...]   //合并多个source保存到destkey中

127.0.0.1:6379> PFADD 20200510-login 1 2 4 5 6 8 10   #写值
(integer) 1
127.0.0.1:6379> PFADD 20200509-login 1 5 10 18 15 12  #写值
(integer) 1
127.0.0.1:6379> PFCOUNT 20200510-login   #统计
(integer) 7
127.0.0.1:6379> PFMERGE login 20200509-login 20200510-login #合并
OK
127.0.0.1:6379> PFCOUNT login   #统计日活/周活/月活
(integer) 10
```

**Bitmap位图**

```php
位存储
统计用户信息，活跃，不活跃！登录、未登录！打卡，365打卡！两个状态的，都可以使用Bitmaps
Bitmaps位图，数据结构，都是操作二进制来进行记录，就只有0和1两个状态

356天 = 365bit 1kb = 8bit  46个字节左右！

应用场景：
#统计注册IP数
#统计每日访问IP数
#统计在线用户数
#统计用户每天搜索不同词条的个数
#统计真实文章阅读数
#统计用户签到或活跃天数
```

```php
#赋值操作
SETBIT KEY offset value     //设置偏移位置上的值

#读取操作
GETBIT KEY offset			//读取偏移位置上的值

#统计操作
BITCOUNT KEY [start end]    //统计KEY

127.0.0.1:6379> SETBIT user:sign:12002 0 1    #设置是否打卡
(integer) 0
127.0.0.1:6379> SETBIT user:sign:12002 1 0
(integer) 0
127.0.0.1:6379> SETBIT user:sign:12002 2 1
(integer) 0
127.0.0.1:6379> SETBIT user:sign:12002 3 1
(integer) 0
127.0.0.1:6379> GETBIT user:sign:12002 5     #查看某天是否打卡操作
(integer) 1
127.0.0.1:6379> BITCOUNT user:sign:12002     #统计某用户打卡的天数
(integer) 4
```



##### 第六章：Redis其他功能

###### 6.1 Redis事务

```
Redis事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行！
一次性、顺序性、排他性！执行一些命令
------ 队列  set set  set ... 执行-----
Redis单条命令是具有原子性的，但是事务不保证原子性，事务没有隔离的概念
所有命令在事务中，并没有直接被执行，只有发起"EXEC"命令才执行

事务执行的三个阶段
   1、开始事务(multi)
   2、命令入队(.....)
   3、事务执行(exec) 或 取消事务(DISCARD)
```

```php
事务使用
127.0.0.1:6379> MULTI     #开启事务
OK
127.0.0.1:6379> set name chenglh  #具体操作
QUEUED
127.0.0.1:6379> set age 18
QUEUED
127.0.0.1:6379> EXEC  #提交事务
1) OK
2) OK
```

```php
事务的错误处理

编译型异常(代码有问题，命令有错！！)，事务中所有的命令都不会被执行
127.0.0.1:6379> Multi
127.0.0.1:6379> set name chenglh
127.0.0.1:6379> set age 18
127.0.0.1:6379> getval   #没有当前命令，整个事务报错，所有命令不会被执行

运行时异常(如：1除以0)如果事务队列中存在语法性错误，其他命令行可以正常执行的
127.0.0.1:6379> Multi
127.0.0.1:6379> set  name chenglh
127.0.0.1:6379> incr name      #当前命令执行报错，但是其他命令正常执行
127.0.0.1:6379> set age 18
127.0.0.1:6379> exec
```

> 悲观锁：很悲观，认为什么时候都会出问题，无论做什么都会加锁！消耗系统性能
> 乐观锁：很乐观，认为什么时候都不会出问题，所有不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据。

```php
#Redis监控! watch
127.0.0.1:6379> MSET income 100 outpay 0  #初始化收入与支出
OK
127.0.0.1:6379> MGET income outpay
1) "100"
2) "0"
#####如果不监控KEY,则在不同的进程处理下，造成数据污染
//乐观锁start
127.0.0.1:6379> WATCH income outpay
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> DECRBY income 20
QUEUED
127.0.0.1:6379> INCRBY outpay 20
QUEUED
#####在另一个汇话窗口中 对income或outpay进行set/decrby/incrby等操作后，再提交事务
127.0.0.1:6379> exec
(nil)
127.0.0.1:6379> unwatch #放弃监控锁，不能带key
//乐观锁end,程序编写上使用回旋操作即可

##总结
##监视一个或多个KEY，如果事务执行提交之前这些KEY被其他会话窗口命令所改动(增、删、改)，那么当前事务将被打断。
```

###### 6.2  Redis持久化

**6.2.1 持久化之RDB操作**

> 什么是RDB? Redis DataBase，Redis是内存数据库，需要把数据保存到磁盘。



![image-20200510213216616](H:\笔记本\Redis.assets\image-20200510213216616.png)

```
在指定时间间隔内将内存中的数据集快照写入磁盘，即snapshot快照，它恢复时是将快照文件直接读到内存里。

Redis会单独创建 fork一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程结束了，再用这个临时文件替换上次持久化的文件。

整个过程中，主进程是不进行任何IO操作的。这就确保了极高的性能，在数据恢复RDB方式要比AOF方式要高效。RDB缺点是最后一次持久化后的数据可能丢失。

默认的就是RDB，不需修改配置redis.conf

rdb保存的文件是  dump.rdb
Redis 提供了三个条件内存持久化
save 900 1        (900s，即15min内有1个更改)
save 300 10       (300s，即5min内有10个更改)
save 60 10000     (60s， 即1min内有1W个更改)

指定本地数据库文件名，默认为dump.rdb
dbfilename dump.rdb

指定本地数据库存放目录
dir ./
```

> 触发机制
>
> 1、配置文件中save的规则满足情况下，会自动触发rdb规则
>
> 2、执行 flushall命令，也会触发rdb规则
>
> 3、shutdown退出redis，也会产生rdb文件



**如何恢复rdb文件**

1、只需将rdb文件放入到redis.conf配置的文件目录下

2、命令行查看文件的位置

```php
127.0.0.1:6379> CONFIG GET dir
1) "dir"
2) "/usr/local/redis/var"    #数据存放的目录
```

```
优点：
1、适合大规模的数据恢复！
2、对数据的完整性要求不高！

缺点：
1、需要一定时间间隔操作，如果redis意外宕机，可能有数据丢失
2、fork进程的时候，会占用一定的内存空间
```



**6.2.1 持久化之AOF操作**

> 是什么? Append  Only File

```
以日志的形式记录每个写操作，将Redis执行过的所有指令记录下来(读操作不记录)，只许追加文件但不可以改写文件，redis启动之初会读取文件重新构建数据。

Aof保存的是 appendonly.aof文件
```

```bash
#redis.conf配置文件中默认是不开启的
appendonly no      #将其修改成yes即可开启
appendfilename "appendonly.aof"

appendfsync everysec    #每秒写一次

auto-aof-rewrite-percentage 100 #当前AOF大小(即aof_current_size)和上一次重写时AOF大小(aof_base_size)的比值
auto-aof-rewrite-min-size 64mb  #执行AOF重写时，文件的最小体积，默认值为64MB
```

手动配置 appendonly yes

重启 redis就可以生效

如果这个appendonly.aof文件有错位(手动去修改造成错误)，这时候redis是启动不起来的，我们需要修复这个aof文件。

```bash
/usr/local/redis/bin/redis-check-aof
/usr/local/redis/bin/redis-check-rdb

# vi appendonly.aof    //可以查看操作日志

手动修复损坏的日志文件
# /usr/local/redis/bin/redis-check-aof --fix /usr/local/redis/var/appendonly.aof

aof默认是无限追加写入文件的
```

```
优点：
1、每一次修改都同步，文件的完整性会更加好！
2、每秒同步一次，可能会丢失一秒的数据
3、从不同步，效率最高的
缺点：
1、相对于数据文件来说，aof远远大于rdb，修复的速度也比rdb慢
2、AOF运行效率也要比rdb慢
```

###### 6.3 Redis发布订阅

Redis发布订阅是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。微信、微博、关注系统！

Redis客户端可以订阅任意数量的频道。

<img src="H:\笔记本\Redis.assets\image-20200511215356419.png" alt="image-20200511215356419" style="zoom:75%;float:left;" />

```bash
#基本命令
1、PSUBSCRIBE pattern [pattern...]   #订阅一个或多个符合给定模式的频道

2、PUBSUB subcommand [argument...]   #查看订阅与发布系统状态

3、PUBLISH channel message           #将信息发送到指定频道


```





##### 第九章 Redis复习

###### 9.1 知识要点

1. 非关系型的键值对数据库，可以根据键以0(1)的时间复杂度取出或插入关联值
2. Redis的数据是存在内存中的
3. 键值对中键的类型可以是字符串，整型，浮点型等，且键是唯一的
4. 键值对中的值类型可以是string, hash ,list, set, sorted set等
5. Redis内置了复制，磁盘持久化，LUA脚本，事务，SSL,客 户端代理等功能
6. 通过Redis哨兵和自动分区提供高可用性



访问延迟时间

<img src=".\Redis.assets\image-20200724145229507.png" alt="image-20200724145229507" style="float:left;" />



**应用场景**

- 计数器

可以对String进行自增自减运算，从而实现计数器功能。Redis这种内存型数据库的读写性能非常高，很适合存储频繁读写的计数量

- 分布式ID生成

利用自增特性，一次请求一个大一点的步长如incr2000,缓存在本地使用，用完再请求。

- 海量数据统计

位图(bitmap) :存储是否参过某次活动，是否已读谋篇文章，用户是否为会员，日活统计。

- 会话缓存

可以使用Redis来统一存储多台应用服务器的会话信息。当应用服务器不再存储用户的会话信息，也就不再具有状态，一个用户可以请求任意一个应用服务器，从而更容易实现高可用性以及可伸缩性

- 分布式队列/阻塞队列

List是一个双向链表，可以通过Ipush/rpush和rpoplpop写入和读取消息。可以通过使用brpop/blpop来实现阻塞队列

- 分布式锁实现

在分布式场景下，无法使用基于进程的锁来对多个节点上的进程进行同步。可以使用Redis自带的SETNX命令实现分布式锁

- 热点数据存储

最新评论，最新文章列表，使用list存储,trim取出热点数据，删除老数据

- 社交类需求

Set可以实现交集，从而实现共同好友等功能，Set通过求差集，可以进行好友推荐，文章推荐

- 排行榜

ZSet可以实现有序性操作，从而实现排行榜等功能

- 延迟队列

使用sorted_ set，使用[ 当前时间戳 + 需要延迟的时长]做score，消息内容作为元素，调用zadd来生产消息，消费者使用zrangbyscore获取当前时间之前的数据做轮询处理。消费完再删除任务remkey member

Redis：实现语言

~~~c
//C语言
char data[] = "chenglh\0";
//C语言的特性 \0表示字符串结束，假如客户端刚好传入 \0 会被截断

char data[] = "cheng'\0'lh'\0'";//cheng

//所以redis 自定义了一个类型  sds : simple dynamic string
sds :
    len：数据长度
    free：剩余空间
    char buf[] = {1,1,\0,2,3,342,32} 根据长度来读取
    //1、所以sds 二进制安全数据结构
    //2、动态扩容机制，避免了频繁的内存分配

    假如：值 aaa -> aaaa
        len  3
        free 3
        char buf   -> 6
    如果修改后的字符串长度 < len + free，就不用重新申请内存空间，直接在原来位置修改
    
    下次扩容  6X2，6*2*2 ...2倍的来扩容，但是到了1024M，每次扩容都是1MB
        
    
~~~





 

