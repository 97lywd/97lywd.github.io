---
title: Linux环境变量及Shell编程
date: 2019-09-10 22:23:23
tags:
   - Linux
---

## 一.环境变量：
   - ***环境变量是什么***
           环境变量一般是指在操作系统中用来指定操作系统运行环境的一些参数，在系统中具有全局特性。
- ***常见环境变量***
      
      PATH*:指定命令的搜索路径
      HOME*：指定用户的主工作目录
      HISTSIZE*：指保存历史命令的条数
      SHELL*：shell的全路径名
      echo $NAME   //NAME: 环境变量名称
      



<!--more-->

 例如我们现在我们来查看上述常见变量：
   

```
[lywd@localhost ~]$ echo $HOME
/home/lywd
[lywd@localhost ~]$ echo $PATH
/usr/lib64/qt-3.3/bin:/home/lywd/perl5/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/lywd/.local/bin:/home/lywd/bin
[lywd@localhost ~]$ echo $HISTSIZE
1000
[lywd@localhost ~]$ echo $SHELL
/bin/bash

```
- ***与环境变量相关的命令***

> 1.echo: 显示某个环境变量值

> 2.export： 设置一个新的环境变量

> 3.env：显示所有环境变量

> 4.set：显示本地定义的Shell变量和环境变量

>  5.unset：清除环境变量

> 6.readonly：设置只读环境变量


例如我们现在将一个变量设置成为环境变量，并查看环境变量

```
[lywd@localhost ~]$ export myname=Yang
[lywd@localhost ~]$ env

SELINUX_LEVEL_REQUESTED=
myname=Yang
HISTCONTROL=ignoredups
SHLVL=1
HOME=/home/lywd
PERL_LOCAL_LIB_ROOT=:/home/lywd/perl5
LOGNAME=lywd
```
- ***环境变量的组织方式***

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190426185816330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjU0OTI1OQ==,size_16,color_FFFFFF,t_70)
如上图每个程序都会收到一张环境表，环境表是一个字符指针数组，
每个指针指向一个以'\0'结尾的环境字符串。

- 用c代码来获取环境变量

``` c
[lywd@localhost enviro]$ cat test.c
#include<stdio.h>
 int main(int argc, char *argv[],char *env[])
{
  int i = 0;
  for (; env[i]; i++) {
      printf("%s\n",env[i]);
  }
  return 0;
}

[lywd@localhost enviro]$ ./test 
XDG_SESSION_ID=2
HOSTNAME=localhost.localdomain
SELINUX_ROLE_REQUESTED=
TERM=xterm
SHELL=/bin/bash
HISTSIZE=1000
SSH_CLIENT=192.168.116.1 55237 22
PERL5LIB=/home/lywd/perl5/lib/perl5:
SELINUX_USE_CURRENT_RANGE=
QTDIR=/usr/lib64/qt-3.3
OLDPWD=/home/lywd/Workspace
QTINC=/usr/lib64/qt-3.3/include
PERL_MB_OPT=--install_base /home/lywd/perl5
SSH_TTY=/dev/pts/0
QT_GRAPHICSSYSTEM_CHECKED=1
USER=lywd


```
- 通过系统调用获取环境变量

```
[lywd@localhost enviro]$ cat test.c
#include<stdio.h>
#include<stdlib.h>
int main()
{
  printf("%s\n",getenv("PATH"));
  return 0;
}

[lywd@localhost enviro]$ ./test 
/usr/lib64/qt-3.3/bin:/home/lywd/perl5/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/lywd/.local/bin:/home/lywd/bin

```

## **二.Shell编程:**
- Shell中变量的数学运算相关命令：
   
   - expr命令：对整数进行算术运算，在算术表达式中如果出现变量，必须在变量前加$，并且运算符与变量之间要加空格。
   - let命令：将算术表达式跟在let命令后面就可以实现数值的运算。

- 元字符

> =  为变量赋值
> $去除变量值
>                 >输出重定定向
>                 >>输出追加到文件
>                 ~代表家目录

- Shell中的控制语句：
    
    - **if语句：**
          if expression
          then statement
          fi
          例如如下demo：
      

```
[lywd@localhost 0425]$ cat test1.sh
if test -f test.sh
then echo "ordinary file"
else echo"not ordinary file"
fi
[lywd@localhost 0425]$ sh test1.sh
ordinary file

```
- **case语句**

     用法：case $变量名 in模式1）

     命令序列1
 
    ;;

    模式2）

     命令序列2

     ;; 

     *）

     默认执行的命令序列 

     ;; 

     esac 

例如我们实现一个简单的四则运算demo：

```shell
[lywd@localhost 04252]$ cat test.sh 
echo "输入第一个数"
read num1
read -p "输入运算符" str
read -p "输入第二个数" num2
case $str in
  +) let result=$num1+$num2
    echo "Result = $result";;
  -) let result=$num1-$num2
    echo "Result = $result";;
 \ *) let result=$num1*$num2
    echo "Result = $result";;
  /) let result=$num1/$num2
    echo "Result = $result";;
esac
[lywd@localhost 04252]$ sh test.sh 
输入第一个数
1
输入运算符+
输入第二个数2
Result = 3


```

- **for语句**
用法格式如下：
 for var in {list}
 do
    Loop body
    done
    例如我们现在实现一个demo：求1~100的累加和
```shell
[lywd@localhost 0425]$ cat sum.sh 

sum=0;
for ((i=1;i<100;i++))
do
  let "sum+=i"
done
echo "the sum is $sum"
[lywd@localhost 0425]$ sh sum.sh 
the sum is 4950

```
  ***注意这里我们的列表内容用了类似于C语言的循环语句，除此以外，for语句还一种用法比如列表的内容是1~100的奇数那么可以这样表示{1..100..2}这里1指的是起始数值，100为结束数值，2为步长。***
  
  - **while语句**
语法格式如下：
while expression
do
Loop body
done
例如我们实现一个猜数字游戏的demo：

```shell
[lywd@localhost 0425]$ cat guess.sh 
echo "Please input the num(1-10)"
read num
while [ $num != 4 ]
do
  if [ $num -lt 4 ]
  then
    echo "Too Small,Try again"
    read num
  elif [ $num -gt 4 ]
  then
    echo "Too Big,Try again"
    read num
  else
    exit 0
  fi
done
echo "you are right"

[lywd@localhost 0425]$ sh guess.sh 
Please input the num(1-10)
5
Too Big,Try again
4
you are right


```
          
