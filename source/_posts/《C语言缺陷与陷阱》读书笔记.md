---
title: 《C语言缺陷与陷阱》读书笔记
date: 2020-05-15 17:42:11
categories: 笔记
tags: [C]
---


虽然这本书已经有些古老了，但是还好，C语言相对来说也挺古老，并且近些年的变化还不算大。虽然其中描述的有些问题，现在已经不存在，或者说编译器已经将其禁止，但是依旧有很多可以学到的技巧。(•̀⌄•́)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　——　by JiHan
* * *
*以下内容都是根据书中的章节目录，并且根据自己的缺陷进行记录的。*
*如果有兴趣，推荐先阅读原著，再看笔记。*

<!-- more -->

### 前言
心智模式(mental model)：被解释为‘’人们深植心中，对于周遭世界如何运作的看法和行为‘’。《心灵的新科学》中认为，人们的心智模式决定了人们如何认识周遭世界。《列子》一书中有个典型故事，说有个人遗失了一把斧头，他怀疑是邻居孩子偷的，暗中观察他的行为，怎么看怎么像偷斧子的人；后来他在自己家中找到了遗失的斧头，再碰到邻居的孩子时，怎么看也不像会是偷他斧头的人了。

你是否愿意购买一个返修率很高的厂家所生产的汽车？如果厂家声明他已经做出了改进，你的态度是否会改变？用户为你找出程序中的Bug，你真正损失的是什么？

### 第一章
**词法分析**
词法分析中的“贪心法”：尽可能长的读取字符将其作为一个符号。

### 第二章
**理解函数声明**
`int *a `中a表示为一个int指针，相应的
`(int *)` 表示“int指针”的强制类型转换。
同理：
float (\*h)() 表示h是一个指向返回值为浮点数的函数指针，那么，
(float (\*)()) 表示“指向返回值为浮点数的函数指针”的强制类型转换。
那么：
`( * (void (*) () )0 ) ();`含义是什么？
首先，`(void (*) () )`表示 “返回值为void型的函数指针”的强制转换。而强制转换的对象是0，意味着0这个地址被强制转换为了一个函数指针，而最开始的\*表示指向函数指针所代表的地址，也就是0地址，综合起来：
调用地址为0处的函数。
普通调用方式：
`fun();`
实际上为`(*fun)();`的简写，fun实质还是一个函数指针。

上述定义，我们也可以使用`typedef`来得到：
```c
typedef void (*funcptr) (); 
(* (funcptr)0) ();
```
这里可以说明一下：`typedef`的实际功能就是为一个类型声明一个别名。在第一行代码中，我们可以理解为`funcptr=void(*)()`。那么，在第二行代码中，`funcptr`是个强制转换，只有类型，没有实际声明变量。将`funcptr`进行替换，就能得到实际的类型声明：
`(* (void(*)())0) ();`
书中还提到了第二种关于signal的声明 `void(*signal(int, void(*)(int)))();`等效于：
```c
typedef void (*HANDLER)(int);
HANDLER signal(int, HANDLER);
```
这儿`signal(int, HANDLER)`是一个`HANDLER`变量，先将其带入到`typedef`中：`void (*signal(int, HANDLER))(int);`，再将signal内部HANDLER变量声明`HANDLER=void(*)(int)`进行替换：`void (*signal(int, void(*)(int)))(int);`

### 第三章
**指针与数组**
`int (*ap)[31]`含义是：声明了`*ap`是一个拥有31个整数元素的数组，因此ap就是指向这样一个数组的指针。注意，这里只是声明了一个指针ap，后面的`[31]`是在表示这个指针的类型。更加通俗：假如声明`int A[31]`,那么`A=(*ap)`,即`ap=&A`.所有操作将`A`和`(*ap)`进行等效替换就行了。
```c
char *hello;
char hello[];
```
这两种声明第一种是一个指向char类型的指针，第二种是代表指向一个char数组的指针。明显第一种范围更广。如果使用中都是代表一个char数组指针的时候，二者是等效的，在参数传递中混用编译器也不会报错。主要是看哪种更能表现出自己的意图。例如：
```c
main(int argc, char *argv[]){
}
main(int argc, char **argv){
}
```
两种写法都等效，唯一不同就在第一种更加强调`argv`是某一字符串的起始地址。而通常我们也是更关心某个传入参数，而不是某个参数中的某个字符。

