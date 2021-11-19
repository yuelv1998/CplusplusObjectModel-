```c++
// project100.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。
//

#include "pch.h"
#include <iostream>
#include <time.h >
#include <stdio.h>
#include <vector>

using namespace std;

class Base
{
public:
	virtual void f() { cout << "Base::f()" << endl; }
	virtual void g() { cout << "Base::g()" << endl; }
	virtual void h() { cout << "Base::h()" << endl; }	
	virtual ~Base() {}	
};

class Derive :public Base {
public:	
	virtual void g() { cout << "Derive::g()" << endl; }
	void myselffunc() {} //只属于Derive的函数
	virtual ~Derive() {}
};

int main()
{	
	//一：RTTI（运行时类型识别）简单回顾
	Base *pb = new Derive(); 
	pb->g();

	Derive myderive;
	Base &yb = myderive;
	yb.g();

	//c++运行时类型识别RTTI，要求父类中必须至少有一个虚函数；如果父类中没有虚函数，那么得到RTTI就不准确；
	//RTTI就可以在执行期间查询一个多态指针，或者多态引用的信息了；
	//RTTI能力靠typeid和dynamic_cast运算符来体现；
	cout << typeid(*pb).name() << endl;
	cout << typeid(yb).name() << endl;

	Derive *pderive = dynamic_cast<Derive *>(pb);
	if (pderive != NULL)
	{
		cout << "pb实际是一个Derive类型" << endl;
		pderive->myselffunc(); //调用自己专属函数
	}

	//二：RTTI实现原理
	//typeid返回的是一个常量对象的引用，这个常量对象的类型一般是type_info（类）；
	const std::type_info &tp = typeid(*pb);
	Base *pb2 = new Derive();
	Base *pb3 = new Derive();
	const std::type_info &tp2 = typeid(*pb2);
	const std::type_info &tp3 = typeid(*pb3);



	cout << typeid(int).name();

	if (tp == tp2)
	{
		cout << "很好，类型相同" << endl;
	}

	//其他用法，静态类型；不属于多态类型
	/*cout << typeid(int).name() << endl;
	cout << typeid(Base).name() << endl;
	cout << typeid(Derive).name() << endl;
	Derive *pa3 = new Derive();
	cout << typeid(pa3).name();*/

	//Base *pb = new Derive();
	//Derive myderive;
	//Base &yb = myderive;
	//cout << typeid(*pb).name() << endl; //class Derive
	//cout << typeid(yb).name() << endl; //class Derive
	//Base *pb2 = new Derive();
	//const std::type_info &tp2 = typeid(*pb2);

	//RTTI的测试能力跟基类中是否u才能在虚函数表有关系，如果基类中没有虚函数，也就不存在基类的虚函数表，RTTI就无法得到正确结果；

	printf("tp2地址为:%p\n", &tp2);
	long *pvptr = (long *)pb2;
	long *vptr = (long *)(*pvptr);
	printf("虚函数表首地址为:%p\n", vptr);
	printf("虚函数表首地址之前一个地址为:%p\n", vptr-1); //这里的-1实际上是往上走了4个字节

	long *prttiinfo = (long *)(*(vptr - 1));
	prttiinfo += 3; //跳过12字节
	long * ptypeinfoaddr = (long *)(*prttiinfo);
	const std::type_info *ptypeinfoaddrreal = (const std::type_info *)ptypeinfoaddr;
	printf("ptypeinfoaddrreal地址为:%p\n", ptypeinfoaddrreal);
	cout << ptypeinfoaddrreal->name() << endl; 


	//三：vptr,vtbl,rtti的type_info信息 构造时机
	//rtti的type_info信息:编译后就存在了；写到了可执行文件中,也是跟着类走的

	//总结一下：各个编译器实现有一定差异，但总体都是以虚函数表开始地址为突破口；

	
	return 1;
}
```

![5-8-1](../img/5-8-1.png)

![5-8-2](../img/5-8-2.png)



