

> 初始化项目

~~~go
go mod init www.jthink.com
~~~

手撸IoC容器初级版

###### 1.1 正转与反转

1、如果项目对性能要求高，那么在运行时，要尽可能的避免过多反射；

2、如果追求业务的封装和维护(如项目后台)，消耗些性能，但方便开发与维护。



IOC容器，Inversion of Control控制反转。

**正常思维**：例如UserService 服务里有方法GetUserInfo(id int)； OrderService服务里有 GetOrderInfo(id int)

~~~go
//1、创建 services/UserService.go
package services

import "fmt"

type UserService struct {}
//光标定位到结构体名上，右键generate>constructer即可自动生成构造函数NewUserService（）

func NewUserService() *UserService {
	return &UserService{}
}

func (this *UserService) GetUserInfo(uid int) {
	fmt.Printf("获取用户ID=%s的详细信息", uid)
}
~~~

~~~go
//2、创建 services/OrderService.go
package services

import "fmt"

type OrderService struct {}

func NewOrderService() *OrderService {
	return &OrderService{}
}

func (this *UserService) GetOrderInfo(id int) {
	fmt.Printf("获取订单ID=%s的详细信息", uid)
}
~~~

程序调用

~~~go
func main() {
	uid := 123
	//正常代码写到这里是没有问题的，但是订单与用户有关
	services.NewUserService().GetUserInfo(uid)
	services.NewOrderService().GetOrderInfo(uid)
}
~~~



**控制正转** : 程序主动去创建“依赖”  (如订单与用户有关)

~~~go
package services

import "fmt"

type UserService struct {}

func NewUserService() *UserService {
	return &UserService{}
}

func (u *UserService) GetUserInfo(uid int) {
	fmt.Printf("用户资料：uid=%d\n", uid)
}

//因为订单都是与用户有关的
//控制正转：程序主动去创建"依赖"
func (u *UserService) GetOrderInfo(uid int) {
	NewOrderService().GetOrderInfo(uid)
}
~~~

程序调用

~~~go
func main() {
	uid := 123
	userService := services.NewUserService() //用户服务接口
	userService.GetUserInfo(uid)
	userService.GetOrderInfo(uid)
}
~~~



**反转控制** : 被动的接受依赖

~~~go
package services

import "fmt"

type UserService struct {
	order *OrderService
}

func NewUserService(o *OrderService) *UserService {
	return &UserService{order: o}
}

func (this *UserService) GetUserInfo(uid int) {
	fmt.Printf("用户资料：uid=%d\n", uid)
}

func (this *UserService) GetOrderInfo(uid int) {
	this.order.GetOrderInfo(uid)
}
~~~

程序调用

~~~go
func main() {
	uid := 123
	userService := services.NewUserService(services.NewOrderService()) //通过注入方式
	userService.GetUserInfo(uid)
	userService.GetOrderInfo(uid)
}
~~~



需求：以上依赖还是我们手工创建的，需要我们定义一个类来统一创建和管理，这就是IoC容器。



###### 1.2 设置IoC容器雏形

知识要点：基于类型的存储，可以是Map或Slice

**基本设计**

最简单的设计是有个Map

key:reflect.Type

key:reflect.Value

格式如下：

~~~go
type BeanMapper map[reflect.Type]reflect.Value
~~~

~~~go
//如：map[*services.OrderService]&{1.0}  //即结构体与值两部分
~~~

涉及三个角色：用户(main.go) -----》  工厂(BeanFactory.go)  -----》 车间(BeanMapper.go)



```go
// Injector/BeanMapper.go 车间

package Injector

import "reflect"

//定义 BeanMapper容器
type BeanMapper map[reflect.Type]reflect.Value

func (this BeanMapper) add(bean interface{}) { //只能内部调用
	t := reflect.TypeOf(bean)
	if t.Kind() != reflect.Ptr { //暂时只针对指针操作
		panic("require ptr object")
	}
	this[t] = reflect.ValueOf(bean) //*services.OrderService = &{1.0}
}

func (this BeanMapper) get(bean interface{}) reflect.Value {
	t := reflect.TypeOf(bean)
	if v,ok := this[t]; ok {//v,ok := this[t] map查找Key的操作
		return v
	}
	return reflect.Value{}//空v
}
```



~~~go
// Injector/BeanFactory.go 工厂
package Injector

var BeanFactory *BeanFactoryImpl

func init()  {//初始化函数
	BeanFactory = NewBeanFactory()
}

type BeanFactoryImpl struct {
	beanMapper BeanMapper
}

func NewBeanFactory() *BeanFactoryImpl {
	return &BeanFactoryImpl{beanMapper:make(BeanMapper)}
}

func (this *BeanFactoryImpl) Set(vlist ...interface{}) {
	if vlist == nil || len(vlist) == 0 {
		return
	}
	for _,v := range vlist {
		this.beanMapper.add(v)
	}
}

func (this *BeanFactoryImpl) Get(vl interface{}) interface{} {
	if vl == nil {
		return nil
	}
	getV := this.beanMapper.get(vl)
	if getV.IsValid() {
		return getV.Interface()
	}
	return nil
}
~~~



程序调用

~~~go
import (
	. "www.test.com/Injector"
	"www.test.com/services"
)

func main() {
	//BeanFactory.Set(&services.OrderService{}) //&services.OrderService{Version:""}
	BeanFactory.Set(services.NewOrderService())
	BeanFactory.Get((*services.OrderService)(nil))
	//&services.OrderService{Version:"1.0"}
	//fmt.Printf("%#v", order)
}
~~~

