# Chapter 7

## 7.1.4
类类型变量在定义时没有显式初始化，则会调用默认构造函数。如果没有默认构造函数，则定义该类型对象时必须提供显式初始化。

编译器生成的默认构造函数会按照如下规则初始化成员：
- 如果有提供类内初始值，用它来初始化成员。
- 否则，[默认初始化](/Chapter2/README.md/#221)该成员。


```C++
class a
{
public:
    int c = 0;
	int b ;
	a() = default;
};

a B;

int main()
{
   
	a A;
	cout << B.b << B.c << endl; //0 
	cout << A.b << A.c << endl; //C值未定义
}
```