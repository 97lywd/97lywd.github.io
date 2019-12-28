---
title: Linux下常见命令以及对权限的理解
date: 2019-08-29 21:49:14
tags:
   - Linux
---
## 一.对目录的操作命令：
       1. ls命令：ls[选项][目录或文件]
          功能：对于目录，列出该目录下的所有子目录及文件。对于文件，
          列出文件名以及其他信息。
     
  eg：Linux下输入命令行ls -i: 列出文件的inode节点信息。
<!--more-->
```
   [lywd@localhost ~]$ ls -i
   75803 Desktop    19003916 Downloads     75786 perl5     50331812 Public     50331813 Videos
   75804 Documents  19003917 Music      35340381 Pictures  35340380 Templates  19837324 Workspace

```
       2.pwd命令：显示用户当前所在的目录
       例如先用ls指令列出当前所有目录，cd指令进入Workspace目录，输入pwd，
       可得到当前的绝对路径
       [lywd@localhost ~]$ ls
       Desktop  Documents  Downloads  Music  perl5  Pictures  Public  
       Templates  Videos  Workspace

       [lywd@localhost ~]$ cd Workspace/
       [lywd@localhost Workspace]$ pwd
       /home/lywd/Workspace
      
3.cd指令：cd[目录命]    ***改变工作目录，将当前工作目录改变到指定目录下***

```
[lywd@localhost ~]$ cd Workspace/
[lywd@localhost Workspace]$ 
```
4.mkdir指令： mkdir[选项] dirname ***在当前目录下创建一个名为“dirname”的目录***

```
[lywd@localhost ~]$ ls
 Desktop  Documents  Downloads  Music  perl5  Pictures  Public 
 Templates  Videos  Workspace
[lywd@localhost ~]$ mkdir dirname
[lywd@localhost ~]$ ls
 Desktop  dirname  Documents  Downloads  Music  perl5 
 Pictures  Public  Templates  Videos  Workspace

```
5.rmdir指令：rmdir[选项][dirname] rmdir与mkdir相反，rmdir为删除命令。

```
[lywd@localhost ~]$ rmdir dirname/
[lywd@localhost ~]$ ls
Desktop  Documents  Downloads  Music  perl5  Pictures  Public  
Templates  Videos  Workspace

```

## 二.对文件的操作命令：
6. touch指令：touch[选项][文件名]  ***更改文档或者目录的日期时间，或者新建一个不存在的文件。***

```
[lywd@localhost Workspace]$ cd test/
[lywd@localhost test]$ ls
[lywd@localhost test]$ touch test.c
[lywd@localhost test]$ ls
test.c
[lywd@localhost test]$ 

```

7.cat指令:cat[选项][文件] ***查看目标文件的内容***
  例如我们查看test.c的代码内容：
```
[lywd@localhost proc]$ cat test.c
#include<stdio.h>
#include<sys/types.h>
#include<unistd.h>

int main()
{
  while(1){
      sleep(1);
  }
  return 0;
}

```

       
8.cp指令：cp[选项][源文件或目录] ***复制文件或目录***
 
 我们在test目录下新建文件a.c,并将a.c复制到test.c文件，实列如下：

```
[lywd@localhost test]$ ls
test.c
[lywd@localhost test]$ touch a.c
[lywd@localhost test]$ ls
a.c  test.c
[lywd@localhost test]$ cp a.c test.c
[lywd@localhost test]$ ls
a.c  test.c
[lywd@localhost test]$ cat test.c 
[lywd@localhost test]$ ls
a.c  test.c
[lywd@localhost test]$ 

```
       
                   
                  
9.mv指令：mv[选项][源文件][目标文件] ***移动文件或者将文件改名***
   例如将a.c文件名该为b.c
```
[lywd@localhost test]$ mv a.c b.c
[lywd@localhost test]$ ls
b.c  test.c
[lywd@localhost test]$ 

```
                     
