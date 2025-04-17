# 学习c++的一些笔记

### pass by value and pass by reference and pass by address
* pass by value: 传递的是值的副本，对副本的修改不会影响原变量。
* pass by reference: 传递的是变量的引用，对引用的修改会影响原变量。
* pass by address: 传递的是变量的地址，对地址的修改会影响原变量。 
```cpp
#include <iostream>
 // pass by value
void modifyValue(int a)
{
	a = 10;
}

// pass by reference
void modifyReference(int& a)
{
	a = 10;
}

// pass by pointer
void modifyPointer(int* a)
{
	*a = 10;
}

int main()
{
	int a = 5;
	std::cout << "Original value: " << a << std::endl;
	modifyValue(a);
	std::cout << "After modifyValue: " << a << std::endl;

	a = 5; // reset value
	std::cout << "Original value: " << a << std::endl;
	modifyReference(a);
	std::cout << "After modifyReference: " << a << std::endl;

	a = 5; // reset value
	std::cout << "Original value: " << a << std::endl;
	modifyPointer(&a);
	std::cout << "After modifyPointer: " << a << std::endl;

	int b = 5;
	int* p_b = &b;
	std::cout << "Original value: b = " << b << std::endl;
	std::cout << "Original value: *p_b = " << *p_b << std::endl;
	modifyPointer(p_b);
	std::cout << "After modifyPointer: b = " << b << std::endl;
	std::cout << "After modifyPointer: *p_b = " << *p_b << std::endl;
}
```
output:
```bash
Original value: 5
After modifyValue: 5
Original value: 5
After modifyReference: 10
Original value: 5
After modifyPointer: 10
Original value: b = 5
Original value: *p_b = 5
After modifyPointer: b = 10
After modifyPointer: *p_b = 10
```

### 指针的指针 值传递和指针传递
如果需要在函数内部修改指针的指向（即让指针指向一块新的内存），函数的参数必须是指针的指针（int**）或指针的引用（C++ 中的 int*&）
```cpp
#include <iostream>

void re_pointer_1(int* func_p_a, int* new_address)
{
	func_p_a = new_address;
}

void re_pointer_2(int** func_p_a, int* new_address)
{
	*func_p_a = new_address;
}

void re_pointer_3(int*& func_p_a, int* new_address)
{
	func_p_a = new_address;
}

int main()
{
	int a = 10;
	int* p_a = &a;
	int** pp_a = &p_a;
	int b = 20;

	std::cout << "Value of a: " << a << std::endl;
	std::cout << "Address of a: " << &a << std::endl;
	std::cout << "Value of p_a: " << p_a << std::endl;
	std::cout << "Address of p_a:" << &p_a << std::endl;
	std::cout << "Value of pp_a: " << pp_a << std::endl;
	std::cout << "Address of pp_a: " << &pp_a << std::endl;

	std::cout << "Value pointed to by p_a: " << *p_a << std::endl;
	std::cout << "Value pointed to by pp_a: " << *pp_a << std::endl;
	std::cout << "Value pointed to by pp_a (dereferencing twice): " << **pp_a << std::endl;

	re_pointer_1(p_a, &b);
	// re_pointer_2(&p_a, &b);
	// re_pointer_3(p_a, &b);

	std::cout << *p_a << std::endl;
}
```
![pointer](cpp_imgs/pointer.png)    
因为c++是值传递，这里的func_p_a是p_a的副本，所以func_p_a的修改不会影响p_a。可以发现，这里的func_p_a和p_a的地址是不同的，但是它们的值是相同的。    
![pointer_to_pointer](cpp_imgs/pointer_to_pointer.png)    
这里函数传入的是指针的指针，传入的是p_a的地址，所以func_p_a的修改会影响p_a。

**需要使用二级指针或者多级指针的情况：**
  1. 动态内存分配并返回给外部指针
  2. 修改链表/树的头指针
  3. 动态创建二维数组（指针数组）
  4. 函数返回多个指针
  5. 回调函数中修改外部指针
  6. 字符串数组（char**）的动态操作
  7. 多级间接寻址（如函数指针数组）   
