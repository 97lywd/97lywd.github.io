---
title: Linux下进程的概念
date: 2019-09-08 23:18:11
tags:
      - Linux
      - C
---
## 一.进程的基本概念：
- 站在用户角度：进程就是运行中的程序
- 站在操作系统的角度：进程就是操作系统进行资源分配和调度的一个独立单位

 <!--more-->

## 二.描述进程-PCB(process control bolck):
   - PCB
                
                包含进程的描述信息和控制信息，是进程存在的唯一标志。
- task_struct 
              
              在Linux中描述进程的结构体是 task_struct
              task_struct是Linux内核的一种数据结构，它会被装载到
              RAM里并包含着进程的信息。
- task_struct的内容分类

    ><font color=#008fff>标识符（PID）：描述本进程的唯一标识符，用来区别于其他进程。
         进程状态： 退出信号，退出代码等。
         优先级：相对于其他进程的优先级
         程序计数器：程序中即将被执行的下一个指令的地址。
         内存指针：程序代码和进程相关数据的指针，其他进程共享内存块的指针。
         上下文数据：进程执行时寄存器中的数据。
          I / O状态信息：显示的I/O请求，分配给进程的I/O设备和进程使用的文件列表。
          记账信息：使用的时钟数总和，时间限制，记账号等。
          其他信息：</font>
    
    

 

## 三. 进程的创建以及状态的分类：
   - **查看进程**
   进程的信息可以通过  /proc系统文件查看
```
[lywd@localhost ~]$ ls /proc/
1     1052  11    267  3    390   4886  5296  5613  5856  5930  6027  6305  651   8          dma          kmsg          partitions     tty
10    1054  1134  268  359  391   4891  5364  5618  5872  5932  6033  6416  6516  87         driver       kpagecount    sched_debug    uptime

```
- **创建进程**  

通过系统调用创建进程-初识fork
    
       fork()   操作系统提供的创建进程的接口
       fork 通过复制父进程创建一个新的子进程
           返回值： 在父进程中，返回子进程pid＞0，
                  在子进程中返回0。用户可通过返回值对父子进程进行代码分流。
                
             
             