10.more指令与less指令：more/less[选项][文件名]  
      ***这两个指令和cat指令有者相似的功能可查阅当前文件的内容，more指令可以让屏幕在显示满时暂停，less指令可以分页显示文件且q键可退出浏览。***
 
 11.head和tail指令：head/tail[参数][文件]  ***head用来显示文件开头至标准输出当中，tail用来看文件的结尾。***


 ## 三.Linux权限的概念：
        

> linux下有两种用户：超级用户(root),普通用户。
> 用户的切换指令：su（普通->root）su  user(root->普通)

### 0.如何查看文件的权限：ls -la或者ls -ID
如下我们用ls -la即可看到每个文件的权限
```
[lywd@localhost ~]$ ls -la
total 60
drwx------. 19 lywd lywd  4096 Apr 19 10:44 .
drwxr-xr-x.  3 root root    18 Dec 22 08:14 ..
-rw-------.  1 lywd lywd  3292 Apr 19 07:12 .bash_history
-rw-r--r--.  1 lywd lywd    18 Apr 10  2018 .bash_logout
-rw-r--r--.  1 lywd lywd   193 Apr 10  2018 .bash_profile
-rw-r--r--.  1 lywd lywd   231 Apr 10  2018 .bashrc

```
### 1.权限的分类以及种类：
      

> 文件和文件目录的所有者：u-------User
> 文件和文件目录所在组的用户：g--------Group
> 其他用户：o--------Others
> r---可读   w----可写  x-----可执行
> rwx可以用数三位八进制表示即777即（111 111 111）

例如r--  只读，  rwx（777）， 可读可写可执行


设定或重置指定用户类型的权限

### 2.有关文件权限的命令：
       
#### 1)chmod命令：chmod【参数】【权限】【文件名】  ***设置文件的访问权限***
来看下段代码，我们可以看到文件b.c并没有可执行权限，我们通过chmod命令可以给其所属组增加可执行权限。
```
[lywd@localhost test]$ ls
b.c  test.c
[lywd@localhost test]$ ls -la
total 0
drwxrwxr-x. 2 lywd lywd 31 Apr 19 07:03 .
drwxrwxr-x. 5 lywd lywd 45 Apr 19 06:54 ..
-rw-rw-r--. 1 lywd lywd  0 Apr 19 06:56 b.c
-rw-rw-r--. 1 lywd lywd  0 Apr 19 06:56 test.c
[lywd@localhost test]$ chmod u+x b.c
[lywd@localhost test]$ ls -la
total 0
drwxrwxr-x. 2 lywd lywd 31 Apr 19 07:03 .
drwxrwxr-x. 5 lywd lywd 45 Apr 19 06:54 ..
-rwxrw-r--. 1 lywd lywd  0 Apr 19 06:56 b.c
-rw-rw-r--. 1 lywd lywd  0 Apr 19 06:56 test.c

```
#### 2）chown命令：类似于chmod命令
这里展示使用数字表示权限：

```
[root@localhost test]# chown 777 b.c
[root@localhost test]# ls -la
total 0
drwxrwxr-x. 2 lywd lywd 31 Apr 19 07:03 .
drwxrwxr-x. 5 lywd lywd 45 Apr 19 06:54 ..
-rwxrwxr--. 1  777 lywd  0 Apr 19 06:56 b.c
-rw-rw-r--. 1 lywd lywd  0 Apr 19 06:56 test.c

```

#### 3）chgrp命令：chgrp【参数】【用户组名】【文件名】***修改文件或目录的所属组***
#### 4）umask命令：umask 【权限值】 ***查看或修改文件掩码***

```
[lywd@localhost test]$ umask
0777

```
其中第一个0与特殊权限有关，可以不用考虑。


**总结：**

> 文件的访问及使用限制由文件本身的权限决定，但是文件名称的修改、文件本身的
 删除是由文件所在目录的w权限决定的。
 文件的拥有者或者root可以修改档案或者目录的权限。
 cd、ls等系统提供的命令也是有权限的，可以通过删除其x权限禁止用户使
用该命令。
 用户使用系统：即用户通过一个程序去操作文件或档案，要顺利进行这一动作，得
 满足两个条件：
 （1）对该程序的访问权限：如对 cd 、ls 等的x权限
 （2）对要操作的目标文件的访问权限

     
