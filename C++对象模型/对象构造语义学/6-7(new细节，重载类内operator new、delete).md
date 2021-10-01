```c++

#include "pch.h"
#include <iostream>
#include <vector>
using namespace std;

namespace _nmsp1 //命名空间
{
	//一：new内存分配细节探秘
	//我们注意到，一块内存的回收，影响范围很广，远远不是10个字节，而是一大片。
	//分配内存这个事，绝不是简单的分配出去4个字节，而是在这4个字节周围，编译器做了很多处理，比如记录分配出去的字节数等等；
	//分配内存时，为了记录和管理分配出去的内存，额外多分配了不少内存，造成了浪费；
	//尤其是你频繁的申请小块内存时，造成的浪费更明显，更严重


	//new ,delete(malloc,free)内存没有看上去那么简单，他们的工作内部是很复杂的；

	void func()
	{
		char *ppoint = new char[10];
		memset(ppoint, 0, 10);
		delete[] ppoint; 

		/*int *ppointint = new int(10);
		*ppointint = 2000000000;
		delete ppointint;*/
		
	}
}
namespace _nmsp2 //命名空间
{
	//二：重载类中的operator new和operator delete操作符
	/*void *temp = operator new(sizeof(A));
	A *pa = static_cast<A*>(temp);
	pa->A::A();*/
	/*pa->A::~A();
	operator delete(pa);*/

	class A
	{
	public:
		static void *operator new(size_t size);  //《c++对象模型探索》
		static void operator delete(void *phead);
		//int m_i = 0;
		A()
		{
			int abc;
			abc = 10;
		}
		~A()
		{
			int abc;
			abc = 1;
		}
	};
	void *A::operator new(size_t size)
	{
		//.....
		A *ppoint = (A *)malloc(size);
		return ppoint;
	}
	void A::operator delete(void *phead)
	{
		//...
		free(phead);
	}

	void func()
	{
		/*A *pa = new A();		
		delete pa;*/
		A *pa = ::new A();  //::全局操作符
		::delete pa;
	}
}
namespace _nmsp3
{
	//三：重载类中的operator new[]和operator delete[]操作符
	class A
	{
	public:
		//static void *operator new(size_t size);  //《c++对象模型探索》
		//static void operator delete(void *phead);
		//int m_i = 0;
		
		static void *operator new[](size_t size);  //《c++对象模型探索》
		static void operator delete[](void *phead);

		A()
		{
			int abc;
			abc = 10;
		}
		~A()
		{
			int abc;
			abc = 1;
		}
	};
	void *A::operator new[](size_t size)
	{
		//.....
		A *ppoint = (A *)malloc(size);
		return ppoint;
	}
	void A::operator delete[](void *phead)
	{
		//...
		free(phead);
	}
	
	void func()
	{
		/*A *pa = new A(); 
		delete pa;*/

		A *pa = new A[3](); //构造和析构函数被调用3次，但是operator new[]和operator delete[]仅仅被调用一次；
		delete[] pa;


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