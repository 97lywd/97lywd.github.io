---
title: Linux下的常用工具
date: 2019-09-03 22:41:05
tags:
    - Linux
---
## 一.VIM编辑器的使用规则：
## 0）三种常用模式：命令模式(command mode),插入模式(insert mode),底行模式(last line mode)**
<!--more-->
```
命令模式： 控制屏幕光标的移动，字符的删除，移动复制某区段
        及进入插入模式下，或者到底行模式下。
插入模式：只有在插入模式下，才能做文本输入，按[ESC]键可回到命令模式。
底行模式：文件保存或者退出，也可以进行文件替换，找字符串，列出行号等操作。
```
##  1）**vim的基本操作：**    
 命令模式——————插入模式<font color=#008000>（输入a/i/o）</font>

 插入模式——————命令模式<font color=#008000>（输入ESC）</font>
 
 命令模式——————底行模式<font color=#008000>（输入：）</font>
 
## 2）**vim三种常见模式命令集：**
### &emsp; &emsp; a.插入模式：
 >  按【i】 进入插入模式后，并从当前光标所在位置开始输入文件
 > 按【a】进入插入模式后，并从当前光标所在位置下一个位置开始输入。
 > 按【o】进入插入模式后，从新的一行行首开始输入。

###  &emsp; &emsp; b.命令模式：
 

> 移动光标：  h/j/k/l  键分别控制光标左，下，上，右移一格。
>  &emsp;  &emsp;  &emsp; &emsp;【G】移至行尾。【gg】进入文本开始。

>  删除文字：【x】删除光标所在位置的一个字符。
> &emsp;  &emsp; &emsp;  &emsp;  【X】删除光标所在位置的前面一个字符。
> &emsp;  &emsp; &emsp;  &emsp;  【dd】删除光标所在行。【#dd】从光标所在行开始删除#行

> 复制撤销：【yw】复制光标所在处到字尾的字符到缓冲区。
>  &emsp;  &emsp; &emsp;  &emsp;  【yy】复制光标所在行到缓冲区。 【p】将缓冲区内的字符贴到光标所在位置。
>   &emsp;  &emsp; &emsp;  &emsp;  【u】回到上一个操作。【crtl+r】撤销的恢复。

###  &emsp; &emsp; c.底行模式：
 

> 【set nu】：列出行号。
> 【w】保存文件
> 【q】退出文件。【q!】强制退出。【wq】保存且退出

## 二.gcc/g++编译器的使用：
用法：gcc 【选项】 要编译的文件【选项】【目标文件】
例如我们写一个简单的hello，world程序

```c
[lywd@localhost test]$ cat test.c
#include<stdio.h>
int main()
{
  printf("hello world");
  return 0;
}

```

 在linux下执行如下命令;gcc test.c -o test，并查看可执行文件执行结果
 

```
[lywd@localhost test]$ gcc test.c -o test
[lywd@localhost test]$ ls
test  test.c
[lywd@localhost test]$ ./test
hello world[lywd@localhost test]$ 

```
上述命令将test.c直接编译成一个可执行文件并且改名为test，其实也可以gcc test.c即不指定输出名，则生成a.out文件。
***实质上，上述编译过程是分为四个阶段进行的，即预处理(也称预编译，Preprocessing)、编译(Compilation)、汇编 (Assembly)和连接(Linking)。***

> 预编译：gcc -E test.c -o test.i  在本例中，预处理结果就是将stdio.h 文件中的内容插入到test.c中了。

> 编译：gcc -S test.c -o test.s 预处理之后，可直接对生成的test.i文件编译，生成汇编代码.

> 汇编：gcc -c test.c -o test.o对于上一小节中生成的汇编代码文件test.s，gas汇编器负责将其编译为目标文件.

> 连接:  gcc test.o -o test	gcc连接器是gas提供的，负责将程序的目标文件与所需的所有附加的目标文件连接起来，最终生成可执行文件。附加的目标文件包括静态连接库和动态连接库。 对于上一小节中生成的test.o，将其与Ｃ标准输入输出库进行连接，最终生成程序test
> 执行./test 可以看到hello world

## 三.gdb调试器的使用：

```
gdb 文件名： 进入调试
退出： ctrl+d 或者 quit
调试命令：list <linenum>　显示linenum行周围的程序。
        list <function>   显示函数function周围的源代码。
        r:运行程序。    n：单条执行。 s：进入函数调用。
         break（b）：在b行处设置断点。
         break（function）：在函数function处设置断点。
         info break： 查看断点信息。
         continue：从当前位置开始连续而非单步执行程序。
         delete breakpoints： 删除所有断点。
```
例如刚才的hello world程序 我们加上-g选项 进入调试

```c
[lywd@localhost test]$ gcc -g test.c -o test.a 
[lywd@localhost test]$ ls
test  test.a  test.c
[lywd@localhost test]$ gdb test.a

(gdb) r
Starting program: /home/lywd/Workspace/test/test 
hello world[Inferior 1 (process 8124) exited normally]
Missing separate debuginfos, use: debuginfo-install glibc-2.17-222.el7.x86_64
(gdb) list 2
1	#include<stdio.h>
2	int main()
3	{
4	  printf("hello world");
5	  return 0;
6	}
(gdb) 

```

## 四.Makefile的使用规则：
先看一下makefile的书写规则：
                目标（target)：目标文件1 目标文件2
　　　　gcc -o 欲建立的执行文件 目标文件1 目标文件2
　　　　

```
例如之前的hello world程序，我们就可以这样写
test:test.o
      gcc test.o -o test
```

> 上面的文件test，它依赖着test.o.执行make命令，就会生成test可执行文件。 那么make是如何工作的呢： 

> 1、make会在当前文件夹下找名字叫“Makefile”或“makefile”的文件。

> 2、假设找到，它会找文件里的第一个目标文件（target），在上面的样例中，他会找到“test”这个文件，并把这个文件作为终于的目标文件。

> 3、假设test文件不存在，或是test所依赖的后面的 .o
> 文件的文件改动时间要比test这个文件新，那么，他就会运行后面所定义的命令来生成test这个文件。

> 4、假设test所依赖的.o文件也不存在，那么make会在当前文件里找目标为.o文件的依赖性，假设找到则再依据那一个规则生成.o文件。（这有点像一个堆栈的过程）

> 5、当然，你的C文件和H文件是存在的啦，于是make会生成 .o 文件，然后再用 .o
> 文件生命make的终极任务，也就是运行文件test了。




　　　　 

