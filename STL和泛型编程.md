## C++标准库和泛型编程

__STL六大部件（components）__  
* 容器（Containers）
* 分配器（Allocators）
* 算法（Algorithms）
* 迭代器（Iterators）
* 适配器（Adapters）
* 仿函数（Functors）   

 容器是“前闭后开”的区间
 ```cpp
 Container<T> c;
 ...
 Container<T>::iterator ite = c.begin();
 for(;ite != c.end(); ++ite)
    ...
 ```

 range-based for statement
 ```cpp
 for(decl : coll)
    statement
 ```
 auto关键词    

 
### 容器
容器分类    
* 关联式容器
* * Array
* * Vector
* * Deques
* * List
* * Forward-list
* 序列式容器
* * Set/Multiset
* * Map/Multimap
* * Unordered Set/Multiset
* * Unordered Map/Multimap    


### oop(object-oriented programming) vs. GP(generic programming)
OOP将datas和methods联系起来    
GP将datas和methods分开开    

采用GP:containers和Algorithms可以各种闭门造车，其间通过Iterator沟通即可    
Algorithms通过Iterator获取操作范围，并沟通Iterator取用Container的元素    

### 操作符重载 & 类模板
模板分类：
* 类模板
* 函数模板
* 成员模板   

特化（Specialization）   
泛化&特化
```cpp
// 泛化
template <class type>
struct __type_traits
{
   ...
   ...
};

// 特化
template<> struct __type_traits<int>
{
   ...
   ...
};

template<> struct __type_traits<double>
{
   ...
   ...
};
```
偏特化（partial specialization）   
（参数数量的偏特化&范围的偏特化）
```cpp
// 泛化
template<class type, class Alloc = alloc>
class vector
{
   ...
};

// 参数数量的偏特化
template<class Alloc>
class vector<bool, Alloc>
{
   ...
};
```

```cpp
// 泛化
template<class Iterator>
struct iterator_traits  // 任意类型
{
   ...
};

// 范围的偏特化
template<class T>
struct iterator_traits<T*>  // 只能指针类型
{
   ...
};

// 范围的偏特化
template<class T>
truct iterator_traits<const T*>  //只能是const指针类型
{
   ...
};
```

c++中i++和++i的区别
```cpp
int i = 0;
int a = i++;  // a = 0, i = 1
int b = ++i;  // b = 2, i = 2
// i++ 是后缀递增运算符，它的优先级较低，这意味着在使用 i++ 时，i 的值会在表达式求值之后增加。
// ++i 是前缀递增运算符，它的优先级较高，这意味着在使用 ++i 时，i 的值会在表达式求值之前增加。
// 当你需要在表达式中使用变量的原始值，并且希望在表达式完成后增加变量的值时，使用 i++。
// 当你需要在表达式中使用变量的新值，或者在递增操作之前需要变量的新值时，使用 ++i。
```
### 迭代器
iterator必须提供5种associated types    


### Traits 特性
Iterator Traits用以区分class iterator 和 non-class iterator   
这个traits机器必须有能力分辨他所获得的iterator是class iterator T 还是 native pointer to T。利用partial specialization 可达到目标
```cpp
// 当需要知道I的value type时
template<typename I,...>
void algorithm(...)
{
   typename iterator_traits<I>::value_type v1;
}

// 如果I是class iterator
template <class T>
struct iterator_trait
{
   typedef typename I::value_type value_type;
};

// 如果I是pointer to T 
template <class T>
struct iterator_traits<T*>
{
   typedef T value_type;
};

// 如果I是pointer to const T
template <class T>
struct iterator_traits<const T*>
{
   typedef T value_type;
};
```

### deque 
分段连续


### 红黑树
平衡二分搜索树

### set和multiset
以rb_tree为底层结构   的iterator改变元素值   
set元素的key不能重复，因此其insert()用的是rb_tree的insert_unique()    
multiset的元素key可以重复，因此其insert()用的是rb_tree的insert_equal()   


### map和multimap
map元素的key不能重复，因此其insert()用的是rb_tree的insert_unique()    
multimap的元素key可以重复，因此其insert()用的是rb_tree的insert_equal()   
map中的key指定为const，禁止user对元素的key赋值 


### hashtable


### 算法 algorithm
**accumulate累加算法**
```cpp
namespace test_accumulate
{
	int myfunc(int x, int y) { return x + 2 * y; }

	struct myclass {
		int operator()(int x, int y) { return x + 3 * y; }
	}myobj;

	void test_accumulate()
	{
		int init = 100;
		int nums[] = {10, 20, 30};

		cout << "使用默认的accumulate :";
		cout << accumulate(nums, nums + 3, init) << endl;
		
		cout << "使用functional中的minus :";
		cout << accumulate(nums, nums + 3, init, minus<int>()) << endl;

		cout << "使用custom function :";
		cout << accumulate(nums, nums + 3, init, myfunc) << endl;

		cout << "使用custom class :";
		cout << accumulate(nums, nums + 3, init, myobj) << endl;
	}
}
```
**for_each算法**
```cpp
namespace test_for_each
{
	void myfunc(int i)
	{
		cout << ' ' << i;
	}

	struct myclass{
		void operator() (int i) { cout << ' ' << i; }
	}myobj;

	void test_for_each()
	{
		vector<int> myvec;
		myvec.push_back(10);
		myvec.push_back(20);
		myvec.push_back(30);
		for_each(myvec.begin(), myvec.end(), myfunc);
		cout << endl;

		for_each(myvec.begin(), myvec.end(), myobj);
		cout << endl;

		// C++11
		for (auto& elem : myvec)
		{
			elem += 5;
		}
		for (auto elem : myvec)
		{
			cout << ' ' << elem;
		}
		cout << endl;
	}
}
```

**repalce, replace_if, relapce_copy**    
**count,count_if**    
**find,find_if**    
容器不带成员函数count,find
* array, vector, list, forward_list, deque
容器带有成员函数count,find
* set/multiset, map/multimap, unordered_set/unordered_multiset, unordered_map/unordered_multimap    
**sort**
```cpp
namespace test_sort
{
	bool myfunc(int i, int j) { return (i < j); }

	struct myclass {
		bool operator() (int i, int j) { return (i < j); }
	}myobj;

	void test_sort()
	{
		int myint[] = { 32, 71, 12, 45, 26, 80, 53, 33 };
		vector<int> myvec(myint, myint + 8);
		
		// 使用默认比较
		sort(myvec.begin(), myvec.begin() + 4);

		// 使用函数作为comp
		sort(myvec.begin() + 4, myvec.end(), myfunc);

		// 使用对象作为comp
		sort(myvec.begin(), myvec.end(), myobj);
		cout << "myvec contains:";
		for (auto elem : myvec)
		{
			cout << " " << elem;
		}
		cout << endl;

		sort(myvec.rbegin(), myvec.rend());
		cout << "myvec contains:";
		for (auto elem : myvec)
		{
			cout << " " << elem;
		}
		cout << endl;
	}
}
```
**binary_serach**   
已经排序   
lower_bound

### 仿函数functors
只为算法服务    
是一个对象，像一个函数


#### 一个万用的hash function








