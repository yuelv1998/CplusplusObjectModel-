
```c++
// project100.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。
//

#include "pch.h"
#include <iostream>
using namespace std;

class A {
public:
	int a;
	A()
	{
		printf("A::A()的this指针是：%p!\n", this);
	}
	void funcA()
	{
		printf("A::funcA()的this指针是：%p!\n", this);
	}
};

class B {
public:
	int b;
	B()
	{
		printf("B::B()的this指针是：%p!\n", this);
	}
	void funcB()
	{
		printf("B::funcB()的this指针是：%p!\n", this);
	}
};

class C : public A, public B
{
public:
	int c;
	C()
	{
		printf("C::C()的this指针是：%p!\n", this);
	}
	void funcC()
	{
		printf("C::funcC()的this指针是：%p!\n", this);
	}

	void funcB()
	{
		printf("C::funcB()的this指针是：%p!\n", this);
	}
};


int main()
{
	//this指针调整：多重继承
	cout << sizeof(A) << endl;
	cout << sizeof(B) << endl;
	cout << sizeof(C) << endl;

	C myc;
	myc.funcA();
	myc.funcB(); //已经被子类C覆盖了
	myc.B::funcB(); 
	myc.funcC();

	//派生类对象 它是包含 基类子对象的。
	//如果派生类只从一个基类继承的话，那么这个派生类对象的地址和基类子对象的地址相同。
	//但如果派生类对象同时继承多个基类，那么大家就要注意：
	  //第一个基类子对象的开始地址和派生类对象的开始地址相同。
	     //后续这些基类子对象的开始地址 和派生类对象的开始地址相差多少呢？那就得吧前边那些基类子对象所占用的内存空间干掉。

	//总结：你调用哪个子类的成员函数，这个this指针就会被编译器自动调整到对象内存布局中 对应该子类对象的起始地址那去；


	
	return 1; 
}

```

![2-3](..\img\2-3.png)

```c++
/******************************************************************************

Welcome to GDB Online.
GDB online is an online compiler and debugger tool for C, C++, Python, Java, PHP, Ruby, Perl,
C#, VB, Swift, Pascal, Fortran, Haskell, Objective-C, Assembly, HTML, CSS, JS, SQLite, Prolog.
Code, Compile, Run and Debug online from anywhere in world.

*******************************************************************************/
#include <stdio.h>
#include <iostream>

using namespace std;

class A
{
    public: 
        int a;
        A()
        {
            std::cout << "A::A()的this指针是？" << this << std::endl;
        }
        void funcA()
        {
            std::cout << "A::funcA()的this指针是：" << this << std::endl;
        }
};

class B
{
    public:
        int b;
        B()
        {
            std::cout << "B::B的this指针是：" << this << std::endl;
        }
        void funcB()
        {
            std::cout << "B::funcB()的this指针是：" << this << std::endl;
        }
};

class C:public A, public B
{
    public:
        int c;
        C()
        {
            std::cout << "C::C的this指针是：" << this << std::endl;
        }
        void funcC()
        {
            std::cout << "C::funcC()的this指针是：" << this << std::endl;
        }
        void funcB()
        {
            std::cout << "C::funcB()的this指针是：" << this << std::endl;
        }
};

int main()
{
    // this指针调整： 一帮出现在多重继承中
    std::cout << sizeof(A) << std::endl;    // 4
    std::cout << sizeof(B) << std::endl;    // 4
    std::cout << sizeof(C) << std::endl;    // 12
    
    C myc;
    myc.funcC();
    myc.funcB();    // 这里已经被子类覆盖了
    myc.B::funcB();
    myc.funcA();

    // A::A()的this指针是？0x7ffcd077136c
    
    // B::B的this�针是：0x7ffcd0771370
    
    // C::C的this指针是：0x7ffcd077136c
    
    // C::funcC()的this指针是：0x7ffcd077136c
    
    // C::funcB()的this指针是：0x7ffcd077136c
    
    // B::funcB()的this指针是：0x7ffcd0771370
    
    // A::funcA()的this指针是：0x7ffcd077136c
    
    // 可以看到C的this指针和A的thi指针一样
    // 70 - 6c = 4 刚好是A类子对象的尺寸
    
    // 派生类对象它是包含基类子对象的
    // 如果派生类只从一个基类继承的话，那么这个派生类对象的地址和基类子对象的地址相同
    // 但是如果派生类对象同时继承多个基类，那么要注意
    // 第一个基类子对象的开始地址和派生类对象的开始地址相同
    // 后续这些基类子对象的开始地址和派生类对象的开始地址相差多少呢？
    // 那就得把前面基类子对象所占用的内存空间去掉
    
    // 总结：
    // 你调用了哪个子类的成员函数，那么这个this指针就会被编译器调整到对象内存布局中对应的该子类对象的起始地址上去

    return 0;
}

```