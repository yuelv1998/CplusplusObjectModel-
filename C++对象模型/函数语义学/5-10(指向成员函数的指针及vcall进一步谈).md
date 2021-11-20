```c++

#include "pch.h"
#include <iostream>
#include <vector>
#include <ctime>

using namespace std;

namespace _nmsp1 //命名空间
{	
	//一：指向成员函数的指针
	//成员函数地址，编译时就确定好的。但是，调用成员函数，是需要通过对象来调用的；
	//所有常规（非静态）成员函数，要想调用，都需要 一个对象来调用它；

	class A
	{
	public:
		void myfunc1(int tempvalue1)
		{
			cout << "tempvalue1 = " << tempvalue1 << endl;
		}
		void myfunc2(int tempvalue2)
		{
			cout << "tempvalue2 = " << tempvalue2 << endl;
		}

		static void mysfunc(int tempvalue)
		{
			cout << "A::mysfunc()静态成员函数--tempvalue = " << tempvalue << endl;
		}
	};

	void func()
	{
		A mya;
		void (A::*pmypoint)(int tempvalue) = &A::myfunc1; //定义一个成员函数指针并给初值
		pmypoint = &A::myfunc2; //给成员函数指针赋值

		(mya.*pmypoint)(15); //通过成员函数指针来调用成员函数，必须要通过对象的介入才能调用

		A *pmya = new A();
		(pmya->*pmypoint)(20); //用对象指针介入来使用成员函数指针 来调用成员函数

		//编译器视角
		//pmypoint(&mya, 15);
		//pmypoint(pmya, 20);
		void(*pmyspoint)(int tempvalue) = &A::mysfunc; //一个普通的函数指针，而不是成员函数指针
		pmyspoint(80);

		//通过成员函数指针对常规的成员函数调用的成本，和通过普通的函数指针来调用静态成员函数，成本上差不多；

		
	}
}
namespace _nmsp2
{
	//二：指向虚成员函数的指针及vcall进一步谈
	//vcall (vcall trunk) = virtual call：虚调用
	//它代表一段要执行的代码的地址，这段代码引导咱们去执行正确的虚函数，
	  //或者我们直接把vcall看成虚函数表，如果这么看待的话，那么vcall{0}代表的就是虚函数表里的第一个函数，
	     //vcall{4}就代表虚函数表里的第二个虚函数

	//完善理解：&A::myvirfunc：打印出来的是一个地址，这个地址中有一段代码，这个代码中记录的是该虚函数在虚函数表中的一个偏移值
	 //有了这个偏移值，再有了具体的对象指针，我们就能够知道调用的是哪个虚函数表里边的哪个虚函数了；

	//成员函数指针里，保存的可能是一个vcall(vcall trunk)地址（虚函数）,要么也可能是一个真正的成员函数地址
		//如果是一个vcall地址，那vcall能够引导编译器找出正确的虚函数表中的虚函数地址进行调用；

	

	class A
	{
	public:
		void myfunc1(int tempvalue1)
		{
			cout << "tempvalue1 = " << tempvalue1 << endl;
		}
		void myfunc2(int tempvalue2)
		{
			cout << "tempvalue2 = " << tempvalue2 << endl;
		}

		static void mysfunc(int tempvalue)
		{
			cout << "A::mysfunc()静态成员函数--tempvalue = " << tempvalue << endl;
		}

		virtual void myvirfuncPrev(int tempvalue)
		{
			cout << "A::myvirfuncPrev()虚成员函数--tempvalue = " << tempvalue << endl;
		}

		virtual void myvirfunc(int tempvalue)
		{
			cout << "A::myvirfunc()虚成员函数--tempvalue = " << tempvalue << endl;
		}

	};	

	void func()
	{
		void (A::*pmyvirfunc)(int tempvalue) = &A::myvirfunc; //成员函数指针  -- vcall(vcall trunk)地址（虚函数）

		A *pvaobj = new A;
		pvaobj->myvirfunc(190);
		(pvaobj->*pmyvirfunc)(190);
		printf("%p\n", &A::myvirfunc);

		pmyvirfunc = &A::myfunc2;  //真正的成员函数地址
		(pvaobj->*pmyvirfunc)(33);

		delete pvaobj;		

	}
}
namespace _nmsp3
{
	//三：vcall在继承关系中的体现
	class A
	{
	public:
		/*void myfunc1(int tempvalue1)
		{
			cout << "tempvalue1 = " << tempvalue1 << endl;
		}
		void myfunc2(int tempvalue2)
		{
			cout << "tempvalue2 = " << tempvalue2 << endl;
		}

		static void mysfunc(int tempvalue)
		{
			cout << "A::mysfunc()静态成员函数--tempvalue = " << tempvalue << endl;
		}

		virtual void myvirfuncPrev(int tempvalue)
		{
			cout << "A::myvirfuncPrev()虚成员函数--tempvalue = " << tempvalue << endl;
		}
*/
		virtual void myvirfunc(int tempvalue)
		{
			cout << "A::myvirfunc()虚成员函数--tempvalue = " << tempvalue << endl;
		}
		 virtual ~A()
		{

		}
	};

	class B :public A
	{
	public:
		virtual void myvirfunc(int tempvalue)
		{
			cout << "B::myvirfunc()虚成员函数--tempvalue = " << tempvalue << endl;
		}
		virtual ~B() {}
	};

	void func()
	{
		B *pmyb = new B();   //pmyb：对象指针
		void (B::*pmyvirfunc)(int tempvalue) = &B::myvirfunc; //成员函数指针
		(pmyb->*pmyvirfunc)(190);

		printf("%p\n", &A::myvirfunc); //vcall地址 和下个vcall地址不一样
		printf("%p\n", &B::myvirfunc);
	}
}

int main()
{
	//_nmsp1::func();
	//_nmsp2::func();
	_nmsp3::func();
	
	return 1;
}

```







