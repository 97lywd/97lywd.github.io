---
title: list的实现及使用
date: 2019-10-16 23:35:26
tags:
    - C++
    - STL
---

# 1.List的介绍及使用接口

## 1.1 List是什么
   List是可以在常数范围内在任意位置进行插入和删除的序列式容器，并且该容器可以前后双向迭代，List的底层是双向链表结构。
## 1.2 iterator的使用
   如下demo，我们分别用正向迭代器和反向迭代器访问List元素
   
<!--more-->
```cpp
int main()
{
	int array[] = { 1,2,3,4 };
	list<int>l1(array, array + sizeof(array) / sizeof(array[0]));
	list<int>::iterator it = l1.begin();
	//正向1234
	while (it != l1.end())
	{
		cout << *it << " ";
		it++;
	}
	//逆向4321
	list<int>::reverse_iterator it2 = l1.rbegin();
	while (it2 != l1.rend())
	{
		cout << *it2 << " ";
		it2++;
	}
	system("pause");
	return 0;
}
```

   
   ## 1.2 List的插入与删除
   
### 1.2.1插入接口
```cpp
iterator insert (iterator position, const value_type& val);
```
注意list的插入要先拿到迭代器的位置
另外它重载两种接口，一是在迭代器位置插入n个val，一是在迭代器位置
插入一个start到end区间内所有元素

```cpp
void insert (iterator position, size_type n, const value_type& val);
template <class InputIterator>
void insert (iterator position, InputIterator first, 
                                 InputIterator last);
```

```cpp
int main()
{
	list<int> lt(1,1);
	list<int>::iterator it = lt.begin();
	lt.insert(it, 2);  //2,1
	lt.insert(it, 2, 100); //2,100,100,1
	list<int> lt2(3, 200);
	lt.insert(it, lt2.begin(), lt2.end());  //2,100,100,200,200,1;
	return 0;
}
```
### 1.2.2 删除接口

```cpp
iterator erase (iterator position);
```
list的删除也是通过迭代器来实现，并返回一个迭代器位置，另外重载了一个接口，
可以实现范围删除。
**要注意的是这里返回的迭代器是被删元素的下一个元素，如果最后一个元素也被删了，那么它就是容器的结尾**

```cpp
iterator erase (iterator first, iterator last);
```

```cpp
	lt.insert(it, lt2.begin(), lt2.end());  //2,100,100,200,200,1;
	it = lt.erase(it);  //2,100,100,200,200
	list<int>::iterator it2 = lt.begin(); 
	it2++;
	lt.erase(it2, it);   //2
```
## 1.3list迭代器失效问题
list在删除元素时，会出现问题
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191006235533415.png)
例如我们删除了中间位置节点，++迭代器时，就会崩溃，因为对于双向链表找到下个节点，需要有前一个节点
解决办法：

- 1.采用上面的方法，接受erase的返回值
- 2.自增iterator


# 2.List的模拟实现
## 2.1List的底层原理
List的底层实际上是一个双向带头节点链表，所有它的节点就包括指向前后的指针，以及节点的值

```cpp
struct ListNode{
	ListNode(const T&val = T())
		:_prev(nullptr)
	    , _next(nullptr)
		, _val(val)
	{}
	ListNode<T>*_prev;
	ListNode<T>*_next;
	T _val;
};
```
## 2.2构造函数
头的前后都指向自己

```cpp
List()
		:_head(new Node)
	{
		_head->_next = _head;
		_head->_prev = _head;
	}
```
## 2.3删除与插入
### 2.3.1 插入
构造新节点，更新新节点的指针指向，以及前后节点的指针指向
```cpp
void Insert(iterator pos, const T&val)
	{
		Node* newNode = new Node(val);
		Node* cur = pos._node;
		Node* prev = cur->_prev;
		prev->_next = newNode;
		newNode->_prev = prev;
		cur->_prev = newNode;
		newNode->_next = cur;
	}
```
### 2.3.2 删除
通过迭代器找到删除位置，保存前后指针指向，删除这个节点，更新指针指向
```cpp
iterator Erase(iterator pos)
	{
		if (pos != end())
		{

			Node* cur = pos._node;
			Node* prev = cur->_prev;
			Node* next = cur->_next;
			delete cur;
			cur = nullptr;
			prev->_next = next;
			next->_prev = prev;
			pos = iterator(next);
		}
		return pos;
	}
```
## 2.3迭代器的实现
list的迭代器需要封装一个类来实现，原生指针无法实现，所以它必须封装一个指针所有的功能。`

```cpp
template<class T,class Ptr,class Ref>
struct ListIterator {
	typedef ListNode<T> Node;
	typedef ListIterator<T, Ptr, Ref> Self;
	Node* _node;
	ListIterator(Node* p = nullptr)
		:_node(p)
	{}
	Ref operator*()
	{
		return _node->_val;
	}
	Ptr operator->() 
	{
		return &_node->_val;
	}
	Self& operator++()
	{
		_node = _node->_next;
		return *this;
	 }
	bool operator!=(const Self& lt)
	{
		return _node != lt._node;
	}
	bool operator==(const Self& lt)
	{
		return _node == lt._node;
	}
};
```

