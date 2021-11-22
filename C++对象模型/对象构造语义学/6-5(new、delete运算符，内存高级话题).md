```c++

#include "pch.h"
#include <iostream>
#include <vector>
#include <ctime>
#include<algorithm>
using namespace std;

namespace _nmsp1
{
	//一：特别说明：《C++从入门到精通 C++98/11/14/17》第七章内容融入到本门课程中来
	//二：malloc来分配0个字节
	//老手程序员和新手程序员最大区别：老手程序员对于不会或者没闹明白的东西可以不去用，但是一般不会用错；
	   //新手程序员正好相反，他发现系统没有报什么异常他就觉得这种用法是正确的；
	//即便malloc(0)返回的是一个有效的内存地址，你也不要去动这个内存，不要修改内容，也不要去读；


	void func()
	{			
		void *p = malloc(100); //new调用的也是malloc，所以
		//char *p = new char[0];
		char *q = (char *)p;
		strcpy_s(q, 100, "这里是一个测试"); //这行导致程序出现暗疾和隐患；
		free(p);


		int abc;
		abc = 1;
	}
}

int main()
{	
	_nmsp1::func();	
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
#include <string>

namespace _nmsp1
{
    // malloc来分配0个字节
    
    
    
    
    void func()
    {
        void *p = malloc(0);    // new 调用的也是malloc，所以
        char *pp = new char[0]; // 这种写法也是分配 0 字节
        
        std::cout << static_cast<void*>(&p) << std::endl;
        std::cout << static_cast<void*>(&pp) << std::endl;
        // 0x7ffc52eedb48
        // 0x7ffc52eedb50
        
        char *q = (char*)p;
        // strcpy(q, 100, "test_____");  // 这种写法，在这里就不能
        
        // 可以看到编译器对于malloc(0)确实给我们分配返回了一个地址，但是因为他是0字节内存，
        // 所以这个，不代表我们就能去使用，更不能去修改这个地址中的内容
        
        free(p);
        delete pp;
    }
}

int main()
{
    _nmsp1::func();

    return 0;
}

```

