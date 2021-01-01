# Chapter2


## 2.2.1

初始化不是赋值，初始化的含义是创建变量时赋予某一个初始值，而赋值的含义是把对象的当前值擦除，而以一个新值代替。

如果定义变量时没有指定初值，则变量被默认初始化，此时变量被赋予默认值。如果是内置类型的变量未被显式初始化，它的值由定义的位置决定。定义于任何函数体之外的变量为0。一种例外情况是，定义在函数体内的内置类型变量将不被初始化。


## 2.5.2

当`auto`对引用类型进行推导时，编译器会以引用类型的对象作为auto的类型。
```C++
int i = 0, &r = i; 
auto a = r; //decltype(a) = int
```

`auto`会忽略掉顶层`const`，而底层`const`则会保留下来。（对常量对象取地址是一种底层`const`）
```C++
const int ci = i, &cr = ci;
auto b = ci; //decltype(b) = int
auto c = cr; //decltype(c) = int
auto d = &i; //decltype(d) = int*
auto e = &ci //decltype(e) = const int*
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