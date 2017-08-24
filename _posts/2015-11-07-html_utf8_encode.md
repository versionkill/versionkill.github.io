---
layout: post
title: golang 处理 html UTF-8编码
category: golang
comments: true
---

##golang 处理 html UTF-8编码

常常遇到URl中带%或者十六进制组成的ascii中文字符串中夹带%时，需要直接将其转化为中文时，使用net/url包中的

```
    QueryUnescape 
```

QueryUnescape函数用于将QueryEscape转码的字符串还原。它会把%AB改为字节0xAB，将'+'改为' '。如果有某个%后面未跟两个十六进制数字，本函数会返回错误。

```golang
s2:="%E6%BE%B3%E9%97%A8"
fmt.Println(url.QueryUnescape(s2))

#澳门   //（输出）
```

