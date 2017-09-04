---
layout: post
title: golang waitgroup
tags: go
comments: true
---

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

结果总是先输出最后一个线程，然后才有序地输出其他线程（前面所有线程），因为设置了程序运行使用最大CPU数量为1，所以有序也很容易理解，
但是为什么总是先输出最后一个线程？？？

