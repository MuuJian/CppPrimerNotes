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
typedef char *pstring;
const pstring cstr = 0;         //char *const cstr
const pstring *ps;              //char *const *ps
```
[说一下里面涉及的概念。](https://segmentfault.com/q/1010000002568376)
```C++
typedef char* pstring;
const pstring cstr = 0;         //char* const cstr
cstr is a constant pointer to char. (注意，const 修饰的是 pointer.)
```
const char * vs char * const ?
 - const char * is a pointer to a const char.
 - char * const is a constant pointer to char.
 - char * const <==> const pstring
 - const char * <==> char const *

所以 @snailcoder 对于 const 在 * 左侧还是右侧的理解是正确的。但对于 typedef声明的理解稍有偏差。

容我直接引用书上的解释，说的很清楚了：

    The base type in these declarations is const pstring. As usual, a const that appears in the base type modifies the given type. The type of pstring is “pointer to char.” So, const pstring is a constant pointer to char—not a pointer to const char.

恰好，这也解释了你第三个疑虑，const 出现在 base type 旁边，就修饰的是 base type，在左边在右边没半点差别。(这里的 base type 是 char *, 是个指针。)

以上。

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

```C++
const auto f = ci;//decltype(f) = const int
```

还可以将引用的类型设置为auto,此时原来的初始化规则仍适用
```C++
auto &g = ci; //decltype(g) = const int&
auto &h = 42; //错误 不能将临时变量绑定到非常量左值引用
const auto &j = 42; //右值
```

设置一个类型为auto的引用时，初始值中的顶层常量属性依然保留。和往常一样，如果我们给初始值绑定一个引用，则此时的常量就不是顶层常量了。
When we ask for a reference to an auto-deduced type, top-level consts in the initializer are not ignored. As usual, consts are not top-level when we bind a reference to an initializer

top-level const : const 修饰的是自身。
low-level const : const 修饰的是别人。

什么叫自身，什么叫别人？C++ 的世界里：

POD，类对象都只能是"自身"
指针可以是自身(指针本身)，可以是别人(指向别人)。
引用没有自身，只能是别人。(引用是别名)
所以，有以下几个规律：

指针，可以是 top-level const，也可以是 low-level const。
引用只能是 low-level const。
指针何时是 top-level const，何时是 low-level const，请参考昨天回答的你的另一个问题。
(const char *是 low-level, char * const 是 top-level.)

如果上面这堆概念你都搞清楚了的话，我们在来理解一下你引用的话。举例说明：

const int ci = 0;
auto &r1 = ci; // r1 is a const int&
好了，ci 是 const int，是 top-level const；r1 呢？是 const int&，是引用，引用必然是 low-level const。

所以：

consts are not top-level when we bind a reference to an initializer

后半句同意吧？

再看例子里面的 r1，请问你可以修改它吗？你可能会不解，废话，都加了 const 还能修改？

那我们来看另一个例子：

const char * ch = "test";
ch = "mood"; // ok, const is low-level

char c[4] = "hel";
char * const cch = c;
cch = ch; // error! const is top-level
看到了吧，对于指针来说，low-level const 是可以修改的！(对应开始的概念，指针可以是"别人"，别人是 const 不影响指针指向另一个人)

那么 const int* 可以修改，而 const int& 不能修改，这是由引用本身的概念导致的（一旦初始化无法再次绑定）。
即，const 引用是 low-level const 的同时，还兼备了 top-level const 的特质，即"自身"无法被修改。

所以：

When we ask for a reference to an auto-deduced type, top-level consts in the initializer are not ignored.

说的是“顶层常量属性依然保留”，好好体会一下这里说的顶层常量属性的含义。

综上所述，结合我给的例子。最简单的解释：

auto &r1 = ci; 发生了什么？

从 top-level const 降成 low-level const.
top-level const 的特质(自身无法修改)依然保留。


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

decltype并不负责把数组类型转换成指针，所以decltype返回类型是个数组。
```C++

int odd[] = { 1, 3, 5, 7, 9 };
int even[] = { 0, 2, 4, 6, 8 };
decltype(odd)* arrPtr(int i)
{
    return (i % 2) ? &odd : &even;
}

decltype(odd) = int[5]
decltype(odd)* = int(*)[5]

```

