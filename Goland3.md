#### day08



多个协程去操作全局变量，会出现问题

~~~go
var wg sync.WaitGroup

var x = 0

func Sum()  {
	for iNum :=0 ; iNum < 5000; iNum++ {
		x = x + 1
	}
	wg.Done()
}

func main()  {
	wg.Add(2)
	go Sum() //开两个协程去跑Sum去个性全局变量 x ,当其中一个协程执行完即 执行wg.Done
	go Sum() //刚好同时拿到同一个全局变量x值，只加一次，即这会加少一次1
	wg.Wait()

	fmt.Println(x) //所以打印出的结果是不确定的
}
~~~



解决办法：加锁

~~~go
var x = 0
var wg sync.WaitGroup
var lock sync.Mutex

func Sum()  {
	for iNum := 0; iNum < 5000; iNum++ {
		lock.Lock() //在修改全局变量时，加解锁操作
		x = x + 1
		lock.Unlock()
	}
	wg.Done()
}

func main()  {
	wg.Add(2)
	go Sum()
	go Sum()
	wg.Wait()

	fmt.Println(x)
}
~~~





















#### 第十天笔记



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

   \>  code.xiangmu.com

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



##### 10.5 NSQ





































