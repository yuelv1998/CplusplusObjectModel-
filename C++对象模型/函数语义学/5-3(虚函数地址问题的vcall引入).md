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

 

### vcall thunk能力

调整this
跳转到真正的虚函数中去

在打印的时候发现pFun的地址和 &(Base::f)的地址竟然不一样太奇怪了？经过一番深入研究，终于把这个问题弄明白了。下面就来一步步进行剖析。

​    根据VC的虚函数的布局机制，上述的布局如下：

![img](../img/SouthEast4)



 然后我们再细细的分析第一种方式：

​     Fun pFun = (Fun)*((int*)*(int*)(d)+0);

d是一个类对象的地址。而在32位机上指针的大小是4字节，因此*(int*)(&d)取得的是vfptr，即虚表的地址。从而*((int*)*(int*)(&d)+0)是虚表的第1项，也就是Base::f()的地址。事实上我们得到了验证，程序运行结果如下：

![img](../img/SouthEast3)



这说明虚表的第一项确实是虚函数的地址，上面的VC虚函数的布局也确实木有问题。

​    但是，接下来就引发了一个问题，为什么&(Base::F）和PFun的值会不一样呢？既然PFun的值是虚函数f的地址，那&(Base::f)又是什么呢？带着这个问题，我们进行了反汇编。

printf("&(Base::f): 0x%x \n", &(Base::f));

   00401068  mov     edi,dword ptr [__imp__printf (4020D4h)] 

   0040106E  push     offset Base::`vcall'{0}' (4013A0h) 

   00401073  push     offset string "&(Base::f): 0x%x \n" (40214Ch) 

   00401078  call     edi 

printf("&(Base::g): 0x%x \n", &(Base::g));

   0040107A  push     offset Base::`vcall'{4}' (4013B0h) 

   0040107F  push     offset string "&(Base::g): 0x%x \n" (402160h) 

   00401084  call     edi 

那么从上面我们可以清楚的看到:

   Base::f 对应于Base::`vcall'{0}' (4013A0h) 

   Base::g对应于Base::`vcall'{4}' (4013B0h)

那么Base::`vcall'{0}'和Base::`vcall'{4}'到底是什么呢，继续进行反汇编分析

Base::`vcall'{0}':

   004013A0  mov     eax,dword ptr [ecx] 

   004013A2  jmp     dword ptr [eax] 

​    ......

Base::`vcall'{4}':

   004013B0  mov     eax,dword ptr [ecx] 

   004013B2  jmp     dword ptr [eax+4] 

   第一句中, 由于ecx是this指针, 而在VC中一般虚表指针是类的第一个成员, 所以它是把vfptr, 也就是虚表的地址存到了eax中. 第二句

相当于取了虚表的某一项。对于Base::f跳转到Base::`vcall'{0}'，取了虚表的第1项；对于Base::g跳转到Base::`vcall'{4}'，取了虚表第2项。由此都能够正确的获得虚函数的地址。

​    由此我们可以看出，vc对此的解决方法是由编译器加入了一系列的内部函数"vcall". 一个类中的每个虚函数都有一个唯一与之对应的vcall函数，通过特定的vcall函数跳转到虚函数表中特定的表项。

   更深一步的进行讨论，考虑多态的情况，将代码改写如下：

 

打印的时候表现出来了多态的性质：

![img](../img/SouthEast)

分析可知原因如下：

![img](../img/SouthEast2)



这是因为类Derive的虚函数表的各项对应的值进行了改写(rewritting)，原来指向Based::f()的地址变成了指向Derive::f()，原来指向Based::g()的地址现在编变成了指向Derive::g()。

反汇编代码如下：

printf("&(Derive::f): 0x%x \n", &(Derive::f));

   00401086  push     offset Base::`vcall'{0}' (4013B0h) 

   0040108B  push     offset string "&(Derive::f): 0x%x \n" (40217Ch) 

   00401090  call     esi 

printf("&(Derive::g): 0x%x \n", &(Derive::g));

   00401092  push     offset Base::`vcall'{4}' (4013C0h) 

   00401097  push     offset string "&(Derive::g): 0x%x \n" (402194h) 

   0040109C  call     esi 

​    因此虽然此时Derive::f依然对应Base::`vcall'{0}',而 Derive::g依然对应Base::`vcall'{4}'，但是由于每个类有一个虚函数表，因此跳转到的虚表的位置也发生了改变，同时因为进行了改写，虚表中的每个slot项的值也不一样。

 

**稍微总结一下：**

在VC中有两种方法调用虚函数，一种是通过虚表，另外一种是通过vcall thunk的方式

**通过虚表的方式**：

​    base *d = new Derive;

​    d->f();

​        004115FA mov     eax,dword ptr [d] 
​       004115FD mov     edx,dword ptr [eax] 
​       004115FF mov     esi,esp 
​       00411601 mov     ecx,dword ptr [d] 
​       00411604 mov     eax,dword ptr [edx] 
​       00411606 call    eax 
​       00411608 cmp     esi,esp 
​       0041160A call    @ILT+470(__RTC_CheckEsp) (4111DBh)

 这种方式的应用环境是通过类对象的指针或引用来调用虚函数



**通过vcall thunk的方式：**

​    typedef void (Base::* func1)( void );

​    base *d = new Derive;

​    func1 pFun1 = &Base::f;

​    (d->*pFun1)();

​         004115A9 mov     dword ptr [pFun1],offset Base::`vcall'{0}' (4110C3h) 
​        004115B0 mov     esi,esp 
​        004115B2 lea     ecx,[d] 
​        004115B5 call      dword ptr [pFun1] 
​        004115B8 cmp     esi,esp 
​        004115BA call    @ILT+460(__RTC_CheckEsp) (4111D1h) 