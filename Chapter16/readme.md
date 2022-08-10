# Chapter16

模版实例化发生在编译期，类/函数模版和定义最通常放在头文件。

模版类型参数
```C++

template<typename T>
bool compare(const T& v1, const T& v2)
{
    return v1 > v2 ? true : false;
}


```

非类型模版参数
一个非类型的模版参数可以是一个整数，或者是一个指向对象的指针或引用。绑定到整型参数的实参必须是常量表达式，引用或指针参数必须有静态生存期。指针参数也可以用`0`或`nullptr`。
```C++

//整形模版
template<unsigned N, unsigned M>
bool compare(const char(&p1)[N], const char(&p2)[M])
{
    std::cout << "Size:" << N << "  " << M << std::endl;
    return strcmp(p1, p2);
}

//指针
template<const char* c>
void pointer(const char* str)
{
    std::cout << c << " " << str << std::endl; 
}

//引用
template<const char (&c)[5]>
void reference(const char* str)
{
    std::cout << c << " " << str << std::endl; 
}

void f(const char* c)
{
    std:: cout << c << std::endl;
}

//函数指针
template<void (*f)(const char* c)>
void fprointer(const char* c)
{
    f(c);
}

char a[] = {"abc"};
char b[] = {"abcd"};


//整形
template<int T>
void add(int c)
{
    std::cout << c + T << std::endl;
}

int main()
{

    if(compare(a, b))
    {
        std::cout << 1 <<std::endl;
    }
    else
    {
        std::cout << 0 <<std::endl;
    }
    int d = 5;
    pointer<a>("ponter");
    reference<b>("refe");
    fprointer<f>("abcd");
    add<5>(5);
}

```


类模版的成员函数在用到时才会实例化。在类模版自己的作用域中，我们可以直接使用模版名而不提供实参。
```C++

Vector<T>& operator=(const Vector<T>& v); 
Vector& operator=(const Vector& v); 

Vector<T>& operator=(Vector<T>&& v) noexcept; 
Vector& operator=(Vector&& v) noexcept;

```

如果一个类模版包含一个非模版友元，则友元被授权可以访问所有类模版实例。如果友元自身是模版，类可以授权给所有模版实例，也可以只授权给特定实例。

一对一友好关系, 为了引用（类或函数）模版的一个实例，必须首先声明模版自身
```C++

template<typename T> class List;

template<typename T>
class ListNode
{
    friend class List<T>;
}


```

一对多友好关系，无需前置声明
```C++

template<typename T> class List;

template<typename T>
class ListNode
{
    template<typename T>
    friend class List; //List的所有实例都是ListNode每个实例的友元。
}


```

令模版自己的类型参数成为友元
```C++

template<typename T>
class card
{
    friend T;
}

```

模版类型别名
```C++
template<typename T>
using Twin = pair<T, T>

Twin<int>; //Pair<int, int>
```

类模版静态成员
每个实例都有一个count和ctr,互不共享。
```C++

template<typename T>
class Foo
{
public:
    static std::size_t count() { return ctr; }
private:
    static std::size_t ctr;
}

template<typename T>
size_t Foo<T>::ctr = 0; //定义并初始化ctr
```

模版参数实参
```C++
template<typename T, typename F = less<T>>
int compare<T a, T b, f = F()>
{
    if(f(a, b)) return -1
}

```
万能引用推导

T && 碰到右值 int &&， T匹配成int；

T && 遇到左值 int，T此时是int &。

T && 碰到左值 const int，T匹配为 const int &。

T && 碰到左值 const int *（指针类型), T匹配为const int *& (下略）

T && 碰到左值 const int * const（指针类型), T匹配为const int *const & （下略）

```C++
template<typename _Tp>  //std::move
typename std::remove_reference<_Tp>::type&& move(_Tp&& __t) noexcept
    { return static_cast<typename remove_reference<_Tp>::type&&>(__t); }

```

```C++ 
template<typename _Tp>  //std::forward
constexpr _Tp&& forward(typename std::remove_reference<_Tp>::type& __t) noexcept
    { return static_cast<_Tp&&>(__t); }

int a;
test_forward(std::move(a));

constexpr int && forward(int & __t) noexcept
    { return static_cast<int &&>(__t); }


test_forward(a);

constexpr int & forward(int & __t) noexcept
    { return static_cast<int &>(__t); }


```


## 參考
https://zhuanlan.zhihu.com/p/369203981

https://www.zhihu.com/column/c_1306966457508118528
