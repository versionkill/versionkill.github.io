---
layout: post
title: golang 并发
tags: go
comments: true
---

### goroutine并发

* waitgropup
* channel
* select

### runtime包中有几个处理goroutine的函数


* Goexit

退出当前执行的goroutine，但是defer函数还会继续调用

* Gosched

让出当前goroutine的执行权限，调度器安排其他等待的任务运行，并在下次某个时候从该位置恢复执行。

* NumCPU

返回 CPU 核数量

* NumGoroutine

返回正在执行和排队的任务总数

* GOMAXPROCS

用来设置可以并行计算的CPU核数的最大值，并返回之前的值。

### 初探waitgroup

WaitGroup 用于线程同步，即等待一组线程执行完毕，才会继续往下执行，而用time.Sleep()给出一个预计结束时间来等待线程结束就显示很不人性化，用WaitGroup就可以轻松解决这个问题。

WaitGroup总共有三个方法：Add(delta int),Done(),Wait()。简单的说一下这三个方法的作用。

Add:添加或者减少等待goroutine的数量。

Done:相当于Add(-1)。

Wait:执行阻塞，直到所有的WaitGroup数量变成0。

例子：

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup

	for i := 0; i < 5; i = i + 1 {
		wg.Add(1)
		go func(n int) {
			// defer wg.Done()
			defer wg.Add(-1)
			EchoNumber(n)
		}(i)
	}

	wg.Wait()
}

func EchoNumber(i int) {
	time.Sleep(3e9)
	fmt.Println(i)
}
```

上面的结果是无法预知的，输出基本上无序，因为

### 新发现

如果给程序开始加一个

```go
    runtime.GOMAXPROCS(1)
```

那么结果又是如何呢？

```go
package main

import (
	"fmt"
	"sync"
	"runtime"
)

func main() {
	runtime.GOMAXPROCS(1)
	var wg sync.WaitGroup

	for i := 0; i < 5; i = i + 1 {
		wg.Add(1)
		go func(n int) {
			// defer wg.Done()
			defer wg.Add(-1)
			EchoNumber(n)
		}(i)
	}

	wg.Wait()
}

func EchoNumber(i int) {
	fmt.Println(i)
}
```

输出：

```
4
0
1
2
3
```

结果总是先输出最后一个线程，然后才有序地输出其他线程（前面所有线程），因为设置了程序运行使用最大CPU数量为1，所以有序也很容易理解，
但是为什么总是先输出最后一个线程？？？

接下来试着不用waitgroup，开启多个groutine后，用sleep来等待所有线程结束:

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func main() {
	runtime.GOMAXPROCS(1)

	for i := 0; i < 5; i = i + 1 {
		go func(n int) {
			EchoNumber(n)
		}(i)
	}

    time.Sleep(1e9)
}

func EchoNumber(i int) {
	fmt.Println(i)
}
```

结果：

```
0
1
2
3
4
```

结果输出总是按照顺序输出线程结果，可以看出waitgroup添加线程会先阻塞这些线程，添加到最后一个线程，最后一个线程并没有去阻塞，而是让其运行，然后才逐个运行其他线程，所以会出现总是先运行最后一个添加的线程，然后才有序的执行之前添加的线程，而用sleep去做主动等待时，只是单纯地运行多个groutine，相当于单线程而已，所以在执行同样复杂性同样等待时间的function时，会有序地输出。


### channel

goroutine 运行在相同的地址，因此访问共享内存必须做好同步，go提供了很好的通讯机制channel，可以实现groutine之间的数据通讯。

channel和map一样，使用前必须先make，否则初始为nil，在一个nil的channel上发送和接收操作会被永久阻塞。

```go
package main

import "fmt"

func sum(a []int, c chan int) {
    total := 0
    for _, v := range a {
        total += v
    }
    c <- total  // send total to c
}

func main() {
    a := []int{7, 2, 8, -9, 4, 0}

    c := make(chan int)
    go sum(a[:len(a)/2], c)
    go sum(a[len(a)/2:], c)
    x, y := <-c, <-c  // receive from c

    fmt.Println(x, y, x + y)
}
```

結果：

```
-5 17 12
```

值得注意的是，chan是可以指定缓冲大小的，例如上方的程序，如果开启的两个goroutine，改变下，开启一个或者都不开，而又不给chan指定缓冲大小，那么结果又如何呢？

修改：
```go
    sum(a[:len(a)/2], c)
    go sum(a[len(a)/2:], c)
```

结果：

```
fatal error: all goroutines are asleep - deadlock!
```

结果是出现异常了，因为没有分配缓冲区，所有chan处理完单线程的sum后已经返回了，也就是close了，这时候再去发送或者接收就会出现异常，而如果都是开启的groutine，则不需要缓冲区，因为本身

