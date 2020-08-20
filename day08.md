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
	go Sum()
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