```c++
/******************************************************************************

Welcome to GDB Online.
GDB online is an online compiler and debugger tool for C, C++, Python, Java, PHP, Ruby, Perl,
C#, VB, Swift, Pascal, Fortran, Haskell, Objective-C, Assembly, HTML, CSS, JS, SQLite, Prolog.
Code, Compile, Run and Debug online from anywhere in world.

*******************************************************************************/
#include <stdio.h>
#include <iostream>


namespace _nmsp1
{
    // 指向成员函数的指针
    // 成员函数地址是在编译期间就确定好了的，但是调用的时候是需要通过对象来调用的，
    // 因为成员函数的运行依赖于this指针，这个this指针就是对象首地址
    // 所有常规（非静态）成员函数，要想调用，都需要一个对象来调用它
    
    class A
    {
    public:
        void myfunc1(int tempvalue1)
        {
            std::cout << "tempvalue1 = " << tempvalue1 << std::endl;
        }
        
        void myfunc2(int tempvalue2)
        {
            std::cout << "tempvalue2 = " << tempvalue2 << std::endl;
        }
        
        static void staticFunc(int staticValue)
        {
            std::cout << "A::staticFunc静态成员函数 staticValue = " << staticValue << std::endl;
        }
    };
    
    
    void func()
    {
        A mya;
        void (A::* pfuncA)(int temp) = &A::myfunc1; // 定义一个成员函数指针并给初值
        pfuncA = &A::myfunc2;   // 给成员函数指针赋值
        
        // 通过成员函数指针来调用成员函数，必须要通过对象的介入才能调用
        (mya.*pfuncA)(1990);
        // 编译器视角(把对象作为this指针实参传递进去)
        // pfuncA(mya, 1990);
        
        
        // 用对象指针介入来使用成员函数指针来调用成员函数
        A *pmya = new A();
        (pmya->*pfuncA)(9989);
        // 编译器视角（把对象指针作为this指针实参传递进去）
        // pfuncA(&pmya, 9989);
        
        
        // 通过普通函数指针来调用静态成员函数
        void (*ptfunc)(int temp) = &A::staticFunc;  // 普通的函数指针【不是成员函数指针】
        ptfunc(10086);
        // A::staticFunc静态成员函数 staticValue = 10086
        
        
        ((A*)0)->staticFunc(11111);
        // A::staticFunc静态成员函数 staticValue = 11111
        
        // 通过成员函数指针对常规的成员函数的调用成本 和 通过普通函数指针来调用静态成员函数的成本差不多
        
        
        
    }
}

namespace _nmsp2
{
    // 指向虚成员函数的指针以及vcall进一步谈
    // vcall（vcall trunk） = virtual call (虚调用)
    // 他代表一段要执行的代码的地址，这段代码引导我们去执行正确的虚函数
    // 或者我们直接把vcall看成虚函数表，如果这么看待，那么vcall[0]就代表虚函数表里的第一个虚函数
    // vcall[4]就代表虚函数表里的第二个虚函数，因为跳4字节，每个指针是4字节
    // 完善理解：
    // &A::virtualFunc; 打印出来的是一个地址，这个地址中有一段代码，这个代码中记录的是该虚函数在虚函数表中的一个偏移值
    // 有了这个偏移值，再有具体的对象指针，就能够知道调用的是哪个虚函数表里的哪个虚函数了
    // 因为首先：对象指针提供虚函数表信息，然后vcall里的提供虚函数的偏移值信息，结合这两个就能知道是哪个虚函数了
    
    
    // 成员函数指针里，保存的可能是一个vcall（vcall trunk）地址【虚函数】，
    // 也可能保存的是一个真正的成员函数地址
    // 如果是一个vcall地址，那么vcall能够引导编译器找出真正的虚函数表中的虚函数地址进行调用
    
    
    class A
    {
    public:
        void myfunc1(int tempvalue1)
        {
            std::cout << "tempvalue1 = " << tempvalue1 << std::endl;
        }
        
        void myfunc2(int tempvalue2)
        {
            std::cout << "tempvalue2 = " << tempvalue2 << std::endl;
        }
        
        static void staticFunc(int staticValue)
        {
            std::cout << "A::staticFunc静态成员函数 staticValue = " << staticValue << std::endl;
        }
        
        virtual void virtualFunc(int tmpvalue)
        {
            std::cout << "A::virtualFunc 虚成员函数 tmpvalue = " << tmpvalue << std::endl;
        }
    };
    
    
    void func()
    {
        void (A::*virPfunc)(int value) = &A::virtualFunc;
        A *paobj = new A();
        // 走虚函数表指针查询虚函数表调用虚函数
        paobj->virtualFunc(100);
        
        // 利用成员函数指针来进行调用【这种方式依然是走的虚函数表的查询】
        (paobj->*virPfunc)(990);    // 保存的是vcall trunk地址
        // A::virtualFunc 虚成员函数 tmpvalue = 990
        
        virPfunc = &A::myfunc2;         // 成员函数指针保存的是真正的成员函数地址
        (paobj->*virPfunc)(89389);
        // tempvalue2 = 89389
        
        
        delete paobj;
    }
}

namespace _nmsp3
{
    // vcall在继承关系中的体现
    
    class A
    {
    public:
        void myfunc1(int tempvalue1)
        {
            std::cout << "tempvalue1 = " << tempvalue1 << std::endl;
        }
        
        void myfunc2(int tempvalue2)
        {
            std::cout << "tempvalue2 = " << tempvalue2 << std::endl;
        }
        
        static void staticFunc(int staticValue)
        {
            std::cout << "A::staticFunc静态成员函数 staticValue = " << staticValue << std::endl;
        }
        
        virtual void virtualFunc(int tmpvalue)
        {
            std::cout << "A::virtualFunc 虚成员函数 tmpvalue = " << tmpvalue << std::endl;
        }
        virtual ~A()
        {}
    };
    
    class B:public A
    {
    public:
        virtual void virtualFunc(int tmpvalue)
        {
            std::cout << "B::virtualFunc 虚成员函数 tmpvalue = " << tmpvalue << std::endl;
        }
        virtual ~B()
        {}
        
    };
    
    
    void func()
    {
        B *pmb = new B();   // 对象指针
        void (B::*pbfunc)(int value) = &A::virtualFunc; 
        // 成员函数指针，现在初始化给的是基类的虚函数地址
        
        (pmb->*pbfunc)(1000);
        // B::virtualFunc 虚成员函数 tmpvalue = 1000
        
        
    }
}


int main()
{
    // _nmsp1::func();
    
    // _nmsp2::func();
    
    _nmsp3::func();
    
    
    return 0;
}


```

