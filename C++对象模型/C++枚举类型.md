## C++ 枚举类型详解

**枚举类型的定义：**枚举类型(enumeration)是 C++ 中的一种派生数据类型，它是由用户定义的若干枚举常量的集合。

定义格式：枚举类型的定义格式为：

```c++
enum <类型名> {<枚举常量表>};
```

**格式说明：**

- 关键字enum——指明其后的标识符是一个枚举类型的名字。
- 枚举常量表——由枚举常量构成。"枚举常量"或称"枚举成员"，是以标识符形式表示的整型量，表示枚举类型的取值。枚举常量表列出枚举类型的所有取值，各枚举常量之间以"，"间隔，且必须各不相同。取值类型与条件表达式相同。

**应用举例：**

```c++
enum color_set1 {RED, BLUE, WHITE, BLACK}; // 定义枚举类型color_set1
enum week {Sun, Mon, Tue, Wed, Thu, Fri, Sat}; // 定义枚举类型week
```

**重要提示：**

枚举常量代表该枚举类型的变量可能取的值，编译系统为每个枚举常量指定一个整数值，默认状态下，这个整数就是所列举元素的序号，序号从0开始。 可以在定义枚举类型时为部分或全部枚举常量指定整数值，在指定值之前的枚举常量仍按默认方式取值，而指定值之后的枚举常量按依次加1的原则取值。 各枚举常量的值可以重复。例如：

```c++
enum fruit_set {apple, orange, banana=1, peach, grape}
//枚举常量apple=0,orange=1, banana=1,peach=2,grape=3。
enum week {Sun=7, Mon=1, Tue, Wed, Thu, Fri, Sat};
//枚举常量Sun,Mon,Tue,Wed,Thu,Fri,Sat的值分别为7、1、2、3、4、5、6。
```

枚举常量只能以标识符形式表示，而不能是整型、字符型等文字常量。例如，以下定义非法：

```c++
enum letter_set {'a','d','F','s','T'}; //枚举常量不能是字符常量
enum year_set{2000,2001,2002,2003,2004,2005}; //枚举常量不能是整型常量
```

可改为以下形式则定义合法：

```c++
enum letter_set {a, d, F, s, T};
enum year_set{y2000, y2001, y2002, y2003, y2004, y2005};
```

------

## 枚举变量的使用

定义枚举类型的主要目的是：增加程序的可读性。枚举类型最常见也最有意义的用处之一就是用来描述状态量，这点将在第9章 输入输出流类看到。

定义格式：定义枚举类型之后，就可以定义该枚举类型的变量，如：

```c++
color_set1 color1, color2;
```

亦可类型与变量同时定义（甚至类型名可省），格式如下：

```c++
enum {Sun,Mon,Tue,Wed,Thu,Fri,Sat} weekday1, weekday2;
```

### 相关操作

枚举变量的值只能取枚举常量表中所列的值，就是整型数的一个子集。

枚举变量占用内存的大小与整型数相同。

枚举变量只能参与赋值和关系运算以及输出操作，参与运算时用其本身的整数值。例如，设有定义：

```c++
enum color_set1 {RED, BLUE, WHITE, BLACK} color1, color2;
enum color_set2 { GREEN, RED, YELLOW, WHITE} color3, color4;
```

则允许的赋值操作如下：

```c++
color3=RED;           //将枚举常量值赋给枚举变量
color4=color3;        //相同类型的枚举变量赋值，color4的值为RED
int  i=color3;        //将枚举变量赋给整型变量，i的值为1
int  j=GREEN;         //将枚举常量赋给整型变量，j的值为0
```

允许的关系运算有：==、<、>、<=、>=、!=等，例如：

```c++
//比较同类型枚举变量color3，color4是否相等
if (color3==color4) cout<<"相等"；
//输出的是变量color3与WHITE的比较结果，结果为1
cout<< color3<WHITE;
```

枚举变量可以直接输出，输出的是变量的整数值。例如：

```c++
cout<< color3;         //输出的是color3的整数值，即RED的整数值1
```

------

## 实例

口袋中有红、黄、蓝、白、黑五种颜色的球若干个，每次从口袋中取三个不同颜色的球，统计并输出所有的取法。

**分析：**由于球只能是五种颜色之一，故可用枚举类型表示球的颜色。设取出的球为i、j、k，根据题意，i、j、k分别可以有五种取值，且i≠j≠k。可以用穷举法，逐个检验每一种可能的组合，从中找出符合要求的组合并输出。

## 实例

```c++
#include<iostream>
#include<iomanip>
using namespace std;
int main(){
    enum color_set {red,yellow,blue,white,black}; //声明枚举类型color
    color_set color; 
    int i,j,k,counter=0,loop; //counter是累计不同颜色的组合数
    for(i=red;i<=black;i++) {
        for(j=red;j<=black;j++) {
            if(i!=j){                        //前两个球颜色不同
                for(k=red;k<=black;k++)
                if(k!=i&&k!=j){        //第三个球不同于前两个，满足要求
                    counter++;
                    if((counter)%22==0){ //每屏显示22行
                        cout<<"请按回车键继续";
                        cin.get();
                    }
                    cout<<setw(15)<<counter;
                    /*下面输出每种取法，一行为一种取法的三个颜色*/
                    for(loop=1;loop<=3;loop++){
                        switch(loop){
                            case 1: color=(color_set) i; break;    //第一个是i
                            case 2: color=(color_set) j; break;    //第二个是j
                            case 3: color=(color_set) k; break;    //第三个是k
                        }
                        switch(color){
                            case red:   cout<<setw(15)<<"red";   break;
                            case yellow:cout<<setw(15)<<"yellow";break;
                            case blue:  cout<<setw(15)<<"blue";  break;
                            case white:    cout<<setw(15)<<"white"; break;
                            case black: cout<<setw(15)<<"black"; break;
                        }
                    }
                    cout<<endl;            //输出一种取法后换行
                }
            }
        }
    }
    cout<<"共有："<<counter<<"种取法"<<endl;
    return 0;
}
```



### 重要提示

- 枚举变量可以直接输出，但不能直接输入。如：cout >> color3;  //非法
- 不能直接将常量赋给枚举变量。如： color1=1; //非法
- 不同类型的枚举变量之间不能相互赋值。如： color1=color3; //非法
- 枚举变量的输入输出一般都采用switch语句将其转换为字符或字符串；枚举类型数据的其他处理也往往应用switch语句，以保证程序的合法性和可读性。