#### 第九天笔记

#### 第十天笔记

##### 10.1 复习

##### 10.2 作业

##### 10.3 数据库操作

###### 10.3.1 MySQL操作

Go语言中的`database/sql`包提供了保证SQL或类SQL数据库的泛用接口，并不提供具体的数据库驱动。
使用`database/sql`包时必须注入（至少）一个数据库驱动。



下载完整的第三方实现，例如：MySQL驱动

~~~go
https://github.com/go-sql-driver/mysql
~~~



> 下载依赖

~~~go
go get -u github.com/go-sql-driver/mysql
~~~

默认是保存到 $GOPATH/src目录下

src  >

   \>  code.xiangmu.com 项目目录

   \>  github.com



> 使用驱动

~~~go
func Open(driverName, dataSourceName string) (*DB, error)
~~~

Open打开一个dirverName指定的数据库，dataSourceName指定数据源，一般至少包括数据库文件名和其它连接必要的信息。



**连接MySQL**

~~~go
import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

func main()  {
  //Go连接MySQL数据库示例
	//DNS
	dsn := "root:12345678@tcp(127.0.0.1:3306)/thinkphp6"
  //连接数据库
	db,err := sql.Open("mysql", dsn)
	if err != nil {
		panic(err)
	}
	fmt.Println("连接成功")

	defer db.Close() //注意这行代码要写在上面的err判断的下面
  //
}

~~~



**思考题**： 为什么上面代码中的`defer db.Close()`语句不应该写在`if err != nil`的前面呢？



> 初始化连接

Open函数可能只是验证其参数格式是否正确，实际上并不创建与数据库的连接。

如果要检查数据源的名称是否真实有效，应该调用Ping方法。

~~~go
import (
	"database/sql"
	"fmt"
  _ "github.com/go-sql-driver/mysql" // init()
)

// 定义一个全局对象db
var db *sql.DB //是一个连接池对象

