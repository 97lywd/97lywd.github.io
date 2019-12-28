---
title: C++类与对象(二)
date: 2019-08-31 15:58:26
tags:
    - C++
---
## 1.构造函数
- 名字与类名相同，创建类类型时编译器自动调用
- 无返回值
- 可以重载
- 本质不是开辟空间创建对象，而是初始化对象
- 如果未定义构造函数，编译器会自动生成默认的构造函数

<!--more-->
``` cpp
	class Date
	{
	public:
		Date() {}  //无参构造
		Date(int year, int month, int day)   //带参构造
		{
			_year = year;
			_month = month;
			_day = day;
		}
	private:
		int _year;
		int _month;
		int _day;
	};
	Date d1;   //调用无参构造
	Date d2(2000, 5, 4);  //调用带参构造
	Date d3();   //注意这行代码其实是声明了d3函数
```
- 全缺省的构造函数和无参的构造函数都是默认构造函数
来看下面一个demo

```cpp
//
错误	C2668	“main::Date::Date”: 对重载函数的调用不明确	

class Date
{
public:
	Date()
	{
		_year = 2000;
		_month = 10;
		_day = 1;
	}
	Date(int year = 1999, int month = 10, int day = 1)
	{
		_year = year;
		_month = month;
		_day = day;
	}
private:
	int _year;
	int _month;
	int _day;
};
Date d1;
```
这里为什么会出现这这样的错误呢，因为这里的d1的调用出现了歧义，即无参，全缺省的带参都可调用

## 2.析构函数
- 析构函数其实就是在函数声明周期结束时对构造的对象进行资源清理
- 函数名时类名前加~
- 无参数，无返回值
- 一个类只有一个析构函数，若未显式定义，系统会自动生成默认析构函数
例如如下析构函数（模拟实现string类定义的析构函数）

```cpp
~String()
	{
		if (_str)
		{
			delete[] _str;
			_str = nullptr;
			_size = _capacity = 0;
		}
	}

```

## 3.拷贝构造函数
- 拷贝构造函数是构造函数的重载
- 参数只有一个，且必须是引用传参，否则会引发无穷递归调用

看下面的demo

```cpp
class Date
{
public:
	Date(int year = 2000, int month = 9, int day = 1)
		{
			_year = year;
			_month = month;
			_day = day;
	}
	Date(const Date&d)
	{
		_year = d._year;
		_month = d._month;
		_day = d._day;
	}
private:
	int _year;
	int _month;
	int _day;
};
Date d1;
Date d2(d1);
```
调用d1,需要调用类Date的拷贝构造函数，而调用Date拷贝构造函数的结果就是再次调用拷贝构造，如此递归下去。

- 若未显示定义拷贝构造函数，系统会自动生成默认的函数，但注意是浅拷贝


## 4.赋值运算符重载

#### A.运算符重载

 - 函数名为 关键字operator后跟要重载的运算符
- 函数原型：返回类型 operator操作符(参数)
例如在Date类中对==运算符的重载，代码如下：

```cpp
bool operator==(const Date& d)const {
		return (_year == d._year) &&
		 (_month == d._month) && (_day == d._day);
	}
```
#### B.赋值运算符重载
- 返回值是引用（可以连续赋值）
- 要检查是否是自己给自己赋值
- 返回*this
例如在Date类的实现里，赋值运算符的重载，代码如下

```cpp
	Date& operator=(const Date &d) // =重载
	{
		if (this != &d)
		{
			_year = d._year;
			_day = d._day;
			_month = d._month;
		}
		return *this;
	}

```
- 若未显示定义拷贝构造函数，系统会自动生成默认的函数，注意也是浅拷贝

## 5.const成员函数
- const修饰的类成员函数就是const成员函数
- 实际上修饰的是隐含的this指针，表明在改函数中，不能对类成员进行修改
来看下面的代码

```cpp
class Date
{
public:
	void display()
	{
		cout << "display()" << endl;
		cout << year << endl;
		cout << month << endl;
	}
	void display()const
	{
		cout << "display()const" << endl;
		cout << year << endl;
		cout << month << endl;
	}
private:
	int year;
	int month;
};
Date d1;
d1.display();
const Date d2;
d2.display();
```
那么上面的代码d1，d2都调用display函数，它怎么辨别的呢？这时就是看调用对象的属性了。所以有以下结论。

- const成员函数可以访问非const对象的非const成员
- 非const成员函数只能访问非const对象的任意成员
- const成员函数可以访问const对象的所有成员