```c++
/******************************************************************************

Welcome to GDB Online.
GDB online is an online compiler and debugger tool for C, C++, Python, Java, PHP, Ruby, Perl,
C#, VB, Swift, Pascal, Fortran, Haskell, Objective-C, Assembly, HTML, CSS, JS, SQLite, Prolog.
Code, Compile, Run and Debug online from anywhere in world.

*******************************************************************************/
#include <stdio.h>
#include <iostream>

class Base
{
public:
    virtual void f()
    {
        std::cout << "Base::f()" << std::endl;
    }
    virtual void g()
    {
        std::cout << "Base::g()" << std::endl;
    }
    virtual void h()
    {
        std::cout << "Base::h()" << std::endl;
    }
    virtual ~Base()
    {
        std::cout << "Base::virtual ~Base()" << std::endl;
    }
};

class Derive : public Base
{
public:
    virtual void g()
    {
        std::cout << "Derive::g()" << std::endl;
    }
    void myselfFunc(){
        
    }
    virtual ~Derive()
    {
        std::cout << "Derive::virtual ~Derive()" << std::endl;
    }
};

int main()
{
    // RTTI（运行时类型识别）
    Base *pb = new Derive();
    pb->g();
    
    Derive myderive;
    Base &yb = myderive;
    yb.g();
    
    // C++运行时类型识别RTTI，要想RTTI发挥作用，必须父类中至少要有一个虚函数
    // 如果父类中没有虚函数，那么得到的RTTI就不准确，那就没有意义了
    // RTTI的能力考typeid和dynamic_cast运算符体现
    std::cout << typeid(*pb).name() << std::endl;
    // 6Derive
    std::cout << typeid(yb).name() << std::endl;
    // 6Derive
    
    Derive *pderive = dynamic_cast<Derive*>(pb);
    if (pderive != NULL)
    {
        std::cout << "转成功了。说明pb实际是Derive*类型" << std::endl;
        pderive->myselfFunc();
    }
    
    
    // RTTI实现原理
    // c++引入RTTI的目的就是为了能够让程序在运行的时候，能够根据基类指针或者基类引用来获得
    // 这个指针或者引用实际指向的类型
    // typeid返回的是一个常量对象的引用，这个常量对象的类型一般是type_info（类）
    // 这个类对象里就记录着这个typeid对应的类型信息
    const std::type_info &tp = typeid(*pb);
    std::cout << tp.name() << std::endl;
    Base *pb2 = new Derive();
    const std::type_info &tp2 = typeid(*pb2);
    if (tp == tp2)
    {
        std::cout << "两者类型相等" << std::endl;
    }
    
    // 其他用法（静态类型，给进去啥，出来啥，不属于多态类型）
    std::cout << typeid(int).name() << std::endl;   // i
    std::cout << typeid(Base).name() << std::endl;
    std::cout << typeid(Derive).name() << std::endl;
    Derive *pp3 = new Derive();
    std::cout << typeid(*pp3).name() << std::endl;
    
    
    // 多态类型
    Base *dp11 = new Derive();
    Derive myderive11;
    Base &yb11 = myderive11;
    
    std::cout << typeid(*dp11).name() << std::endl;
    std::cout << typeid(yb11).name() << std::endl;
    Base *pb22 = new Derive();
    const std::type_info &tp11 = typeid(*dp11);
    const std::type_info &tp22 = typeid(*pb22);
    
    // RTTI的测试能力和基类是否有虚函数表有直接关系，如果基类中没有虚函数，也就不纯在基类的虚函数表
    // 没有虚函数表，RTTI也就无法准确得到结果
    
    // 下面代码有错误 【todo】
    std::cout << "tp22的地址为 = " << (void *)(&tp22) << std::endl;
    // tp22的地址为 = 0x55d9e133cd10
    long *pvptr = (long *)pb22;
    long *vptr = (long *)(*pvptr);
    std::cout << "虚函数表首地址为 = " << (void *)(&vptr) << std::endl;
    std::cout << "虚函数表首地址之前的一个地址为 = " << (void *)(&vptr + 1) << std::endl;
    
    long *pRTTIinfo = (long *)(*(&vptr - 1));
    pRTTIinfo += 3; // linux下跳过 3*8 = 24字节
    long * ptypeInfoAddr = (long *)(*pRTTIinfo);
    const std::type_info *ptypeinfoAddrReal = (const std::type_info *)ptypeInfoAddr;
    std::cout << "ptypeinfoAddrReal地址为 = " << (void *)(&ptypeinfoAddrReal) << std::endl;
    // tp22的地址为 = 0x564f1c88dd10
    // 虚函数表首地址为 = 0x7ffe4df6f1b0
    // 虚函数表首地址之前的一个地址为 = 0x7ffe4df6f1b8
    // ptypeinfoAddrReal地址为 = 0x7ffe4df6f1b8

    // vptr， vtbl, rtti的type_info信息的构造时机
    // rtti的type_info信息，在编译后就存在了，写在了可执行文件中
    
    
    
    
    return 0;
}


```

