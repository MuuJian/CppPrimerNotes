# Chapter2


## 2.2.1

初始化不是赋值，初始化的含义是创建变量时赋予某一个初始值，而赋值的含义是把对象的当前值擦除，而以一个新值代替。

如果定义变量时没有指定初值，则变量被默认初始化，此时变量被赋予默认值。如果是内置类型的变量未被显式初始化，它的值由定义的位置决定。定义于任何函数体之外的变量为0。一种例外情况是，定义在函数体内的内置类型变量将不被初始化。

## 2.4
不能将临时变量绑定到非常量左值引用
```C++
  class Base
    {
        public:
               void something(Base& b){}
    };

    int main()
    {
        Base b;
        b.something(Base()); //error
        return 0;           
    }
```

## 2.5.1
```C++
typedef char* Pstring
const Pstring cstr = 0
const char* cstr = 0
```
两者不相等，因为指针的 const 表示比较特殊，const T* 是一个指向 const T 的指针，你可以使其指向另一个 const T；而 T* const 是一个指向 T 的指针，而且你无法改变它指向的对象。然而在非指针情况下 const T 就是一个无法被改变的对象，所以当 T 是一个指针类型时（如S*），那么 const T 会被解释为 T* const，原因是 const 是在修饰 T 本身（整个指针），而不是 S （指针指向的对象）。

## 2.5.2

当`auto`对引用类型进行推导时，编译器会以引用类型的对象作为auto的类型。
```C++
int i = 0, &r = i; 
auto a = r; //decltype(a) = int
```

`auto`会忽略掉顶层`const`，而底层`const`则会保留下来。
```C++
const int ci = i, &cr = ci;
auto b = ci; //decltype(b) = int
auto c = cr; //decltype(c) = int
auto d = &i; //decltype(d) = int*
auto e = &ci //decltype(e) = const int* （对对象取地址是底层const)
```


## 2.5.3

`decltype`的作用是选择并返回操作数的数据类型。
```C++
int f();
decltype(f()) = sum = x;  //decltype(sum) = int
```

`decltype`处理const和引用的方式与auto不同
```C++
const int ci = 0, &cj = ci;
decltype(ci) x = 0; //const int
decltype(cj) y = x; //const int&
decltype(cj) z; //error const int&必须初始化
```

`decltype`解指针可以得到指针所指的对象。从借助函数的角度考虑的话，可以想下若某个类要模仿指针的语义，其解引用的`operator*()`就需要返回`T&` 。
```C++
int i = 42, *p = &i;
decltype(*p) c = i; //decltype(c) = int&
```

切记`decltype((variable))`返回的结果永远是引用，而`decltype(variable)`只有当`variable`本身是一个引用时才是引用.

`auto`配合`decltype`引用于`Effective Modern C++`
```C++
int i;
int&& f();
auto x3a = i;                  // decltype(x3a) is int
decltype(auto) x3d = i;        // decltype(x3d) is int
auto x4a = (i);                // decltype(x4a) is int
decltype(auto) x4d = (i);      // decltype(x4d) is int&
auto x5a = f();                // decltype(x5a) is int
decltype(auto) x5d = f();      // decltype(x5d) is int&&
auto x6a = { 1, 2 };           // decltype(x6a) is std::initializer_list<int>
decltype(auto) x6d = { 1, 2 }; // error, { 1, 2 } is not an expression
auto *x7a = &i;                // decltype(x7a) is int*
decltype(auto)*x7d = &i;       // error, declared type is not plain decltype(auto)
```
```C++

template<class T, class U>
decltype(*(T*)(0), *(U*)(0)) mul(T x, U y)
{
    return x * y;
}

template<class T, class U>
auto mul(T x, U y) -> decltype(x * y)
{
    return x * y;
}

C++14:
template<class T, class U>
auto mul(T x, U y)
{
    return x * y;
}

```