在 C++ 中，可用 指针的引用（如 int*&）替代二级指针  
***核心规则：若需修改某个指针的值（而非其指向的内容），必须传递该指针的地址（即二级指针）！***


### const和static 
1. const    
const 用于定义常量或限制变量、函数、指针等的修改行为，表示“不可修改”。
 - 修饰变量 声明一个常量，值不可修改。
    ```
    const int MAX_SIZE = 100; // 常量，不能修改
    // MAX_SIZE = 200; // 错误！不能修改 const 变量
    ```
 -  修饰指针    
        const 可以修饰指针本身或指针指向的数据：

        在使用const时，如果关键字const出现在星号左边，表示被指物是常量；如果出现在星号右边，表示指针本身是常量；如果出现在星号两边，表示被指物和指针两者都是常量。

        const int* ptr：指针指向的数据是常量，不能通过 ptr 修改。

        int* const ptr：指针本身是常量，不能修改指针的指向。

        const int* const ptr：指针和它指向的数据都是常量


    ```cpp
    int a = 10, b = 20;
    const int* ptr1 = &a; // 指向的数据是 const
    // *ptr1 = 30; // 错误！不能修改指向的数据
    ptr1 = &b;     // 可以修改指针的指向

    int* const ptr2 = &a; // 指针本身是 const
    *ptr2 = 30;     // 可以修改指向的数据
    // ptr2 = &b;   // 错误！不能修改指针的指向

    const int* const ptr3 = &a; // 指针和指向的数据都是 const
    // *ptr3 = 40; // 错误！
    // ptr3 = &b;  // 错误！
    ```

- 修饰函数参数:防止函数内部修改传入的参数         
    ```cpp
    void printData(const int* data, int size) {
        for (int i = 0; i < size; i++) {
            // data[i] = 0; // 错误！不能修改 const 数据
            cout << data[i] << " ";
        }
    }
    ```
-  修饰成员函数
表示该函数不会修改类的成员变量（只能调用其他 const 成员函数）。
    ```cpp
    class MyClass {
        int value;
    public:
        int getValue() const { // const 成员函数
            // value = 10; // 错误！不能修改成员变量
            return value;
        }
    };
    ```
- 修饰返回值
表示函数返回的是常量，不能通过返回值修改外部变量。
    ```cpp
    const int* getData() {
        static int data = 42;
        return &data;
    }

    int main() {
        const int* ptr = getData();
        // *ptr = 100; // 错误！不能修改 const 返回值
        return 0;
    }
    ```

2. static
static 用于定义静态变量、静态函数、静态成员等，具有以下特点：
 - 修饰局部变量    
使局部变量的生命周期延长到整个程序运行期间（但作用域仍限于函数内）。   
变量只初始化一次，后续调用保持之前的值。
    ```cpp
    void counter() {
        static int count = 0; // 只初始化一次
        count++;
        cout << count << endl;
    }

    int main() {
        counter(); // 输出 1
        counter(); // 输出 2
        counter(); // 输出 3
        return 0;
    }
    ```
 - 修饰全局变量/函数    
  限制全局变量或函数的作用域，使其仅在当前文件可见（防止命名冲突）
    ```cpp
    // file1.cpp
    static int localVar = 42; // 只在 file1.cpp 可见
    static void localFunc() {} // 只在 file1.cpp 可见

    // file2.cpp
    extern int localVar; // 错误！无法访问 file1.cpp 的 static 变量
    ```
 - 修饰成员变量
  静态成员变量属于类，不属于任何对象，所有对象共享该变量。必须在类外初始化
    ```cpp
    class MyClass {
    public:
        static int count; // 声明
        MyClass() { count++; }
    };

    int MyClass::count = 0; // 必须初始化

    int main() {
        MyClass obj1, obj2;
        cout << MyClass::count; // 输出 2（所有对象共享）
        return 0;
    }
    ``` 
 - 修饰成员函数    
    静态成员函数属于类，而不是某个对象，可以直接通过类名调用。
    静态成员函数不能访问非静态成员变量（需要通过对象访问），但可以访问静态成员变量。
    ```cpp
    class MyClass {
    public:
        static void printCount() {
            // cout << value; // 错误！不能访问非静态成员
            cout << "Count: " << count << endl;
        }
        static int count;
        int value;
    };

    int MyClass::count = 0;

    int main() {
        MyClass::printCount(); // 直接调用，无需对象
        return 0;
    }
    ```
