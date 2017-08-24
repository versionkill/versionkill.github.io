---
layout: post
title: udp broadcast
category: tools
comments: true
---


1、首先发送端广播数据包，可以使用 lsof -i：8888 或 netstat -su 来查看udp端口有没有正常启用

2、用tcpdump等抓包工具检查广播包有没有发送出去，没有发送出去的原因有几个可能：

     1、首先查看当前网卡是否支持广播或多播

         #ifconfig

         #UP BROADCAST RUNNING MULTICAST MTU：1500 Metric：1 

         metric：1说明是支持的

     2、系统缓冲区满了，不足以发送数据包需要修改缓冲区大小

         操作：sysctl -a|grep net.core          #检查缓冲大小情况（rmen_max,wmen_max,rmen_default,...）

         sysctl -w net.core.rmen_max=50000      #修改

         sysctl -w net.core.rmen_default=50000  

         sysctl -w net.core.wmen_max=50000

         sysctl -w net.core.wmen_default=50000

     3、防火墙禁用udp端口服务

         /etc/init.d/iptables status 查看防火墙状态

         一般这个问题的话，发送时会有明显的错误提示，提示这个udp端口不允许用

         可以相应去修改防火墙规则/etc/sysconfig/iptables

     4、Route禁用udp，禁止发送接收udp数据包

         修改路由设置

     *5、网络参数设置问题

        1、bcast的设置问题，如果发送的直接广播地址是10.255.255.255，bcast也要改成10.255.255.255。

        2 、接收端不需要做特殊处理，但需要加10.0.0.0的gateway，用netstat -rn命令去查看下，有自动分配好则不需要

        设置，一般还需要设置下244.0.0.0，即route add -net 224.0.0.0 netmask 240.0.0.0 dev eth0。

        3 、现在那边的server设置是 ip：10.0.0.1 bcast：10.255.255.255 mask：255.0.0.0 ，udpServer直接广播地址也是
        10.255.255.255所以发送没问题，这里其实应该叫多播或者组播了，多播需要用到路由，但client端是10.0.0.10，从10.0.0.1
        过来的广播包需要用到gateway才行，所以1发的，10没法收到，如果直接广播地址是10.0.0.255，那么就 10是可以收到的

     6、另外发送不出还有可能是当前系统存在虚拟网卡的原因，不存在虚拟网卡就忽略这个问题



3、除了在发送端没发出去的问题外，还有udp本身丢包问题

     1、缓冲区大小不够导致丢包，修改方法如2.1

     2、数据包较大发送又较快，导致接收端还没处理完，就断开连接了，修改接受缓存大小，最好进行分包处理，加个处理等待时间或

        使用独立的处理线程。

     3、网络问题，网络不好也会导致丢包



4、丢包问题测试

```

iperf测试

TCP

   server：iperf -s -i 1 -w 1M

   client: iperf -c [host] -i 1 -w 1M


UDP

   server :iperf -u -s

   client :iperf -u -c [host] -b 900m -i 1 -w 1M -t 60

```
更正：

```
我理解错了，应该还是广播，A类10.0.0.0的默认子码是255.0.0.0广播地址是10.255.255.255，ABC类主机地址全1为广播地址，这也
是A类的最小广播地址范围，所以如果10.0.0.255去广播的话就不对了，而C类的广播地址192.168.6.255，所以只要直接广播地址比设
置的广播地址范围小即可，也就是之前发不出的原因就是因为是A类广播，所以udpServer直接广播地址应该是10.255.255.255，之前我
理解为组播心里就有这个疑问的，就是C类地址的广播地址可以随意却不影响广播发出去，只所以理解为组播是因为我不添加gateway就发
不出去以为是因为路由的关系，其实并不是，所以那边并没有添加224.0.0.0就可以发出去了，因為一般Router／Firewall／Gateway會
是同一個Device，现在说的通了，组播范围是D类地址224.0.0.0~239.255.255.255，

之前不知道从哪边看到的内容理解错了
```