### 第五章
**使用errno检测错误**
用处是检测最后一次系统错误。当调用某个与系统相关的函数，返回的是错误值时，可以调用此函数。(猜测：库函数中应该都会有错误码)
**库函数signal**
函数声明形式：
```c
#include <signal.h>
signal(signal type, handle function);
```
这个函数时非常有用的，特别是针对出现段错误的情况。当出现段错误时，会发出`SIGSEGV`信号给程序，而程序中一开始调用了`signal`后，它将会接收对应信号量并使用相应的`function`来处理。我们在处理函数中使用`backtrace`及相关的函数即可将发生段错误时的函数堆栈信息打印出来，即可追溯到对应的错误函数。
下面是[参考其他人](
https://blog.csdn.net/astrotycoon/article/details/8142588)后进行了相应修改的代码：
```c

#include <stdio.h>
#include <stdlib.h>
#include <stddef.h>
#include <execinfo.h>
#include <signal.h>
#include <unistd.h>
#include <string.h>
 
void dump(int sig)
{
    void* array[128];
    size_t arr_size, i;
    char **strings = NULL;
    signal(SIGSEGV, SIG_IGN);
    //signal(other signal type, SIG_IGN);
    
    arr_size = backtrace (array, sizeof(array)/sizeof(array[0]));
    strings = backtrace_symbols (array, arr_size);
    printf("Signal:[%s], PID:(%d), Stack trace:\n", strsignal(sig), getpid());  
    if (strings) {
        for (i = 0; i < arr_size; i++) {
            printf( "%ld: %s\n", i + 1, strings[i]);
        }
        free (strings);
    }
    raise(sig);
}
void func_c()
{
    char *segement = NULL;
    printf("%s\n",segement);
}
 
void func_b()
{
    func_c();
}
 
void func_a()
{
    func_b();
}
 
int main(int argc, const char *argv[])
{
    if (signal(SIGSEGV, dump) == SIG_ERR)
        perror("can't catch SIGSEGV");
    func_a();
    return 0;
}
```
编译命令：
`gcc -g -rdynamic signal.c -o a.out`
这里需要注意，添加-O2进行优化后，可能导致backtrace_symbols输出不了函数名。其最终结果如下：
```c
Signal:[Segmentation fault], PID:(4248), Stack trace:
1: ./a.out(dump+0x3d) [0x400afa]
2: /lib64/libc.so.6(+0x35270) [0x7f20db113270]
3: /lib64/libc.so.6(+0x86c31) [0x7f20db164c31]
4: /lib64/libc.so.6(_IO_puts+0xc) [0x7f20db14af2c]
5: ./a.out(func_c+0x1c) [0x400bd5]
6: ./a.out(func_b+0xe) [0x400be5]
7: ./a.out(func_a+0xe) [0x400bf5]
8: ./a.out(main+0x38) [0x400c2f]
9: /lib64/libc.so.6(__libc_start_main+0xf5) [0x7f20db0ffc05]
10: ./a.out() [0x4009f9]
Segmentation fault (core dumped)
```
这里可以非常明显的看到，是执行a.out时其中func_c函数出错，而其错误是由于IO输出导致的。简直不要太明显。
如果添加了优化选项，代码结构会改变，不方便定位。
添加-O2选项后的输出：
```c
Signal:[Segmentation fault], PID:(4367), Stack trace:
1: ./a.out(dump+0x2c) [0x400b1c]
2: /lib64/libc.so.6(+0x35270) [0x7fd5b3951270]
3: /lib64/libc.so.6(+0x86c31) [0x7fd5b39a2c31]
4: /lib64/libc.so.6(_IO_puts+0xc) [0x7fd5b3988f2c]
5: ./a.out(main+0x20) [0x4009f0]
6: /lib64/libc.so.6(__libc_start_main+0xf5) [0x7fd5b393dc05]
7: ./a.out() [0x400a2c]
Segmentation fault (core dumped)
```
我们可以把可执行文件编程汇编代码，看看发生了啥
`objdump -d a.out > a.s`
a.s中对应的出错位置就是在[0x4009f0]地址处，而这个地址也实实在在的在main函数中：
![优化后汇编代码](《C陷阱与缺陷》读书笔记/优化后汇编代码.png)
优化过程中删减了中间两层的函数调用。厉害。也正是因为这样，导致在函数跟踪的时候，只能到看到main函数。
而没有优化过的代码，就和原始结构一样：
![原始汇编代码](《C陷阱与缺陷》读书笔记/原始汇编代码.png)

### 第六章

简单一句话概括，define功能就是做宏替换，它啥功能也没有，就是简简单单的字符替换。
### 第七章
主要讲的是移植性问题，虽然C最初开发的目的就是为了可移植性。但是后续不断的发展，导致C上层库出现了偏差，最显而易见的就是windows和linux的C代码兼容性很差，特别是系统调用级别的，基本是两套api了。
其中还讲到了关于内存重复利用问题，也就是realloc函数。主要用途还是用于原有的空间不够，进行扩展。这样是比你free掉之前的空间，然后重新malloc要快。并且realloc时，是可以拷贝数据到新的地方的。当然，realloc的空间小于之前的空间，那么会产生截断。总之，realloc时需要有很多注意事项，如果不是对空间利用率有较高的要求，可以不用使用。
### 第八章
建议，尽量在写代码的时候，多想，特别是结构。代码写完后多检查，测试。
尽量覆盖异常问题，但避免过多的防御式编程。
### 总结
这本书还是相当的老了，很多书中的问题，在现在来看已经不是问题。但是还是有些地方的提出的注意事项，值得参考和学习。加上全书不长，可以快速一看，然后发现自己缺失的点即可。
2020.5.7