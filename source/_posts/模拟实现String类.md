---
title: 模拟实现String类
date: 2019-09-04 22:53:53
tags:
     - C++
     - STL
---
# string类是什么
-  string是表示字符串的字符串类
- 该类的接口与常规容器的接口基本相同，再添加了一些专门用来操作string的常规操作。
-  string在底层实际是：basic_string模板类的别名，typedef basic_string<char, char_traits, allocator> string;
-  不能操作多字节或者变长字符的序列。
<!--more-->
# string类的接口实现
## 1.构造函数

```cpp
String(const char* str = "")
	{
		size_t len = strlen(str);
		_size = len;
		_capacity = _size;
		_str = new char[_capacity + 1];
		strcpy(_str, str);
	}

```
## 2.析构函数

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

``` cpp
String(const String& s)
		:_str(nullptr)
		, _size(0)
		, _capacity(0)
	{
		String tmp(s._str);
		Swap(tmp);
	}


	void Swap(String& s)
	{
		swap(_str, s._str);
		swap(_size, s._size);
		swap(_capacity, s._capacity);
	}

```
## 4.插入

```cpp
//插入的是字符，如果是字符串，重载这个函数即可
void Insert(size_t pos, const char c)
	{
		assert(pos <= _size);
		if (_size == _capacity)
		{
			size_t newC = _capacity == 0 ? 15 : 2 * _capacity;
			Reserve(newC);
		}
		size_t end = _size;
		while (end > pos)
		{
			_str[end] = _str[end - 1];
			--end;
		}

		_str[pos] = c;
		_str[++_size] = '\0';

	}
```
## 5.删除

```cpp
void Erase(size_t pos, size_t n)
	{
		assert(pos < _size);
		if (n >= _size - pos)
		{
			_size = pos;
			_str[_size] = '\0';
			return;
		}
		size_t begin = pos + n;
		while (begin <= _size)
		{
			_str[pos++] = _str[begin];
			++begin;
		}

		_size -= n;
	}
```
## 6.查找

```cpp
//这里是查找一个字符，如果是字符串，重载这个函数即可
size_t find(size_t pos, char c)
	{
		for (int i = pos; i < _size; i++)
		{
			if (_str[i] == c)
				return i;
		}
		return -1;
	}
```
# 深拷贝与浅拷贝
##  1.浅拷贝
也叫位拷贝，即编译器只将对象中的值拷贝过来，不考虑资源的管理。
然而对于上述的例子，即string类的实现，这种方法就行不通。string类中私有成员里有一个char* 成员变量，在调用赋值或者拷贝构造时，就会两个对象共享一片内存，此时析构函数清理资源时，就会出现重复销毁。

## 2.深拷贝
如果一个类中涉及到资源的管理，其拷贝构造函数、赋值运算符重载以及析构函数必须要显式给出，一般都是按照深拷贝方式提供。
深拷贝的执行方式是，为每一个对象独立分配资源，此时清理资源时，就不会造成访问违规现象。