我们看以下代码用fork创建子进程，并用ret接收返回值，根据返回值的不同对代码进行分流。
```c
[lywd@localhost proc]$ cat fork.c
#include<stdio.h>
#include<sys/types.h>
#include<unistd.h>
int main()
{
  int ret = fork();
  if(ret < 0){
    perror("fork");
    return 1;
  }
  else if(ret == 0){
    //child
    printf("I am child! : %d,ret :%d \n",getpid(),ret);
  }
  else{
    //father
    printf("I am father! : %d,ret ；%d\n",getpid(),ret);
  }
  sleep(1);
  return 0;
}
[lywd@localhost proc]$ ./fork 
I am father : 6953!,ret ；6954
I am child :6954!,ret :0 

```
- **进程的状态**
 Linux下的进程状态： 运行( R)，可中断睡眠(S)，不可中断睡眠(D)，停止(T)，    僵尸(Z)。
 - **进程的查看**
 命令：ps -ef  / ps -aux(两者的输出结果差别不大，但展示风格不同。aux是BSD风格，-ef是System V风格。）
  例如我们现在写一个非常简单的demo：
  

```c
[lywd@localhost pid]$ cat pid.c
#include<stdio.h>
#include<sys/types.h>
#include<unistd.h>
int main()
{
  while(1)
  {
    sleep(1);
  }
  return 0;
}

```
现在我们使用ps -aux＋管道符 来查看进程及其状态：

```
[lywd@localhost ~]$ ps -aux |grep pidd
lywd       6915  0.0  0.0   4208   348 pts/0    S+   11:09   0:00 ./pidd
lywd       6926  0.0  0.2 112704   972 pts/1    R+   11:09   0:00 grep --color=auto pidd

```
- **进程状态的修改：**
先介绍一个命令：kill------发送指定的信号到相应进程。
注意：不指定型号将发送SIGTERM（15）终止指定进程。如果无法终止该程序可用“-KILL” 参数，其发送的信号为SIGKILL(9) ，将强制结束进程。
***kill 一般用法为 kill【参数】【进程号】***
kill -l 列出所有信号名称：

```
[lywd@localhost ~]$ kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX	
```

现在我们修改刚才的进程状态：
先看修改前：![在这里插入图片描述](https://img-blog.csdnimg.cn/20190424232341401.png)

现在我们打开另一个对话并修改进程状态：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190424232506454.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjU0OTI1OQ==,size_16,color_FFFFFF,t_70)

## 四.僵尸进程与孤儿进程：
- **僵尸进程**
  处于僵尸状态的进程
-  危害：资源泄露
-   产生：子进程先于父进程退出。
- 处理：退出父进程
 -  预防： 进程等待
现在我们创建一个僵尸进程的demo：

```c
[lywd@localhost Js]$ cat test.c
#include<stdio.h>
#include<stdlib.h>
int main()
{
  pid_t pid = fork();
  if(pid < 0)
  {
    perror("fork");
    return 1;
  }
  else if(pid > 0)
  {
    sleep(30); //如果是父进程让其等待
  }
  else {
    exit(0); //如果是子进程让其提出
  }
  return 0;
}

```

         
此时我们来看进程的状态：

```
[lywd@localhost Js]$ ps -aux |grep test
lywd       7620  0.0  0.0   4208   348 pts/0    S+   11:47   0:00 ./test
lywd       7621  0.0  0.0      0     0 pts/0    Z+   11:47   0:00 [test] <defunct>

```
Z+表示当前为僵尸进程。

- **孤儿进程**
   父进程先于子进程退出，子进程被称为之孤儿进程，孤儿进程的父进程将变为1号init进程，并且孤儿进程退出，不会产生僵尸进程。
  现在我们创建一个孤儿进程的demo：
  

```c
[lywd@localhost Ge]$ cat test.c
#include<stdio.h>
#include<stdlib.h>
int main()
{
  pid_t pid = 0;
  if(pid < 0)
  {
    perror("fork");
    return 1;
  }
  else if(pid == 0){
    //子进程，让其等待
    sleep(10);
  }
  else{
    //父进程，让其退出
    exit(0);
  }
  return 0;
}

```
现在我们来查看进程状态：

```
[lywd@localhost Js]$ ps -aux |grep test
lywd       8064  0.0  0.0   4208   348 pts/0    S+   12:04   0:00 ./test
lywd       8066  0.0  0.2 112704   968 pts/1    R+   12:04   0:00 grep --color=auto test

```
很显然，未产生僵尸进程。

## 五.进程的优先级：

   查看：  ps -l
  功能：决定cpu资源的优先分布权。
  现在我们用ps -l命令：
  

```
[lywd@localhost ~]$ ps -l
F S   UID    PID   PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 S  1000   6102   6098  0  80   0 - 29186 do_wai pts/0    00:00:00 bash
0 R  1000   8173   6102  0  80   0 - 38300 -      pts/0    00:00:00 ps
```
我们很容易的注意到几个值：

- UID：代表执行者的身份
- PID:  代表进程号
- PPID：父进程的进程号
- PRI：代表可被执行的优先级，值越小越早被执行。
- NI：代表nice值
-    优先级无法直接修改，但是可以通过NI的值进而对优先级做出设置
 -    PRI = PRI + NI 
 -    nice值的范围： -20~19
 
 修改进程优先级的命令：nice renice
 我们对刚才的代码进行测试：在这里我们用renice 但执行成功需要足够的权限
 

```
[root@localhost pid]# renice 5 -p 8321
8321 (process ID) old priority -5, new priority 5
[root@localhost pid]# ps -l
F S   UID    PID   PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0   8315   6102  0  80   0 - 57948 do_wai pts/0    00:00:00 su
4 S     0   8321   8315  0  85   5 - 29182 do_wai pts/0    00:00:00 bash
0 R     0   8570   8321  0  85   5 - 38300 -      pts/0    00:00:00 ps

```
除此之外top命令也可以修改已经存在进程的nice：
 进入top命令后，按r，后输入进程的PID，最后输入新的nice值。

