---
title: 'C++:动态内存管理'
date: 2019-09-02 23:42:52
tags:
    - C++
---

# 一.c语言中malloc/calloc/realloc的区别
我们来看一下以下demo：
<!--more-->
```cpp
int main()
{
	int *p1 = (int*)malloc(sizeof(int));
	free(p1);
	int *p2 = (int*)calloc(4, sizeof(int));
	//free(p2);
	cout << &p2 << endl;
	//00EFF9A4
	int *p3 = (int*)realloc(p2, sizeof(10000));
	cout << &p3 << endl;
	//00EFF998
	free(p3);
	system("pause");
	return 0;
}

```
- malloc会在堆上开辟指定字节的空间，但不会初始化。
- calloc与malloc相似 ，但是会初始化成0，并且可以指定元素个数
- realloc是给已分配空间的指针重新分配指定大小的空间
- 需要注意的是，如上例中，我们注意到realloc指定的空间太大，导致后面的空间不够时，会重新开辟一段空间，并且将原来的值拷贝过来。

# 二.C++中是如何实现动态内存管理
## 1.内置类型
看以下demo：

```cpp
int main()
{
	int *p1 = new int;  //动态申请一个int类型空间
	delete p1;
	int *p2 = new int(10);  //动态申请一个int类型空间
	delete p2;;
	int *p3 = new int[10];   //动态申请十个int类型空间
	delete []p3;
}
```
- 事实上，对于内置类型，new底层调用是operator new（实际上也是malloc来实现的），delete底层调用的是operator delete (实际上是用free来实现的)。
## 2.自定义类型
先看下面的demo：

```cpp
class demo
	{
	public:
		demo()
			:_data(0)
		{
			cout << "test()" << this << endl;
		}
		~demo()
		{
			cout << "~test()" << this << endl;
		}
	private:
		int _data;
	};
	void test1()
	{
		demo *p1 = (demo*)malloc(sizeof(demo));
		free(p1);
	}
	void test2()
	{
		demo *p2 = new demo;
		delete p2;
	}
	int main()
	{
		test2();
		return 0;
	}
```
- 自定义类型使用new时会调用构造函数，delete会调用析构函数。而malloc和free不会。

# 三.new和delete底层实现原理
  ###   内置类型
与malloc/delete基本一致，如果申请连续空间，则需要new[]/delete[]
### 自定义类型
- new的原理
1.调用operator new申请空间
2.调用构造函数，完成对象的构造
- delete的原理
1.调用析构函数，完成对象的清理
2.调用operato delete释放空间
对于连续n个空间，则执行n次上述操作。
### 定位new表达式
在已分配的空间调用构造函数初始化一个对象
使用方法：(以上面的demo类为例)

```cpp
//p1现在指向的是demo对象大小相同的一段空间，但是并不是一个对象
demo* p1 = (demo*)malloc(sizeof(demo));
//执行完new即调用构造函数后，它就成为了一个对象，
//注意，构造函数有参数，那么下面的代码需要传参
new(pt) demo;
```
# 四.new/delete 与 malloc/free区别总结
-  #### 1.属性
new/delete是C++关键字，需要编译器支持。malloc/free是库函数，需要头文件支持。
 
-  #### 2.参数
使用new操作符申请内存分配时无须指定内存块的大小，编译器会根据类型信息自行计算。而malloc则需要显式地指出所需内存的尺寸。
 
-  #### 3.返回类型
new操作符内存分配成功时，返回的是对象类型的指针，类型严格与对象匹配，无须进行类型转换，故new是符合类型安全性的操作符。而malloc内存分配成功则是返回void * ，需要通过强制类型转换将void*指针转换成我们需要的类型。
 
- #### 4.分配失败
new内存分配失败时，会抛出bac_alloc异常。malloc分配内存失败时返回NULL。
 
-  #### 5.自定义类型
new会先调用operator new函数，申请足够的内存（通常底层使用malloc实现）。然后调用类型的构造函数，初始化成员变量，最后返回自定义类型指针。delete先调用析构函数，然后调用operator delete函数释放内存（通常底层使用free实现）。
malloc/free是库函数，只能动态的申请和释放内存，无法强制要求其做自定义类型对象构造和析构工作。
