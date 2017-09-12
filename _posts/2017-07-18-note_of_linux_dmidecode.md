---
layout: post
title: dmidecode 获取server硬件信息
tags: linux
comments: true
---


linux dmidecode命令查看服务器主板、硬盘等硬件信,golang也有对应的包"github.com/dselans/dmidecode"，本质上也是使用了该命令

### 1.查看主板信息
```
dmidecode -t 2
```

### 2.查询内存信息
```
dmidecode -t 16
```

### 3.查看当前内存数和插槽数
```
dmidecode|grep -P -A5 "Memory Device" |grep Size
```

### 4.查看内存条数
```
dmidecode -t 17
```

### 5.查看CPU信息
```
dmidecode -t 4
```

### 6.查看服务器硬盘信息
```
cat /proc/scsi/scsi
```

### 7.dmidecode查看内存速率
```
dmidecode|grep -A16 "Memory Device"|grep 'Speed'
```

### 8.golang 利用 "github.com/dselans/dmidecode" 获取硬件信息

```go

package main

import (
    "fmt" 
    dmidecode "github.com/dselans/dmidecode" 
)

func isMSIServer() {
    dmi := dmidecode.New()

    if err := dmi.Run(); err != nil {
        fmt.Println(err)
    }

    // or you can also search by record type
    byTypeData, byTypeErr := dmi.SearchByType(1)
	if byTypeErr!=nil{
		fmt.Println("SearchByType(1):", byTypeData, byTypeErr )
	}

    pn := byTypeData["Product Name"]
    fmt.Println(pn)   
}

func main(){
	isMSIServer()
}

```
