

> 初始化项目

~~~go
go mod init www.jthink.com
~~~



###### 1.1 手撸IoC容器初级版

1、如果项目对性能要求高，那么在运行时，要尽可能的避免过多反射；

2、如果追求业务的封装和维护(如项目后台)，消耗些性能，但方便开发与维护。



IOC容器，Inversion of Control控制反转。

例如：UserService 服务里有方法GetUserInfo(id int)； OrderService服务里有 GetOrderInfo(id int)

~~~go
//1、创建 services/UserService.go
package services

import "fmt"

type UserService struct {//光标定位到结构体名上，右键generate>constructer即可自动生成构造函数NewUserService（）

}

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

type OrderService struct {

}

func NewOrderService() *OrderService {
	return &OrderService{}
}

func (this *UserService) GetOrderInfo(id int) {
	fmt.Printf("获取订单ID=%s的详细信息", uid)
}
~~~

调用服务接口类

~~~go

~~~

