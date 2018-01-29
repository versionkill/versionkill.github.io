---
layout: post
title: 关于go的一些细节（持续更新）
tags: go
comments: true
---

### 1、关于数组长度

#### 问题重现

```go
package main

import (
	"fmt"
)

func main() {
	s := make([]int, 5)
	s = append(s, 1, 2, 3)
	fmt.Println(s)
}
```
输出结果如何？

```
[0 0 0 0 0 1 2 3]
```

#### 分析原因

原本猜想make一个长度为5的数组，会从是s[0]开始存入，结果会在原有的数组尾部append，这边因为是int型的数组，所以看起来很明显，
但是如果是string，那么就会挖出一个坑，还不容易发现，看起来就像只是多了几个空格。
   
```go
s := make([]int, 5)
fmt.Println(&s[0])
s = append(s, 1, 2, 3)
fmt.Println(&s[0])
```

输出结果：

```
0xc082035d70
0xc0820580f0
```

 试着打印出地址，此时会发现，在append后地址有做了改变，查证后确实是append会重新分配内存，所以会在原数组后尾部append，但事实上
 地址已经发生了变化，前后两者已经不是同一物了。