3. const 和 static 的组合使用    
   static const：表示静态常量（通常用于类的常量成员）
    ```cpp
    class Math {
    public:
        static const double PI = 3.14159; // 静态常量
    };
    ```


### 全局变量 静态局部变量 静态全局变量

|    特性    |     全局变量     |     静态局部变量     |   静态全局变量   |
| :--------: | :--------------: | :------------------: | :--------------: |
|   作用域   |     文件全局     |   函数内部(static)   | 文件内部(static) |
|   存储期   |    静态存储期    |      静态存储期      |    静态存储期    |
|   链接性   |     外部链接     |        无链接        |     内部链接     |
| 初始化次数 |       一次       | 一次(首次调用函数时) |       一次       |
|   可见性   | 可被其他文件访问 |     仅函数内可见     |   仅本文件可见   |

**静态变量（无论是局部还是全局）的主要特点是它们保持其值在程序的整个生命周期内，但限制了其可见范围，这有助于实现信息隐藏和模     块化编程。**    

### c++中各种变量的存储区域
- 静态存储区：存放全局变量、静态变量、常量
- 栈区：存放局部变量、函数参数
- 堆区：动态分配的内存(new/malloc)
- 代码区：存放程序的执行代码

详细内容参考：[C++内存模型](https://www.cnblogs.com/lqerio/p/12110482.html#%E5%8A%A8&%E9%9D%99)


### constexpr 常量表达式
const对象表示表明值不会改变，但是在编译的时候并不一定能取得结果     
constexpr是const的一种加强，constexpr对象的值不仅不变，而且在编译时就能取得结果    
如果一个const变量在编译的时候就能取得结果，那么使用constexpr修饰他更好  
      

constexpr函数可以把运行时的计算移到编译时，但是会增加编译时间。如果函数体很复杂，编译器可能会选择不在编译时计算   


constexpr 全称是constant expression，表示常量表达式   
常量表达是指值不会改变，并且在编译时就能取得结果

特点：值不会改变，编译时就能取得结果，编译器会在编译时计算出值并替换掉变量名。

使用场景：
* 数组大小
* 整型模板实参
* switch-case 中的case标签
* 枚举量的值
* 对齐规格
  
```cpp
int i1 = 42;  // i1不是常量表达式
const int i2 = i1;  // i2不是常量表达式：初始值i1不是常量表达式
const int i3 = 42;  // i3是常量表达式
const int i4 = i3;  // i4是常量表达式：初始值i3是常量表达式
const int i5 = get_size();  // 因为get_size()是普通函数，运行时才能确定，所以i5不是常量表达式
```

```cpp
int i1 = 42;
constexpr int i2 = i1;  // 报错：表达式必须含有常量值。因为constexpr变量i2必须使用常量表达式初始化，不允许在常量表达式中读取非const变量
constexpr int i3 = 42;  // i3是常量表达式
constexpr int i4 = i3 + 1;  // i4是常量表达式：初始值i3是常量表达式
constexpr int i5 = get_size(); // 报错：表达式必须含有常量值。因为get_size()时constexpr函数才可以
constexpr int i6 = get_size2(); // i6是常量表达式：初始值get_size2()是常量表达式
```
constexpr函数是指能用于常量表达式的函数，函数的返回类型和所有的形参的类型都要是字面值类型，而且函数中有且只有一个return语句。    
**constexpr函数不一定返回常量表达式**
```cpp
constexpr int sum(int a, int b)
{
    return a + b;
}

constexpr int i2 = sum(a, b);  // 所有参数都是常量表达式，sum 的结果也是常量表达式，在编译期求值

int AddThree(int a)
{
    return  sum(a, 3);  // // i 不是常量表达式，此时 sum 作为普通函数使用
}
```
constexpr的好处
1. 编译器可以保证 constexpr 对象是常量表达式（能够在编译期取得结果），而 const 对象不能保证。如果一个 const 变量能够在编译期求值，将其改为 constexpr 能够让代码更清晰易读
2. constexpr 函数可以把运行期计算迁移至编译期，使得程序运行更快（但会增加编译时间）

### Lambda表达式
lambda表达式: ()[]mutabel ->returnType{}    
():参数列表，捕获外部变量，供函数体使用    
[]:捕获列表，类似于函数对象
mutable:可变关键字，取消[]中捕获变量的const属性，允许修改捕获的变量
->返回值类型，
{}函数体
* 捕捉列表[] 和 函数体{} 不可省略
* 如果使用了 mutable关键字 或者 ->returntype 返回值，就不能省略 ( )参数列表，即使为空
* 返回值类型编译器可以推导，(SonarCube提示为了避免损失精度和适应未来需求，可以取消返回值，让程序自己推到返回值类型)
* 最简单的lambda表达式：[]{}
```cpp
int main()
{
	// 直接使用lambda表达式
	int ret = [](int a, int b) {return a + b; }(1, 2);
	std::cout << ret << std::endl;

	// 创建匿名函数对象
	auto add = [](int a, int b) {return a + b; };
	std::cout << add(3, 6) << std::endl;

	// 参数设为引用
	int x = 3;
	int y = 4;
	std::cout << "交换前" << std::endl;
	std::cout << "\tx:" << x << std::endl << "\ty:" << y << std::endl;
	auto swap = [](int& a, int& b) {
		auto temp = a;
		a = b;
		b = temp;
		};

	swap(x, y);
	std::cout << "交换后" << std::endl;
	std::cout << "\tx:" << x << std::endl << "\ty:" << y << std::endl;

	// 捕获外部变量
	// 捉列表 中的参数是一个值类型（传值捕捉），此时的捕获的是外部变量的内容，然后赋值到 “m、m” 中，
	// 捕捉列表 中的参数默认具有 常量属性，不能直接修改，但可以添加 mutable 关键字 取消常性

	int m = 10;
	int n = 20;
	std::cout << "交换前" << std::endl;
	std::cout << "\tm:" << m << std::endl << "\tn:" << n << std::endl;
	auto swap_2 = [m, n]() mutable {
		auto temp = m;
		m = n;
		n = temp;
		};

	swap_2();
	// 没有改变，因为捕捉列表中的参数是值传递，需要修改还是要使用引用传递
	std::cout << "交换后" << std::endl;
	std::cout << "\tm:" << m << std::endl << "\tn:" << n << std::endl;

	auto swap_3 = [&m, &n]() {
		auto temp = m;
		m = n;
		n = temp;
		};

	swap_3();

	std::cout << "使用swap3交换后" << std::endl;
	std::cout << "\tm:" << m << std::endl << "\tn:" << n << std::endl;

	// 不需要指定， 使用[&]可以引用捕捉外部所有变量,使用[=]全部值捕捉
	auto func = [&]() {
		std::cout << x << " " << y << " " << m << " " << n << std::endl;
		};

	func();

	std::cout << "lambda表达式的大小: " << std::endl;
	std::cout << "\t" << sizeof(swap) << " byte" << std::endl;

	return 0;
}
```

lambda表达式的有点
* 简洁性： 对于简单的函数操作，无需再手动创建函数、调用，只需要编写一个 lambda 表达式生成函数对象
* 方便性： lambda 表达式具有 捕捉列表，可以轻松捕获外部的变量，避免繁琐的参数传递与接收
* 函数编程支持： lambda 表达式可以作为函数的参数、返回值或存储在数据结构中
* 内联定义： lambda表达式可以作为函数的参数、返回值或存储在数据结构中
* 简化代码： 对于一些简单的操作，使用 lambda 表达式可以减少代码的行数，提高代码的可读性


### 仿函数
https://www.cnblogs.com/ISmileLi/p/12699732.html

1、仿函数概念
仿函数是定义了一个含有operator()成员函数的对象，可以视为一个一般的函数，只不过这个函数功能是在一个类中的运算符operator()中实现，是一个函数对象，它将函数作为参数传递的方式来使用。

2、仿函数的优缺点   
优点：      
    1）仿函数比函数指针的执行速度快，函数指针时通过地址调用，而仿函数是对运算符operator进行自定义来提高调用的效率。  
    2）仿函数比一般函数灵活，可以同时拥有两个不同的状态实体，一般函数不具备此种功能。   
    3）仿函数可以作为模板参数使用，因为每个仿函数都拥有自己的类型。  
缺点：      
    1）需要单独实现一个类。   
    2）定义形式比较复杂。   

3、仿函数作用
仿函数通常有下面四个作用：   
    1）作为排序规则，在一些特殊情况下排序是不能直接使用运算符<或者>时，可以使用仿函数。     
    2）作为判别式使用，即返回值为bool类型。     
    3）同时拥有多种内部状态，比如返回一个值得同时并累加。   
    4）作为算法for_each的返回值使用。   
**仿函数作为排序规则**
```cpp
#include <iostream>
#include <string>
#include <set>
using namespace std;

class Student
{
public:
	string getName() const
	{
		return m_name;
	}

	float getScore() const
	{
		return m_score;
	}

public:
	string m_name;
	float m_score;
};

class compareName
{
public:
	bool operator() (const Student& s1, const Student& s2) const
	{
		return s1.getName() < s2.getName();
	}
};

int main()
{
	cout << "----------------仿函数作为排序规则使用--------------" << endl;

	using StudentSet = set<Student, compareName>;
	StudentSet stuSet;
	stuSet.clear();
	Student stuJack;
	stuJack.m_name = "Jack";
	stuJack.m_score = 80;

	Student stuToby;
	stuToby.m_name = "Toby";
	stuToby.m_score = 90;

	Student stuISmileLi;
	stuISmileLi.m_name = "ISmileLi";
	stuISmileLi.m_score = 100;

	// 插入数值
	stuSet.insert(stuJack);
	stuSet.insert(stuToby);
	stuSet.insert(stuISmileLi);

	// 遍历
	for (auto it = stuSet.begin(); it != stuSet.end(); ++it)
	{
		cout << "姓名: " << it->getName() << " 分数: " << it->getScore() << endl;
	}
}
```
**作为判别式示例**
```cpp
#include <iostream>
#include <list>
#include <algorithm>

using namespace std;

class RmNum
{
private:
	int m_count = 0; // In-class initializer for m_count
	int m_num;
public:
	explicit RmNum(int num) : m_num(num) {} 

	bool operator() (int) 
	{
		return ++m_count == m_num;
	}
};

//打印函数
void myPrintf(list<int>& lt)
{
	for (auto it = lt.begin(); it != lt.end(); ++it)
	{
		cout << *it << " ";
	}
	cout << endl;
}

int main()
{
	cout << "----------------仿函数作为判别式使用--------------" << endl;
	cout << "----------------list中插入数值--------------" << endl;
	list<int> tmpList;
	for (int i = 0; i < 9; i++)
	{
		tmpList.emplace_back(i);
	}
	cout << "----------------打印list中插入的数值--------------" << endl;
	myPrintf(tmpList);

	cout << "----------------删除list中符合条件的数值--------------" << endl;
	auto pos = remove_if(tmpList.begin(), tmpList.end(), RmNum(2));  // 
	tmpList.erase(pos, tmpList.end()); //删除后面的数
	cout << "----------------打印删除后的list--------------" << endl;	
	myPrintf(tmpList);
	return 0;
}

```
输出:这个输出有点意思，估计要研究一下remove_if的实现原理了
```
----------------仿函数作为判别式使用--------------
----------------list中插入数值--------------
----------------打印list中插入的数值--------------
0 1 2 3 4 5 6 7 8
----------------删除list中符合条件的数值--------------
----------------打印删除后的list--------------
0 2 4 5 6 7 8
```
**作为内部状态**
```cpp
class MySequence
{
public:
	explicit MySequence(int ivalue) : m_value(ivalue) {}

	int operator()()
	{
		return ++m_value;
	}
private:
	int m_value;
};

//打印函数
void myPrintf(list<int>& lt)
{
	for (auto it = lt.begin(); it != lt.end(); ++it)
	{
		cout << *it << " ";
	}
	cout << endl;
}

int main()
{
	cout << "----------------仿函数拥有内部状态--------------" << endl;
	cout << "----------------list中插入数值--------------" << endl;
	list<int> tmpList;
	cout << "----------------从1开始序列化list--------------" << endl;
	generate_n(back_inserter(tmpList), 9, MySequence(1));
	myPrintf(tmpList);
	cout << "----------------从10开始序列化list--------------" << endl;
	generate(tmpList.begin(), tmpList.end(), MySequence(10));
	myPrintf(tmpList);

	return 0;
}
```
**for_each使用**
```cpp
class AverageValue
{
private:
	int m_sum;
	int m_count;

public:
	void operator()(int value)
	{
		++m_count;
		m_sum += value;
	}

	//返回平均值
	double average() const
	{
        return static_cast<double>(m_sum) / m_count;
	}

};

//打印函数
void myPrintf(vector<int>& lt)
{
	for (auto it = lt.begin(); it != lt.end(); ++it)
	{
		cout << *it << " ";
	}
	cout << endl;
}

int main()
{
	cout << "----------------仿函数作为算法for_each的返回值使用--------------" << endl;

	vector<int> tmpVector;
	cout << "----------------vector中插入数值--------------" << endl;
	for (int i = 0; i < 10; i++)
	{
		tmpVector.emplace_back(i);
	}
	cout << "----------------打印vector中插入的数值--------------" << endl;
	myPrintf(tmpVector);
	AverageValue averValue = for_each(tmpVector.begin(), tmpVector.end(), AverageValue());
	cout << averValue.average() << endl;
	return 0;
}
```


### 函数指针  
函数指针是指向函数的指针变量。

通常我们说的指针变量是指向一个整型、字符型或数组等变量，而函数指针是指向函数。

函数指针可以像一般函数一样，用于调用函数、传递参数。

函数指针类型的声明：
```cpp
// typedef int (*fun_ptr)(int,int); // 声明一个指向同样参数、返回值的函数指针类型

int get_max(int x, int y)
{
	return (x > y) ? x : y;
}


int main()
{
	int a = 10;
	int b = 20;
	int c = 30;
	// Function pointer
	int (*p)(int, int) = &get_max;
	int d = p(p(a, b), c);
	std::cout << "The maximum value is: " << d << std::endl;

	d = p(a, p(c, d));
	std::cout << "The maximum value is: " << d << std::endl;
	return 0;
}
```


### 回调函数
函数指针作为某个函数的参数      
函数指针变量可以作为某个函数的参数来使用的，回调函数就是一个通过函数指针调用的函数。

简单讲：回调函数是由别人的函数执行时调用你实现的函数
```cpp
void populate_array(int* arr, size_t arr_size, int(*getNextValue)(void))
{
for (size_t i = 0; i < arr_size; ++i)
{
	arr[i] = getNextValue();
}
}

int getNextRandomValue()
{
return rand();
}

int main()
{
	int arr[10];
	populate_array(arr, 10, getNextRandomValue);

	// Replacing raw for-loop with range-based for-loop
	for (const auto& value : arr)
	{
		std::cout << value << " ";
	}
    return 0;
}
```