---
layout: post
title: golang net timeout
category: golang
comments: true
---

##golang net timeout（绑定端口时问题）


为了防止程序被多次叫起来，就给程序绑定了端口

```
func Test_soc_port(portNum int) *net.TCPListener {
    port := fmt.Sprintf(":%d", portNum)
    tcpAddr, err := net.ResolveTCPAddr("tcp4", port)
    if err != nil {
        fmt.Println("get tcpaddr failed:", err)
        return nil
    }
    listener, err := net.ListenTCP("tcp", tcpAddr)
    if err != nil {
        fmt.Println("listen tcpaddr failed", err)
        return nil
    }
    return listener
}
```

```
ok := Test_soc_port(53083)
    if ok == nil {
        fmt.Println("this app is running")
        os.Exit(0)
 }
 ```
 
过程中发现listener莫名奇妙地关闭了，检查端口占用等情况无问题后，猜测是net包的五个timeout搞的鬼，因为之前没设置什么时候close，我就在代码
里加了defer ok.Close()  就没问题了

```
    ok := Test_soc_port(53083)
    if ok == nil {
        fmt.Println("this app is running")
        os.Exit(0)
    }
    defer ok.Close()
```
刚才看了下，golang http 库客户端有5个超时设置，一个是 Client 里面的  Timeout，一个是 Client 使用的 Transport 的  ResponseHeaderTimeout，
还有三个是 Transport 内部的 Dialer 的超时设置，Timeout，KeepAlive，Deadline。

通过查看 golang 代码发现，在执行 http 请求之前， 如果 Client.Timeout > 0，会使用 time.AfterFunc 定义一个回掉函数，超时后调用，此函数会取消
正在进行中的请求。

Dialer 的 Timeout 和 Deadline 是连接超时时间，建立连接过程中使用

发送请求，接收响应 分别由两个协程处理。发送请求后，Transport 里的超时时间 ResponseHeaderTimeout 开始计时，因此它指的是等待响应的超时时间。
