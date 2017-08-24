---
layout: post
title: golang cross compile
category: golang
comments: true
---

#go1.5以前的版本的交叉编译

前言：

go1.5以前版本的交叉编译，无法实现直接交叉编译，需要先配置构建好要目标版本环境，go1.5以后则无需再构建环境了，直接可以编译，内部会自动构建，所以第一次会比较慢。

##一、各平台的GOOS和GOARCH参考

OS ARCH OS version

```
linux 386 / amd64 / arm >= Linux 2.6
darwin 386 / amd64 OS X (Snow Leopard + Lion)
freebsd 386 / amd64 >= FreeBSD 7
windows 386 / amd64 >= Windows 2000
```

##二、linux 下的交叉编译

```
$ cd /usr/local/go/src 
$ sudo CGO_ENABLED=0 GOOS=linux GOARCH=amd64 ./make.bash
```

这里并不是重新编译Go，因为安装Go的时候，只是编译了本地系统需要的东西；而需要跨平台交叉编译，需要在Go中增加对其他平台的支持。所以，有 ./make.bash 这么一个过程。

执行结果类似如下：

```
sudo CGO_ENABLED=0 GOOS=freebsd GOARCH=amd64 ./make.bash 
Password:
Building C bootstrap tool.

cmd/dist
Building compilers and Go bootstrap tool for host, darwin/amd64.

lib9 
libbio 
libmach 
misc/pprof 
cmd/addr2line 
cmd/cov 
cmd/nm 
cmd/objdump 
cmd/pack 
cmd/prof 
cmd/cc 
…… 
pkg/text/template/parse 
pkg/text/template 
pkg/go/doc 
pkg/go/build 
cmd/go 
pkg/runtime (linux/amd64)
Building packages and commands for host, darwin/amd64.

runtime 
errors 
sync/atomic 
unicode 
unicode/utf8 
math 
sync 
unicode/utf16 
crypto/subtle 
io 
syscall 
………. 
net/rpc/jsonrpc 
testing/iotest 
testing/quick
Building packages and commands for linux/amd64.

runtime 
errors 
sync/atomic 
unicode 
unicode/utf8 
math 
sync 
unicode/utf16 
…….. 
testing 
net/rpc/jsonrpc 
testing/iotest 
testing/quick
Installed Go for linux/amd64 in /usr/local/go 
Installed commands in /usr/local/go/bin
```

开始交叉编译对应平台下的执行文件 
到源代码目录下执行：

```
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build sina.go
```

不带前面参数的 go build 只是编译出开发环境适用的执行文件。

注意：有C代码不能直接交叉编译，需要真实环境编译，现在go1.5说是移除C，只是编译器、代码库从C转GO，对于C库的编译，还得需要CGO，只是除了C代码的其他部分都不需要C编译器了。

##三、windows下的交叉编译：

比如编译一个linux-arm版本的出来。

首先，把arm 环境build出来，准备 go环境 for arm，然后进入golang源码目录/src

```
set GOOS=linux
set GOARCH=arm
set GOARM=7
make.bat --no-clean 2>&1
```

*note：

1.  go env  可以看到go执行环境

2.  要编译project 的时候，windows下，打开命令行窗口，进入项目当前路径，执行 set GOPATH=%CD%  可以设定GOPATH这很重要（方便）

3.  liteide 是为go 准备的ide，还不错。

4.  如果你用到的package 需要编译c 代码，而跨平台编译不一定行得通。这个时候你可以现在对应平台上把package编译好（使用虚拟机），然后把package 静态文件copy 过来，到windows下完成整个程序最后的link 工作。


##四、fessbsd下的交叉编译

设置GOPATH、GOROOT

```
setenv GOPATH /root/.gopkg
echo "setenv GOPATH /root/.gopkg" >> ~/.cshrc

go build xxxx.go
```
