```c++

#include "pch.h"
#include <iostream>
#include <vector>
#include <ctime>
using namespace std;

/*
void *operator new(size_t size)
{
	return malloc(size);
}
void *operator new[](size_t size) //数组版本
{
	return malloc(size);
}
void operator delete(void *phead)
{
	free(phead);
}
void operator delete[](void *phead)
{
	free(phead);
}
*/

namespace _nmsp1 //命名空间
{	
	//一：重载全局operator new和operator delete函数
	   //重载全局operator new[]和operator delete[]函数
	class A
	{
	public:
		int m_i;
		int m_j;
		A()
		{
			cout << "A::A()" << endl;
		}
		~A()
		{
			cout << "A::~A()" << endl;
		}


		void *operator new(size_t size)
		{
			A *ppoint = (A*)malloc(size);
			return ppoint;
		}
		void *operator new[](size_t size) //数组版本
		{
			A *ppoint = (A*)malloc(size);
			return ppoint;
		}
		void operator delete(void *phead)
		{
			free(phead);
		}
		void operator delete[](void *phead)
		{
			free(phead);
		}
	};

	void func()
	{
		/*int *pint = new int(12); 
		delete pint;

		char *parr = new char[15];
		delete[] parr;
*/
		A *p = new A();
		delete p;

		A *pa = new A[3]();
		delete[] pa;
		
		
	}
}
namespace _nmsp2
{
	//二：定位new（placement new）
	//有placement new，但是没有对应的placement delete
	//功能：在已经分配的原始内存中初始化一个对象；
	 //a)已经分配，定位new并不分配内存，你需要提前将这个定位new要使用的内存分配出来
	 //b)初始化一个对象（初始化一个对象的内存），我们就理解成调用这个对象的构造函数；
	//说白了，定位new就是能够在一个预先分配好的内存地址中构造一个对象；
	//格式：
	//new (地址) 类类型()

	class A
	{
	public:
		int m_a;
		A() :m_a(0)
		{
			int test;
			test = 1;
		}
		A(int tempvalue) :m_a(tempvalue)
		{
			int test;
			test = 1;
		}
		~A()
		{
			int abc;
			abc = 1;
		}

		//传统new操作符重载
		void *operator new(size_t size)
		{
			A *ppoint = (A*)malloc(size);
			return ppoint;
		}

		//定位new操作符的重载
		void *operator new(size_t size,void *phead)
		{
			//A *ppoint = (A*)malloc(size);
			//return ppoint;
			return phead; //收到内存开始地址，只需要原路返回
		}

	};
	   	 
	void func()
	{
		void *mymemPoint = (void *)new char[sizeof(A)]; //内存必须事先分配出来
		A *pmyAobj1 = new (mymemPoint) A(); //调用无参构造函数，这里并不会额外分配内存

		//A *pmyAobj3 = new A();

		/*void *mymemInt = (void *)new int; 
		int  *pmyint = new (mymemPoint) int();
		*/
		
		void *mymemPoint2 = (void *)new char[sizeof(A)];
		A *pmyAobj2 = new (mymemPoint2) A(12); //调用带一个参数的构造函数，这里并不会额外分配内存

		//delete pmyAobj1;
		//delete pmyAobj2;

		pmyAobj1->~A(); //手工调用析构函数是可以的，但手工调用构造函数一般不可以
		pmyAobj2->~A();
		delete[](void *)pmyAobj1;
		delete[](void *)pmyAobj2;

	}
}
namespace _nmsp3
{
	//三：多种版本的operator new重载
	//可以重载很多版本的operator new，只要每个版本参数不同就行，但是第一个参数是固定的，
	//都是size_t，表示你要new对象的sizeof值

	class A
	{
	public:
		void *operator new(size_t size, int tvp1, int tvp2)
		{
			return NULL;
		}
		A()
		{
			int test;
			test = 1;
		}
	};
	void func()
	{
		A *pa =  new (1234, 56) A(); //自定义 new 不调用构造函数

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