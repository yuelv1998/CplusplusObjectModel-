```c++
// project100.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。
//

#include "pch.h"
#include <iostream>
#include <time.h >
#include <stdio.h>
#include <vector>

using namespace std;

class MYACLS
{
public:
	virtual void myvirfunc1()
	{

	}
	virtual void myvirfunc2()
	{

	}
};

int main()
{
	//打印的是vcall函数（代码）地址，而不是真正的虚函数；
	printf("MYACLS::myvirfunc1()地址=%p\n", &MYACLS::myvirfunc1); 
	printf("MYACLS::myvirfunc2()地址=%p\n", &MYACLS::myvirfunc2);
	cout << sizeof(MYACLS) << endl;
	//调用虚函数地址和打印的并不一样是因为打印的是vcall函数地址
	MYACLS *pmyobj = new MYACLS();

	//vcall thunk;
	//(1)调整this
	//(2)跳转到真正的虚函数中去



	return 1;
}

```