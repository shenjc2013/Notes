

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



~~~go
package services

import "fmt"

type OrderService struct {}

func NewOrderService() *OrderService {
	return &OrderService{}
}

func (o *OrderService) GetOrderInfo(id int) {
	fmt.Printf("订单详情，id=%d\n", id)
}
~~~



~~~go
func main() {
	uid := 123

	//正常代码写到这里是没有问题的，但是订单与用户有关
	//services.NewUserService().GetUserInfo(uid)
	//services.NewOrderService().GetOrderInfo(uid)

	userService := services.NewUserService()
	userService.GetUserInfo(uid)
	userService.GetOrderInfo(uid)
}
~~~



反转：

~~~go
package services

import "fmt"

type UserService struct {
	order *OrderService
}

func NewUserService(order *OrderService) *UserService {
	return &UserService{order: order}
}

func (this *UserService) GetUserInfo(uid int) {
	fmt.Printf("用户资料：uid=%d\n", uid)
}

func (this *UserService) GetOrderInfo(uid int) {
	this.order.GetOrderInfo(uid)
}
~~~



~~~go
func main() {
	uid := 123

	//正常代码写到这里是没有问题的，但是订单与用户有关
	//services.NewUserService().GetUserInfo(uid)
	//services.NewOrderService().GetOrderInfo(uid)

	userService := services.NewUserService(services.NewOrderService()) //通过注入方式
	userService.GetUserInfo(uid)
	userService.GetOrderInfo(uid)
}
~~~



听不懂呀

