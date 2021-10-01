```c++

#include "pch.h"
#include <iostream>
#include <vector>
#include <ctime>

using namespace std;

namespace _nmsp1 //命名空间
{			
	//一：inline函数回顾
	//使用inline之后，编译器内部会有一个比较复杂的测试算法来评估这个inline函数的复杂度；
	//可能会统计这个inline函数中，赋值次数，内部函数调用，虚函数调用等次数 ---权重 
	//(1)开发者写inline只是对编译器的一个建议，但如果编译器评估这个inline函数复杂度过高，这个inline建议就被编译器忽略；
	//(2)如果inline被编译器采纳，那么inline函数的扩展，就要在调用这个inline函数的那个点上进行，
	    //那么可能带来额外的问题比如 参数求值，可能产生临时对象的生成和管理；
	
	//二：inline扩展细节
	//（2.1）形参被对应实参取代
	//（2.2）局部变量的引入(局部变量能少用尽量少用，能不用尽量不用)
	//（2.3）inline失败情形	     

	inline int myfunc(int testv)
	{
		//int tempvalue = testv * (5 + 4) * testv;
		return testv * (5 + 4) * testv;
		//return tempvalue;

		if (testv > 10)
		{
			testv--;
			myfunc(testv);
		}
		return testv;
	}

	/*int rtnvalue()
	{
		return 5;
	}*/

	void func()
	{
		//int i = myfunc(12 + 15);  //编译器会先求值，然后用实参再替换形参
		//int a = 80;
		//int i = myfunc(a + 15); //编译器会先计算a和15的和值，然后再替换掉形参
		//int i = myfunc(rtnvalue() + 15); //编译器会先调用rtnvalue()得到返回值，返回值和15做加法，然后再替换掉形参
		int i = myfunc(12);
		cout << i << endl;		
	}
}

int main()
{
	_nmsp1::func();	
	return 1;
}
```