---
title: 动态规划(一)：从Fbionacci数列谈起
date: 2019-08-25 14:36:25
tags:
    - Algorithm
    - C++
---

## 一.什么是动态规划
    动态规划其实是一种分治算法思想的延申，在将大问题化解为小问题的分治过程中，
    保存对这些小问题已经处理好的结果，并供后面处理更大规模的问题时直接使用这些结果。    
<!--more-->
## 二.动态规划的特点
     1.重叠子问题
     2.保存子问题的解
     3.所有子问题只需解决一次
## 三.如何使用动态规划解决问题
     1.定义状态
     2.状态转移方程
     3.状态初始化
     4.返回结果
**A. Fibonacci问题**
    题目难度：easy
   题目来源：<a href= https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId   target = "_blank">牛客网</a>
*<font color=#0099ff>题目描述：
大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0）。
n<=39</font>*
    
     1.定义状态：F(n)---第n项的Fibonacci数
     2.状态转移方程：F(n) = F(n-1) + F(n-2)
     3.初始值: F(0) = 0,F(1) = 1
     4.返回值：F(n)
代码如下：

```c++
class Solution {
public:
    int Fibonacci(int n) {
       if(n <= 0)
       {
        return 0;
       }
        if(n==1 || n==2){
         return 1;   
        }
        int *ret = new int[n + 1];
        ret[0] = 0;
        ret[1] = 1;
        for(int i = 2; i <= n; i++)
        {
            ret[i] = ret[i - 1]+ret[i - 2];
        }
        return ret[n];
        delete[]ret;
    }
};
```

事实上，我们还可以将空间复杂度O(n)优化至O(1),即只需要记录第n项的n-1项值和n-2项的值，不必保存所有子问题的解。
**B.变态青蛙跳台阶问题**
题目难度：easy
题目来源：[牛客网](https://www.nowcoder.com/practice/22243d016f6b47f2a6928b4313c85387?tpId)
题目描述：
*<font color=#0099ff>一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。</font>*

     1.定义状态：F(n)----还剩n个台阶时有多种跳法
     2.状态方程：假设前面跳到了n-1个台阶，那么对于剩下的
     一个台阶而言还是有F(n-1)种，所以我们可得F(n) = 2*F(n-1）
     3.初始值：F(1) = 1
     4.返回值： F(n)
代码如下：

```c++
class Solution {
public:
    int jumpFloorII(int number) {
         if(number <= 0)
             return 0;
        int ret = 1;
        for(int i = 1;i < number; i++)
        {
            ret *= 2;
        }
        return ret;
    }
};
```
对于上面的代码，我们其实可以优化到O(1),从状态转化方程我们可以看出，这是一个等比数列，所以对于F(n)的求解我们可以使用移位操作，即等于
1<<(number-1)

**C.连续子数组的最大值**
题目难度：easy
题目来源：[牛客网](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId)
题目描述:
*<font color=#0099ff>HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)</font>*

    1.定义状态：F(i)---以array[i]元素结尾的子数组最大值
    2.状态方程：F(i) = max(array[i],array[i]+F(i-1)
    3.初始值:F(0) = array[0]
    4.返回值：maxsum（max，F(i))
代码如下：

```c++
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
       if(array.empty())
           return -1;
        int sum = array[0];
        int maxsum = array[0];
        for(int i = 1;i < array.size();i++)
        {
            
          sum = max(array[i],array[i]+sum);
           maxsum = (sum < maxsum)?maxsum:sum;
        }
        return maxsum;
    }
};
```
