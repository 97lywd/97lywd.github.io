---
title: C++类与对象(三)
date: 2019-09-01 22:38:26
tags:
     - C++
---

## 一.构造函数的初始化列表

```cpp
class Date
{
public:
	Date(int year, int month ,int day)
		:_year(year),
		_month(month),
		_day(day)
	{}
private:
	int _year;
	int _month;
	int _day;
};
```
<!--more-->
如上例，初始化列表以一个冒号开始，逗号分隔数据成员，每个成员变量之后的括号里跟一个初始值。

- 初始化只能初始一次
- const成员，引用，类类型成员必须放在初始化列表初始化
- 成员变量的初始化顺序与初始化列表中列出的变量的顺序无关，它只与成员变量在类中声明的顺序有关

看下面的Demo

```cpp
class Demo {
public:
	Demo(int b)
		:m_b(b),m_a(m_b)
	{}
	void show()
	{
		cout << "m_a" << m_a << "m_b" << m_b << endl;
	}
private:
	int m_a;
	int m_b;
};
Demo a(100);
a.show();
```
上面代码初始化列表里看似是先初始化m_b,再初始化m_a,但是初始化顺序只与成员变量声明顺序有关，所以这块等价于m_a = m_b, m_b = b;而m_b此时还未初始化所以m_a是一个随机值。

## 二.static成员

```cpp
class Student{
public:
    Student(char *name, int age, float score);
    void show();
public:
    static int m_total;  //静态成员变量
private:
    char *m_name;
    int m_age;
    float m_score;
};
int Student::m_total = 100;
```
- 静态成员类内定义，但必须在类外初始化
- 静态成员函数与普通函数区别在于没有this指针，只能访问静态成员
- 在声明静态成员函数时需要加static，而定义时就不必再加


## 三.友元
- 友元函数
   - 友元可以直接访问类的私有成员，是定义在类外的普通函数
   - 需要在类内部声明，使用friend关键字

例如在模拟实现string类时，我们重载operator<<时，cout输出流对象和this指针都在抢占第一个参数位置，只是我们就需要将operator<<重载为全局对象，那么在类外访问类内成员，此时就要用到友元函数。

```cpp
ostream& operator<<(ostream& _cout, const String& s)
{
	_cout << s.c_str() << endl;
	return _cout;
}
class String
{
    ......
   friend ostream& operator<<(ostream& _cout, const String& s);
    ......
}

```
- 友元类
  若A是B的友元类，那么A的成员都可以访问B的任意非公有成员
  

## 四.内部类
概念：如果一个类定义在另一个类的内部，这个内部类就叫做内部类。

注意：内部类就是外部类的友元类。注意友元类的定义，内部类可以通过外部类的对象参数来访问外部类中的所有成员。但是外部类不是内部类的友元。
-   另外请注意，友元的鲜明特点：友元关系是一种单向的关系。

