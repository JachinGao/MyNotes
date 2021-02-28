## 什么是库

    库是编程模块的集合，可以在程序中调用它们。库对很多常见的编程问题提供了可靠的解决方法，因此可以节省程序员大量的时间和工作量。



## 1. Hello World

```c++
#include <iostream> //标准输入输出流
using namespace std;  // 使用命名空间， std打开一个叫std房间

//函数入口
int main() {
	
	// endl 结束换行
	cout << "Hello World" << endl;  
	system("pause");
	return 0;
}
```



## 2. 双冒号作用于运算符

```c++
int atk = 200;
void test01()
{
	int atk = 100;

	cout << "攻击力为 ： " << atk << endl;
	//双冒号 作用域运算符  ::全局作用域
	cout << "全局攻击力为 ： " << ::atk << endl;
}

//输出结果
攻击力为 ： 100
全局攻击力为 ： 200
```

  

## 3. namespace的使用

##### namespace命名空间主要用途 用来解决命名冲突的问题

(1). 命名空间下可以放函数、变量、结构体、类；

(2). 命名空间必须定义在全局作用域下；

(3). 命名空间可以嵌套命名空间；

(4). 同名命名空间会合并；

(5). 匿名命名空间；

(6). 命名空间可以起别名；


game_1.h
```c++
#include <iostream>
using namespace std;

namespace LOL 
{
	void goAtk();
}
```

game_2.h

```c++
#include <iostream>
using namespace std;

namespace KingGlory
{
	void goAtk();
}
```

game_1.cpp

```c++
#include "game_1.h"

void LOL::goAtk()
{
	cout << "LOL攻击实现" << endl;
}
```

game_2.cpp

```c++
#include "game_2.h"

void KingGlory::goAtk()
{
	cout << "王者农药攻击实现" << endl;
}
```

main.cpp

```c++
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

#include "game_1.h"
#include "game_2.h"

//namespace命名空间主要用途 用来解决命名冲突的问题
void test01()
{
	LOL::goAtk();
	KingGlory::goAtk();
}

int main() {

	test01();

	system("pause");
	return EXIT_SUCCESS;
}
```



##### 命名空间放变量示例

```c++
namespace A 
{
	int m_value = 10;
}

int main() {
	cout << A::m_value << endl;
	system("pause");
	return EXIT_SUCCESS;
}

//返回结果为10；
```



##### 命名空间嵌套命名空间

```c++
namespace A 
{
	int m_value = 10;

	namespace B 
	{
		int m_value = 20;
	}
}

int main() {
	cout << A::m_value << endl;  //输出 10；
	cout << A::B::m_value << endl; 、、输出 20；

	system("pause");
	return EXIT_SUCCESS;
}
```



##### 匿名命名空间

```c++
namespace
{
	int m_C = 0;
	int m_D = 0;
}
//当写了无名命名空间，相当于写了 static int m_C ; static int m_D;
//只能在当前文件内使用
```



##### 命名空间起别名

```c++
namespace Name_A {
	int m_val = 20;
}

int main() {
	namespace Name_B = Name_A;
	cout << Name_A::m_val << endl;  //输出20
	cout << Name_B::m_val << endl;  //输出20

	system("pause");
	return EXIT_SUCCESS;
}
```





## 4. using声明和using编译指令

```c++
namespace Tom
{
	int resId = 10;
}

namespace Jerry
{
	int resId = 30;
}

int main() {
	int resId = 20;
	//using Tom::resId; //写了using声明后,以后用到的resId是用Tom下的

	using namespace Tom;  //打开Tom房间；
	using namespace Jerry; //打开Jerry房间；

	cout << resId << endl;	//输出20；
    
    //如果打开多个房间，也要避免二义性问题
	cout << Jerry::resId << endl;  //输出30

	system("pause");
	return EXIT_SUCCESS;
}
```





## 5. c++对c语言的增强

**(1). 全局变量的检测增强**

**(2). 函数的检测增强**

​	参数类型、返回值、参数个数等c++严格检测；

**(3). 类型转换增强**

​	malloc返回void* ，C中可以不用强转，C++必须强转

**(4). struct增强**

​    c语言中struct中不可以加函数；

​	使用C必须加关键字 struct ，C++可以不加；

```c++
struct Person 
{
	int mAge;
	void addAge() {
		mAge++;
	}
};

int main() {
	Person p1;
	p1.mAge = 20;
	p1.addAge();
	
	cout << p1.mAge << endl;  //输出结果为：21
	
	system("pause");
	return EXIT_SUCCESS;
}
```

**(5). bool增强**

 	c语言中没有bool；

**(6). const增强**

c语言中，const修饰的变量，是伪变量，编译器是会分配内存的；

c++中，const不会分配内存，编译器会临时开辟一块内存空间，临时控件看不到；

```c++
void test()
{
	const int m_B = 20; //真正常量

	int * p = (int *)&m_B;
	*p = 200;
	cout << "*p = " << *p << endl;
	cout << "m_B = " << m_B << endl;

	int arr[m_B]; //可以初始化数组
}

//输出结果
// *p = 200
// m_B = 20
```



## 6. const

#####  c语言中的const默认是外部链接；

```c
#include<stdio.h>
#include<string.h>
#include<stdlib.h>

int main(){
	extern const int a; //告诉编译器在a在外部
	printf("a = %d \n ", a);   //输出10

	system("pause");
	return EXIT_SUCCESS;
}
```

**test.c**

```
const int a = 10; //C语言中默认const是外部链接
```

##### c++语言中的const默认是外部链接

test.cpp

```
extern const int a = 10; //C++中的const默认内部链接 ,extern提高作用域
```

```c++
#include<iostream>
using namespace std;

int main(){
	extern const int a;
	cout << a << endl;

	system("pause");
	return EXIT_SUCCESS;
}
```

##### const分配内存情况

(1). const分配内存 取地址会分配临时内存

(2). extern编译器也会给const变量分配内存

(3). 用普通变量初始化 const 的变量

(4). 自定义数据类型  加const也会分配内存

```c++
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include <string>
using namespace std;

//1、const分配内存 取地址会分配临时内存
//2、extern 编译器也会给const变量分配内存
void test01()
{
	const int m_A = 10;
	int * p = (int*)&m_A; //会分配临时内存
	*p = 100;
	cout << "m_A: " << m_A << endl;  //输出： m_A = 10
	cout << "*p: " << *p << endl;  //输出： *p = 100
}

//3、 用普通变量初始化 const 的变量
void test02()
{
	int a = 10;
	const int b = a; //会分配内存

	int * p = (int *)&b;
	*p = 1000;

	cout << "b = " << b << endl;  //输出 b = 1000
	cout << "*p = " << *p << endl; //输出 *p = 1000
}

//4、 自定义数据类型  加const也会分配内存
struct Person
{
	string m_Name; //姓名
	int m_Age;
};
void test03()
{
	const Person p1;
	//p1.m_Name = "aaa";

	Person * p = (Person*)&p1;
	p->m_Name = "德玛西亚";
	(*p).m_Age = 18;

	cout << "姓名： " << p1.m_Name << " 年龄： " << p1.m_Age << endl;
	//输出： 姓名： 德玛西亚 年龄： 18
}

int main() {

	test01();
	test02();
	test03();

	system("pause");
	return EXIT_SUCCESS;
}
```

## 7. 尽量以const替换#define



