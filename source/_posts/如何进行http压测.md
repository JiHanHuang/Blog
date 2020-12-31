---
title: 如何进行http压测
date: 2020-12-25 18:09:41
categories: 技术杂谈
tags:
---

测试新手上线 (•̀⌄•́)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　——　by JiHan
* * *

<!-- more -->


## 如何进行http压测

### 准备
首先要准备2台设备，客户端，压测设备。如果是网关类型，需要增加后面的测试桩。
其次，我们需要选择压测工具，这里我使用的是`wrk`，由于io复用能在单机上产生很高的并发量。

### 目的
压测目的：
1. 了解自身项目在某种环境下的最大承受业务。
2. 进行性能问题排查和优化。

**TODO：**
1. 选定固定的运行环境(网络，运行系统，平台，压测工具，客户端)
2. 准备基准测试项目和待测试项目
3. 进行基准测试，并确定环境配置正常
4. 进行待测试项目测试
5. 分析总结

### 基准
开始测试之前，我们需要测试对标的基准。通常可以在相应服务器上搭建一个nginx简单服务，进行压测得到基准参考数据。当然也可以找官方压测数据。
示例：
`wrk -t 16 -c 1000 -d 60s --script=proxy_http.lua --latency http://192.168.1.11:5556/test10k.html`
```
Running 1m test @ http://192.168.1.11:5556/test0k.html
  16 threads and 1000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     6.69ms    6.67ms 430.69ms   99.61%
    Req/Sec     9.52k   825.25    25.95k    91.53%
  Latency Distribution
     50%    6.39ms
     75%    6.88ms
     90%    7.45ms
     99%   10.20ms
  9100687 requests in 1.00m, 2.13GB read
Requests/sec: 151455.19
Transfer/sec:     36.25MB
```
基准测试一般都需要考虑到平台环境和网络环境，测试出的结果是否是正常值。如果有较大偏差，则需要考虑是否有其他因素影响测试结果。
**影响测试结果的一些情况：**
1. linux文件/socket数目限制
   可以通过以下配置查看
    ```
    ubuntu:~$ ulimit -a
    core file size          (blocks, -c) 0
    data seg size           (kbytes, -d) unlimited
    scheduling priority             (-e) 20
    file size               (blocks, -f) unlimited
    pending signals                 (-i) 16382
    max locked memory       (kbytes, -l) 64
    max memory size         (kbytes, -m) unlimited
    open files                      (-n) 1024  #打开文件数目限制
    pipe size            (512 bytes, -p) 8
    POSIX message queues     (bytes, -q) 819200
    real-time priority              (-r) 0
    stack size              (kbytes, -s) 8192
    cpu time               (seconds, -t) unlimited
    max user processes              (-u) unlimited
    virtual memory          (kbytes, -v) unlimited
    file locks                      (-x) unlimited
    ```
    修改打开文件数目限制(临时)：`ulimit -n 2048`
    永久设置方法([参考](https://blog.csdn.net/fdipzone/article/details/34588803))：
    >`vim /etc/security/limits.conf`在最后加入:
    >```
    >* soft nofile 4096
    >* hard nofile 4096
    >```
    >最前的 * 表示所有用户，可根据需要设置某一用户，例如:
    >```
    >fdipzone soft nofile 8192
    >fdipzone hard nofile 8192
    >```
    >改完后注销一下就能生效。

    查看某一进程打开进程数：`lsof -p pid | wc -l`
    查看系统打开进程数：`lsof | wc -l`

2. 网络因素
   如果网络有其他人一起在用，可能出现网络波动，导致测试结果波动，或者`wrk`出现timeout。实际测试时，可以考虑单独直连网线。
3. 大量timeout占用socket
   可以通过`ss -ta | grep TIME-WAIT`命令查看是否有大量的time_wait。如果存在大量time_wait可通过配置` /etc/sysctl.conf`文件修改：
   ```
    # Controls the use of TCP syncookies
    net.ipv4.tcp_syncookies = 1

    # The TIME-WAIT sockets for new connections can be reused
    net.ipv4.tcp_tw_reuse = 1

    # Enable fast recycling of TIME-WAIT sockets status
    net.ipv4.tcp_tw_recycle = 1

    # Decrease the time default value for tcp_fin_timeout connection
    net.ipv4.tcp_fin_timeout = 30
   ```
   然后执行 `/sbin/sysctl -p` 让参数生效
4. 系统IO
   大量的系统IO会使程序性能降低很多，当然类似的mysql频繁读写也会(这里不谈)。
   `iotop`查看系统IO情况。
   如果存在一些大量占用IO的情况，可通过`lsof -p <pid>`(或者`iostat `、`pidstat`都能达到相同效果)来确定进程关联的文件，是否有文件在进行大量读写操作。
   如果上述没有找到可以文件，也可以通过`strace -p <pid>`来观察进程和系统的交互，来确定程序在哪里进行了大量IO操作。[strace使用参考](https://www.linuxidc.com/Linux/2018-01/150654.htm)
5. 其他

可根据项目重视的点来测试基准数据，并统计整理。
测试基准数据记录，除了基准的QPS，还要包括内存利用率，CPU利用率，网络带宽利用率，IO利用率这些基本情况。[Linux查看网络流量](https://tlanyan.me/linux-traffic-commands/)

### 压测
首先要**确保环境的一致性**，保证变量的单一性，不论环境，网络，系统配置都需要保持 一致。
简单示例结果：
|	|http+0k	|http+nk	|https+0k	|https+nk	|
|---|---|---|---|---|---|
|基准|	QPS:151455.19<br>CPU:93.6%<br>NET:125MB/s<br>IO:100KB/s<br>MEM:20%	|...	|...	|...	|
|project|	QPS:142234.19<br>CPU:97.8%<br>NET:122MB/s<br>IO:128KB/s<br>MEM:40%	|...	|...	|...	|
|结论|	...	|...	|...	|...	|