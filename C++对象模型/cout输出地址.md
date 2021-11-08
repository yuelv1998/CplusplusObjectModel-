先给出通过字符型指针输出字符串的示例代码，如下：

```c

#include <iostream>
using std::cout;
using std::endl;
 
int main()
{
    const char *pszStr = "this is a string";
 
    // 输出字符串
    cout << "字符串：" << pszStr << endl;
 
    // 显然不会输出地址值
    cout << "字符串起始地址值： " << pszStr << endl;
 
    return 0;
}
```

对于要使用cout输出字符串指针地址值，我们可能会产生困惑。曾经我们使用C标准库中的printf函数是如此的方便：

```c++

#include <stdio.h>
 
int main()
{
    const char *pszStr = "this is a string";
 
    // 输出字符串
    printf("字符串：%s\n", pszStr);
 
    // 输出地址值
    printf("字符串起始地址值：%p\n", pszStr);
    return 0;
}
```

醒醒吧，咱们要写的是C++代码，不要总是抓着C不放。由于C++标准库中I/O类对<<操作符重载，因此在遇到字符型指针时会将其当作字符串名来处理，输出指针所指的字符串。既然这样，那么我们就别让它知道那是字符型指针，所以得用到强制类型转换，不过不是C的那套，我们得用static_cast来实现，把字符串指针转换成无类型指针，这样更规范，如下：

```c++

#include <iostream>
using std::cout;
using std::endl;
 
int main()
{
    const char *pszStr = "this is a string";
     
    // 输出字符串
    cout << "字符串：" << pszStr << endl;
                
    // 如我们所愿，输出地址值
    cout << "字符串起始地址值： " << static_cast<const void *>(pszStr) << endl;
 
    return 0;
}
```

