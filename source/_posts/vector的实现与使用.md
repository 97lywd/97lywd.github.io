---
title: vector的实现与使用
date: 2019-09-07 16:18:23
tags:
    - C++
    - STL
---

# 1.vector的介绍与使用接口
## 1.1 vector是什么
- vector是一种序列式容器
- 其底层实现原理和数组一样，但不同的是，它的大小可动态改变
- 与其他序列式容器相比vector的随机访问使访问效率更加高效(O(1))
## 1.2 iterator的使用
<!--more-->
```cpp

//vector的使用
#include<vector>
int main()
{
	vector<int> vt;
	vt.push_back(1);
	vt.push_back(2);
	vt.push_back(3);
	vt.push_back(4);
	//通过拿到迭代器首位置，遍历所有元素
	vector<int>::iterator it = vt.begin();
	while (it != vt.end())
	{
		cout << *it << endl;
		it++;
	}
	//通过反向迭代器，拿到末尾，遍历所有元素
	vector<int>::reverse_iterator rit = vt.rbegin();
	while (rit != vt.rend())
	{
		cout << *rit << endl;
		rit++;
	}
		system("pause");
	return 0;
}
```
## 1.3 vector空间增长倍率

```cpp
//测试vector的容量
int main()
{
	vector<int> v;
	size_t sz;
	sz = v.capacity();
	for (int i = 0; i < 100; i++)
	{
		v.push_back(i);
		if (sz != v.capacity())
		{
			sz = v.capacity();
			cout << sz << endl;
		}
	}
	system("pause");
	return 0;
}
```
- 从上面的demo我们可以测得在VS下		vector空间的增长速率大概在1.5倍左右
## 1.4 vector的增删改查

```cpp
int main()
{
	vector<int> vt;
	for (int i = 0; i < 10; i++)
		vt.push_back(i);
	//修改0的值
	vt[0] = 200;
	//使用find查找6
	vector<int>::iterator pos = find(vt.begin(), vt.end(), 6);
	//在6之前插入100
	vt.insert(pos, 100);
	for (auto e : vt)
	{
		cout << e << " ";
	}
	cout << endl;
	//删除pos位置的数据
	pos = find(vt.begin(), vt.end(), 6);
	vt.erase(pos);
	for (auto e : vt)
	{
		cout << e << " ";
	}
	system("pause");
	return 0;
}
```
## 1.5 reverse与resize
**reverse与resize都有改变容器大小的能力，它们区别之处在哪呢？来看下面的demo。**
在测试reverse的demo里，我们在未插入之前修改capacity的大小，在后续插入时，会发现capacity并未改动。假设我们将reserve的值改为50，我们会发现capacity的值回改动

- 所以我们可知，reverse的作用在于直接修改capacity的容量，如果后续容量不够，会从设定的reverse值开始扩容。
```cpp
//测试reverse
int main()
{
	vector<int> vt;
	size_t sz = vt.capacity();
	vt.reserve(100);
	for (int i = 0; i < 100; i++)
	{
		vt.push_back(i);
		if (sz != vt.capacity())
		{
			sz = vt.capacity();
			cout << sz << endl;
		}
	}
	system("pause");
	return 0;
}
```
在下面测试resize的demo里，我们可以看到如果resize的值小于capacity值时，直接去掉resize传进来的值到end之间的元素，当然resize还可以指定插入元素，如下面的例子6-8将会被初始化为100，如果未指定且这个坐标下无元素时将会被初始化为0。

- 所以我们可知，resize的作用在于修改当前end指向的位置，并且对其初始化，如果大于capacity时，会调用reverse借口进行扩容。
```cpp
//测试resize
int main()
{
	vector<int> vt;
	for (int i = 0; i < 10; i++)
		vt.push_back(i);
	vt.resize(5);   //0,1,2,3,4
	vt.resize(8, 100);  //0,1,2,3,4,100,100,100
	vt.resize(10);    //0,1,2,3,4,100,100,100,0,0
	for (auto e : vt)
		cout << e << endl;
	system("pause");
	return 0;
}
```

# 2.vector的模拟实现
## 2.1 底层原理
来看下图![在这里插入图片描述](https://img-blog.csdnimg.cn/20190907153416566.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjU0OTI1OQ==,size_16,color_FFFFFF,t_70)
vector的底层有三个指针，一是指向首位置，一是指向当前size的位置，一是指向当前capacity的位置。
## 2.2 构造函数

```cpp
Vector()
		:_start(nullptr)
		, _finish(nullptr)
		, _endOfStorage(nullptr)
	{}
```
## 2.3 析构函数

```cpp
~Vector()
	{
		delete[]_start;
		_start = _endOfStorage = _finish;
	}
```
## 2.4 插入

```cpp
//尾插
void pushback(const T&val)
	{
		if (_finish == _endOfStorage)
		{
			size_t newCapacity = Capacity() == 0 ? 1 : Capacity() * 2;
			Reserve(newCapacity);
		}
		*_finish = val;
		++_finish;
	}
	//利用迭代器实现随机插入
	void Insert(T* pos, const T&val)
	{
		assert(pos <= _finish && pos >= _start);
		if (_finish == _endOfStorage)
		{
			size_t size = Size();
			size_t newCapacity = Capacity() == 0 ? 1 : Capacity() * 2;
			Reserve(newCapacity);
			pos = _start + size;
		}
		iterator end = _finish - 1;
		while (pos <= end) {
			*end = *(end - 1);
			end--;
		}
		*pos = val;
		_finish++;
	}
	
```
## 2.5 删除

```cpp
iterator Erase(iterator pos)
	{
		assert(pos < _finish && pos >= _start);
		iterator begin = pos + 1;
		while (begin < _finish) {
			*(begin - 1) = *(begin);
			begin++;
		}
		_finish--;
		return pos;
	}
```
# 3.vector迭代器失效的问题
我们继续来看上面的一个demo：

```cpp
int main()
{
	vector<int> vt;
	for (int i = 0; i < 10; i++)
		vt.push_back(i);
	//修改0的值
	vt[0] = 200;
	//使用find查找6
	vector<int>::iterator pos = find(vt.begin(), vt.end(), 3);
	//在6之前插入100
	vt.insert(pos, 100);
	for (auto e : vt)
	{
		cout << e << " ";
	}
	cout << endl;
	//删除pos位置的数据
	//pos = find(vt.begin(), vt.end(), 3);
	vt.erase(pos);
	for (auto e : vt)
	{
		cout << e << " ";
	}
	system("pause");
	return 0;
}
```
在这里插入元素后，我们注释掉了重新拿到迭代器的代码，运行发现访问异常，这就是迭代器实效问题，在上面这个例子里面，插入元素导致扩容，扩容后pos还指向原来的空间，但是原来的空间已经被释放。

- 对于插入操作，如果未引发扩容，迭代器容有效，如果引发扩容，迭代器失效。


那么删除操作是怎么样造成的呢? 在删除操作的实现函数里，pos值是在start和finish之间，然后从pos+1开始到finish依次向前移动，此时pos指向已经发生变化并且finish位置也会变化，随后it++可能出现指向finish后面的位置，所以访问异常。

- 对于删除操作，pos之前迭代器有效，pos之后迭代器失效。

