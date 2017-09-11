---
layout: post
title: golang 官网被墙解决方法
tags: go
comments: true
---


### 1. 修改hosts文件

也是一种翻墙手段，需要经常修改，不稳定。

找到hosts文件，Mac OS X/*nix在/etc/hosts，Windows在C:\WINDOWS\system32\drivers\etc\hosts
增加一行

```
173.194.75.141 golang.org
```

### 2、本地启动godoc服务

```
godoc -http=:6060
go get golang.org/x/net/proxy
```

### 3、翻墙

VPN、第三方软件、代理，任由选择（浏览器翻墙插件适合浏览，对于console操作等没什么帮助）

### 4、替代

http://golangtc.com/download/package 输入第三方包，再下载，免去翻墙步骤

### 5、git clone 建立软连接

```
git clone git://github.com/webpy/webpy.git
ln -s    `pwd`/webpy/web
```
