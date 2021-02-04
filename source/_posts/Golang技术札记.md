---
title: Golang技术札记
date: 2021-01-25 15:36:18
categories: 技术杂谈
tags: 
---

全当一个适合自己的golang快捷手册(•̀⌄•́)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　——　by JiHan
* * *

<!-- more -->

##### Golang条件编译
可参考：[golang交叉编译和条件编译的实际应用](https://zhuanlan.zhihu.com/p/92235251)

##### 编译test程序
`-c` 参数就能满足：
```
go test -mod vendor -c <your_test.go>
```
编译完成后，可以像`go test`的方式一样使用该可执行程序：
```
go test -mod vendor ipset_test.go -test.run TestDoor
```
等同于
```
./ipset.test -test.run TestDoor
```
[参考](https://www.cnblogs.com/sunsky303/p/11818480.html)