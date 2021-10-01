```c++

#include "pch.h"
#include <iostream>
#include <vector>
using namespace std;

namespace _nmsp1 //命名空间
{	
	class A
	{
	public:
		int m_i; //成员变量

		A()
		{

		}
		~A()
		{

		}
		//virtual void func() {}
	};
	void func()
	{
		//一：总述与回顾：二章四节，五章二节
		//二：从new说起
		//（2.1）new类对象时加不加括号的差别
			//(2.1.1)如果是个空类，那么如下两种写法没有区别，现实中，你不可能光写一个空类
			//(2.1.2)类A中有成员变量则：
				//带括号的初始化会把一些和成员变量有关的内存清0，但不是整个对象的内存全部清0；
					//《c++对象模型探索》
			//(2.1.3)当类A有构造函数 ，下面两种写法得到的结果一样了；
			//(2.1.4)不同的看上去的感觉

		//（2.2）new干了啥
		//new 可以叫 关键字/操作符 
		//new 干了两个事：一个是调用operator new(malloc)，一个是调用了类A的构造函数
		//delete干了两个事：一个是调用了类A的析构函数，一个是调用operator delete(free)

		//（2.3）malloc干了啥
		//（2.4）总结
		A *pa = new A(); //函数调用
		//....
		delete pa;		

		A *pa2 = new A;
		int *p3 = new int;  //初值随机
		int *p4 = new int(); //初值 0
		int *p5 = new int(100); //初值100
			
		//operator new(120);
		//operator delete();
		//malloc()
		//free();

		int abc;
		abc = 6;
		
	}
}

int main()
{	
	_nmsp1::func();	
	return 1;
}

```