// 定义一个初始化数据库的函数
func initDB() (err error)  {
	// DSN:Data Source Name
	dsn := "root:12345678@tcp(127.0.0.1:3306)/thinkphp6?charset=utf8mb4&parseTime=True"
	// 不会校验账号密码是否正确
	// 注意！！！这里不要使用 :=，我们是给全局变量赋值，然后在main函数中使用全局变量db
	db, err = sql.Open("mysql", dsn)
	if err != nil {
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {
		return err
	}

	return nil
}

func main()  {
	err := initDB() // 调用初始化数据库的函数
	if err != nil {
		fmt.Printf("init db failed,err:%v\n", err)
		return
	}
}
~~~



如何找到对应驱动的：

~~~go
import (
	"database/sql" //解释一
	_ "github.com/go-sql-driver/mysql" //解释二
)

//解释一
// 代码位置：/src/database/sql/driver/sql.go
func Register(name string, driver driver.Driver) {
	driversMu.Lock()
	defer driversMu.Unlock()
	if driver == nil {
		panic("sql: Register driver is nil")
	}
	if _, dup := drivers[name]; dup {
		panic("sql: Register called twice for driver " + name)
	}
	drivers[name] = driver
}

//解释二
//匿名引入mysql驱动类，但是不使用里边的方法
//只使用 其 init()初始化函数
func init() {
	sql.Register("mysql", &MySQLDriver{})
}
~~~



其中**sql.DB**是表示连接的数据库对象（结构体实例），它保存了连接数据库相关的所有信息。
**它内部维护着一个具有零到多个底层连接的连接池，它可以安全地被多个goroutine同时使用。**

即go中的sql是原生支持连接池，并发安全，可以直接使用。



> SetMaxOpenConns

~~~go
func (db *DB) SetMaxOpenConns(n int)
~~~

`SetMaxOpenConns`设置与数据库建立连接的最大数目。 
如果n大于0且小于最大闲置连接数，会将最大闲置连接数减小到匹配最大开启连接数的限制。 

如果n<=0，不会限制最大开启连接数，默认为0（无限制）。



> SetMaxIdleConns

~~~go
func (db *DB) SetMaxIdleConns(n int)
~~~

SetMaxIdleConns设置连接池中的最大闲置连接数。
如果n大于最大开启连接数，则新的最大闲置连接数会减小到匹配最大开启连接数的限制。 如果n<=0，不会保留闲置连接。



举个例子：

~~~go
func initDB() (err error)  {
	// DSN:Data Source Name
	dsn := "root:12345678@tcp(127.0.0.1:3306)/thinkphp6?charset=utf8mb4&parseTime=True"
	// 不会校验账号密码是否正确
	// 注意！！！这里不要使用 :=，我们是给全局变量赋值，然后在main函数中使用全局变量db
	db, err = sql.Open("mysql", dsn)
	if err != nil {
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {
		return err
	}
  
  //设置数据库连接池中最大连接数
  db.SetMaxOpenConns(10) //根据业务调整
  
  //设置数据库最大空闲连接数(某时间段内业务量少，如半夜等，节省资源设置)
  db.SetMaxIdleConns(5) //最空闲的时候就弄成5个连接数
  
	return nil
}
~~~



###### 10.3.2 CURD操作

> 查询记录

未封装程序

~~~go
var db *sql.DB // 是一个连接池对象

// 定义一个初始化数据库的函数
func initDB() (err error)  {
	// DSN:Data Source Name
	dsn := "root:12345678@tcp(127.0.0.1:3306)/thinkphp6?charset=utf8mb4&parseTime=True"
	// 不会校验账号密码是否正确
	// 注意！！！这里不要使用:=，我们是给全局变量赋值，然后在main函数中使用全局变量db
	db, err = sql.Open("mysql", dsn)
	if err != nil {
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {
		return err
	}

	return nil
}

type User struct {
	userId int
	userNickname string
	userRegtime string
}

func main()  {
	err := initDB() // 调用输出化数据库的函数
	if err != nil {
		fmt.Printf("init db failed,err:%v\n", err)
		return
	}

	var u User
	//1、查询单条记录的sql
	sqlStr := `select user_id AS userId,user_nickname AS userNickname,user_regtime AS userRegtime from hx_user where user_id=?`
	//2、执行
	//rowObj := db.QueryRow(sqlStr, 1)
	////3、拿到结果，返回连接资源
	//rowObj.Scan(&u.userId,&u.userNickname,&u.userRegtime)

	//链式操作
	db.QueryRow(sqlStr,1).Scan(&u.userId,&u.userNickname,&u.userRegtime)

	//打印结果
	fmt.Printf("u:%#v", u)
}

//u:main.User{userId:1, userNickname:"张三峰", userRegtime:"2020-08-22T09:52:21Z"}% 
~~~



**查询一条记录**

~~~go
func queryOne(user_id int) {
	var u User
	//1、查询单条记录的sql
	sqlStr := `select user_id AS userId,user_nickname AS userNickname,user_regtime AS userRegtime from hx_user where user_id=?`
	//2、执行
	//rowObj := db.QueryRow(sqlStr, 1)
	////3、拿到结果，返回连接资源
	//rowObj.Scan(&u.userId,&u.userNickname,&u.userRegtime)

	//链式操作
	db.QueryRow(sqlStr,user_id).Scan(&u.userId,&u.userNickname,&u.userRegtime)

	fmt.Printf("u:%#v\n", u)
}
~~~



**查询多条记录**

~~~go
// 查询多条数据示例
func queryMultiRow(user_id int) {
	sqlStr := "select user_id AS userId,user_nickname AS userNickname,user_regtime AS userRegtime from hx_user where user_id > ?"
	rows, err := db.Query(sqlStr, user_id)
	if err != nil {
		fmt.Printf("query failed, err:%v\n", err)
		return
	}
	// 【非常重要：关闭rows释放持有的数据库链接】
	defer rows.Close()

	// 循环读取结果集中的数据
	for rows.Next() {
		var u User
		err := rows.Scan(&u.userId, &u.userNickname, &u.userRegtime)
		if err != nil {
			fmt.Printf("scan failed, err:%v\n", err)
			return
		}
		fmt.Printf("userId:%d userNickname:%s userRegtime:%#v\n", u.userId, u.userNickname, u.userRegtime)
	}
}
~~~



**增加、修改、删除 exec()**

~~~go
func (db *DB) Exec(query string, args ...interface{}) (Result, error)
~~~

Exec执行一次命令（包括查询、删除、更新、插入等），返回的Result是对已执行的SQL命令的总结。参数args表示query中的占位参数。

> 插入数据

~~~go
/**
 * 插入一行记录
 */
func insertRow(name,mobile,regtime string)  {
	sqlStr := `insert into hx_user(user_nickname,user_mobile,user_regtime) values(?,?,?)`
	ret, err := db.Exec(sqlStr, name,mobile,regtime) //执行插入操作
	if err != nil {
		fmt.Println("insert faile,err:",err)
	}
	lastId,err := ret.LastInsertId()//获取最新插入的记录ID
	if err != nil {
		fmt.Println("get lastid faile,err:", err)
		return
	}
	fmt.Println("insert success, id:",lastId)
}

//调用插入
//3、插入一行记录
//var timeObj = time.Now()
//regtime := timeObj.Format("2006-01-02 03:04:05")
//insertRow("范平平","13790925466",regtime)
~~~



> 更新数据

~~~go
func updateRow(mobile string, id int)  {
	sqlStr := `update hx_user set user_mobile=? where user_id=?`
	ret, err := db.Exec(sqlStr, mobile, id) //执行更新操作
	if err != nil {
		fmt.Println("update failed, err:", err)
		return
	}
	total,err := ret.RowsAffected()//操作影响的行数
	if err != nil {
		fmt.Println("update faile,err:",err)
		return
	}
	fmt.Println("update success, affected rows:",total)
}

/**
 * 4、更新数据
 * updateRow("13800138000", 1)
 */
~~~



> 删除数据

~~~go
/**
 * 删除操作
 */
func delete(id int) {
	sqlStr := `delete from hx_user where user_id = ?`
	ret, err := db.Exec(sqlStr, 1) //操作删除操作
	if err != nil {
		fmt.Println("delete failed,err:", err)
		return
	}
	total,err := ret.RowsAffected() //操作影响的行数
	if err != nil {
		fmt.Println("get rowsaffected failed,err:", err)
		return
	}
	fmt.Println("delete success affected rows:",total)
}

/**
 * 5、删除操作
 * delete(1)
 */
~~~



###### 10.3.3 MySQL事务

Go语言中使用以下三个方法实现MySQL中的事务操作。

> 开启事务

~~~go
func (db *DB) Begin() (*Tx, error)
~~~



> 提交事务

~~~go
func (tx *Tx) Commit() error
~~~



> 回滚事务

~~~go
func (tx *Tx) Rollback() error
~~~



举个例子：用户A给用户B转帐 50元

~~~go


~~~



###### 10.3.4 MySQL预处理



##### 10.4 Redis操作

###### 10.4.1 Redis介绍

1、Redis支持的**数据结构**

字符串（strings）、哈希（hashes）、列表（lists）、集合（sets）、带范围查询的排序集合（sorted sets）、位图（bitmaps）、hyperloglogs、带半径查询和流的地理空间索引等数据结构（geospatial indexes）



2、应用场景

- 缓存系统，减轻主数据库（MySQL）的压力。
- 计数场景，比如微博、抖音中的关注数和粉丝数。
- 热门排行榜，需要排序的场景特别适合使用ZSET。
- 利用LIST可以实现队列的功能。



3、docker容器

~~~go
docker run --name redis507 -p 6379:6379 -d redis:5.0.7
~~~

启动一个redis-cli连接上面的redis server：

~~~go
docker run -it --network host --rm redis:5.0.7 redis-cli
~~~



###### 10.4.2 Redis库

下载Redis库：

~~~go
go get github.com/go-redis/redis/v8
~~~



###### 10.4.3 初始化连接

> 单服务器

~~~go
// 声明一个全局的rdb变量
var rdb *redis.Client

// 初始化连接
func initClient() (err error) {
	rdb = redis.NewClient(&redis.Options{
		Addr:     "127.0.0.1:6379",
		Password: "", // no password set
		DB:       0,  // use default DB
	})

	_, err = rdb.Ping().Result()
	if err != nil {
		return err
	}
	return nil
}
~~~



> 哨兵模式

~~~go
func initClient()(err error){
	rdb = redis.NewFailoverClient(&redis.FailoverOptions{
		MasterName:    "master",
		SentinelAddrs: []string{"x.x.x.x:26379", "xx.xx.xx.xx:26379", "xxx.xxx.xxx.xxx:26379"},
	})

	_, err = rdb.Ping().Result()
	if err != nil {
		return err
	}
	return nil
}
~~~



> 集群模式

~~~go
func initClient()(err error){
	rdb = redis.NewClusterClient(&redis.ClusterOptions{
		Addrs: []string{":7000", ":7001", ":7002", ":7003", ":7004", ":7005"},
	})

	_, err = rdb.Ping().Result()
	if err != nil {
		return err
	}
	return nil
}
~~~



###### 10.4.4 基本使用

> set/get

~~~go
func main() {
	err := initRedis()
	if err != nil {
		fmt.Println("redis 连接出错：", err)
	}

	//set/get
	error := rdb.Set("username", "chenglh",time.Second * 60).Err() //60s生命期，0表示永久有效
	if error != nil {
		fmt.Printf("set username failed, err:%v\n", error)
		return
	}

	val,error := rdb.Get("username").Result()
	if error != nil {
		fmt.Printf("get username failed, err:%v\n", error)
		return
	}
	fmt.Printf("username=%v\n", val)
}
~~~



> Zset

~~~go
func main() {
	err := initRedis()
	if err != nil {
		fmt.Println("redis 连接出错：", err)
	}

	zsetKey := "language_rank"
	languages := []redis.Z{
		redis.Z{Score: 90.0, Member: "Php"},
		redis.Z{Score: 98.0, Member: "Golang"},
		redis.Z{Score: 99.0, Member: "C++"},
	}

	// ZADD
	num, err := rdb.ZAdd(zsetKey, languages...).Result()//切片拆分
	if err != nil {
		fmt.Printf("zadd failed, err:%v\n", err)
		return
	}
	fmt.Printf("zadd %d succ.\n", num)

	// 把Golang的分数(热度)加10
	newScore, err := rdb.ZIncrBy(zsetKey, 7, "Golang").Result()
	if err != nil {
		fmt.Printf("zincrby failed, err:%v\n", err)
		return
	}
	fmt.Printf("Golang's score is %f now.\n", newScore)

	//插入元素
	num1, err := rdb.ZAdd(zsetKey, []redis.Z{redis.Z{Score: 95.0, Member: "Java"}}...).Result()
	if err != nil {
		fmt.Printf("zadd failed, err:%v\n", err)
		return
	}
	fmt.Printf("zadd %d succ.\n", num1)//添加成功数量：1

	// 取分数最高的3个
	ret, err := rdb.ZRevRangeWithScores(zsetKey, 0, 2).Result()
	if err != nil {
		fmt.Printf("zrevrange failed, err:%v\n", err)
		return
	}
	for _, z := range ret {
		fmt.Println(z.Member, z.Score)
	}

	// 取95~100分的
	opt := redis.ZRangeBy{
		Min: "95",
		Max: "100",
	}
	ret, err = rdb.ZRangeByScoreWithScores(zsetKey, opt).Result()
	if err != nil {
		fmt.Printf("zrangebyscore failed, err:%v\n", err)
		return
	}
	for _, z := range ret {
		fmt.Println(z.Member, z.Score)
	}
}
~~~



###### 10.4.5 Pipeline

`Pipeline` 主要是一种网络优化。它本质上意味着客户端缓冲一堆命令并一次性将它们发送到服务器。这些命令不能保证在事务中执行。这样做的好处是节省了每个命令的网络往返时间（RTT）。



`Pipeline` 基本示例如下：

```go
pipe := rdb.Pipeline()

incr := pipe.Incr("pipeline_counter")
pipe.Expire("pipeline_counter", time.Hour)

_, err := pipe.Exec()
fmt.Println(incr.Val(), err)
```

上面的代码相当于将以下两个命令一次发给redis server端执行，与不使用`Pipeline`相比能减少一次RTT。

```go
INCR pipeline_counter
EXPIRE pipeline_counts 3600
```



也可以使用`Pipelined`：

```go
var incr *redis.IntCmd
_, err := rdb.Pipelined(func(pipe redis.Pipeliner) error {
	incr = pipe.Incr("pipelined_counter")
	pipe.Expire("pipelined_counter", time.Hour)
	return nil
})
fmt.Println(incr.Val(), err)
```

在某些场景下，当我们有多条命令要执行时，就可以考虑使用pipeline来优化。



###### 10.4.6 事务

Redis是单线程的，因此单个命令始终是原子的，但是来自不同客户端的两个给定命令可以依次执行，例如在它们之间交替执行。但是，`Multi/exec`能够确保在`multi/exec`两个语句之间的命令之间没有其他客户端正在执行命令。



在这种场景我们需要使用`TxPipeline`。`TxPipeline`总体上类似于上面的`Pipeline`，但是它内部会使用`MULTI/EXEC`包裹排队的命令。例如：

```go
pipe := rdb.TxPipeline()

incr := pipe.Incr("tx_pipeline_counter")
pipe.Expire("tx_pipeline_counter", time.Hour)

_, err := pipe.Exec()
fmt.Println(incr.Val(), err)
```

上面代码相当于在一个RTT下执行了下面的redis命令：

```bash
MULTI
INCR pipeline_counter
EXPIRE pipeline_counts 3600
EXEC
```

还有一个与上文类似的`TxPipelined`方法，使用方法如下：

```go
var incr *redis.IntCmd
_, err := rdb.TxPipelined(func(pipe redis.Pipeliner) error {
	incr = pipe.Incr("tx_pipelined_counter")
	pipe.Expire("tx_pipelined_counter", time.Hour)
	return nil
})
fmt.Println(incr.Val(), err)
```



###### 10.4.7 Watch

在某些场景下，我们除了要使用`MULTI/EXEC`命令外，还需要配合使用`WATCH`命令。在用户使用`WATCH`命令监视某个键之后，直到该用户执行`EXEC`命令的这段时间里，如果有其他用户抢先对被监视的键进行了替换、更新、删除等操作，那么当用户尝试执行`EXEC`的时候，事务将失败并返回一个错误，用户可以根据这个错误选择重试事务或者放弃事务。

```go
Watch(fn func(*Tx) error, keys ...string) error
```



Watch方法接收一个函数和一个或多个key作为参数。基本使用示例如下：

```go
// 监视watch_count的值，并在值不变的前提下将其值+1
key := "watch_count"
err = client.Watch(func(tx *redis.Tx) error {
	n, err := tx.Get(key).Int()
	if err != nil && err != redis.Nil {
		return err
	}
	_, err = tx.Pipelined(func(pipe redis.Pipeliner) error {
		pipe.Set(key, n+1, 0)
		return nil
	})
	return err
}, key)
```



最后看一个官方文档中使用GET和SET命令以事务方式递增Key的值的示例：

```go
const routineCount = 100

increment := func(key string) error {
	txf := func(tx *redis.Tx) error {
		// 获得当前值或零值
		n, err := tx.Get(key).Int()
		if err != nil && err != redis.Nil {
			return err
		}

		// 实际操作（乐观锁定中的本地操作）
		n++

		// 仅在监视的Key保持不变的情况下运行
		_, err = tx.Pipelined(func(pipe redis.Pipeliner) error {
			// pipe 处理错误情况
			pipe.Set(key, n, 0)
			return nil
		})
		return err
	}

	for retries := routineCount; retries > 0; retries-- {
		err := rdb.Watch(txf, key)
		if err != redis.TxFailedErr {
			return err
		}
		// 乐观锁丢失
	}
	return errors.New("increment reached maximum number of retries")
}

var wg sync.WaitGroup
wg.Add(routineCount)
for i := 0; i < routineCount; i++ {
	go func() {
		defer wg.Done()

		if err := increment("counter3"); err != nil {
			fmt.Println("increment error:", err)
		}
	}()
}
wg.Wait()

n, err := rdb.Get("counter3").Int()
fmt.Println("ended with", n, err)
```



##### 10.5 NSQ





































