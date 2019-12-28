---
title: c++类与对象(一)
date: 2019-08-28 17:02:31
tags:
    - C++
---
## 一.类的定义
- 定义和声明全在类内

```cpp
class Date
{
  public:
     void showInfo(){}
  private:
     int _day;
     int _month;
};
```
<!--more-->

- 声明在头文件，定义在.cpp文件之中

```cpp
//date.h文件
class Date
{
 public:
    void showInfo();
 private:
    int _day;
    int _month;
};
```

```cpp
//date.cpp文件

#include"date.h"
void Date::showInfo()
{
     cout <<_month<<--<<_day<< endl;
}
```
类的定义一般有以上两种方式，但是后者更加规范。
在上面的demo里，我们可以看到，类的定义关键词是class,Date是类名，{}里类的主体,并且以;号结尾。
**其中类的元素,称为类的成员,函数称为类的成员函数,数据称为成员变量。**


## 二.类的访问限定符
- 访问限定符有三种：public,private,protected
- public修饰的成员可以被类外进行访问,private和protected修饰不可。
- class的默认访问权限是private,而struct默认的是public


## 三.类的作用域与实例化
- 类定义了一个作用域，如果需要在类外定义成员，那么就要使用::作用解析符,指明成员属于哪个域。
- 类创建对象的过程，称之为类的实例化

```cpp
class Person
{
    public:
        void display(){}
    private:
        char* _name;
        int _age;
};
void test()
{
//实例化
     Person man;
     man._name = "Pony"
     man._age = 10;
     man.display();
}
```

## 四.类的大小的计算
- 一个类的大小，其实就是所有成员变量之和，当然也遵循内存对齐(就是结构体的对齐规则)，如果是空类，编译器给了一个字符唯一标识这个类。
看这样一个demo：

```cpp
class A
	{
	public :
		void fun1() {}
	private:
		int _num;
	};
class B
	{
	public:
		void fun2() {}
	};
class C
	{};
	cout << sizeof(A) << endl;
	cout << sizeof(B) << endl;
	cout << sizeof(C) << endl;
```
上面的输出结果是4，1，1，这就说明了类的大小其实就是成员变量之和。

## 五.this指针
- this指针的类型：类类型 * const
- 只能在成员函数内部使用
- 是成员函数的第一个隐含的形参，由编译器调用

先看这样一个例子：可以编译通过吗？

```cpp
	class A
	{
	public:
		void fun()
		{
			cout <<" Hello" << endl;
		}
	private:
		int a;
	};
	int main()
	{
	   A*p = NULL;
	   p->fun();
	   return 0;
	}
```
事实上，上面的例子可以输出，这是为什么呢？p是一个NULl指针，调用成员函数不会出错误？其实p->fun()是fun(b);因为没有fun函数没有访问成员，所以不存在解引用，所以正常输出；我们可以通过一个例子来验证

```cpp
class A
	{
	public:
		void fun()
		{
			cout <<" Hello" << endl;
		}
		void fun2()
		{
			cout << a << endl;
		}
	private:
		int a;
	};
int main()
	{
	   A*p = NULL;
	   p->fun();
	   p->fun2();       //引发程序崩溃
	   return 0;
	}
```

- 在非静态成员函数内部可以直接使用 this 关键字，this 就代表指向该函数所作用的对象的指针
看下面这个程序：

```cpp
class price
	{
	public:
		int num, cost;
		price(int n, int c)
			:num(n),cost(c){}
		price add()
		{
			this->cost++;
			return *this;
		}
	};
	price n1(1,1), n2(0,0);
	n2 = n1.add();
	cout << n2.num<< n2.cost << endl;

//输出结果：1，2
```
第九行的this的类型是price*，所以this->cost就是cost，*this就是n1，所以n2的值才会变成n1。


