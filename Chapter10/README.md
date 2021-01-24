# Chapter10

# 10.3.2

lambda 表达式
```C++
[capture list](pramter list) -> return type {function body}
```

忽略参数
```C++
auto f = [] {return 42;}
cout << f() << endl;
```

向lambda传递参数
```C++
auto f = [](const string& a, const string& b)
{
    return a.size < b.size;
}

sort(words.begin(), words.end(), f());
```

捕获列表
```C++
[sz](const string& a)
{
    return a.size() >= sz;
}
```

## 10.3.3 
lambda值捕获

```C++
void fcn1()
{
    size_t v1 = 42;
    auto f = [v1]{return v1;};
    v1 = 0;
    auto j = f(); //42 保存值
}
```
lambda引用捕获

```C++
void fcn1()
{
    size_t v1 = 42;
    auto f = [&v1]{return v1;};
    v1 = 0;
    auto j = f(); //0
}
```

lambda隐式捕获
```C++
void biggies(vector<stirng>& words, vector<string>::size_type, ostream& os = cout, char c = ' ')
{
    for_each(words.begin(), words.end(), [&, c](const string& s){os << s << c;}) //隐式捕获os 显示捕获c
}


void biggies(vector<stirng>& words, vector<string>::size_type, ostream& os = cout, char c = ' ')
{
    for_each(words.begin(), words.end(), [=, &os](const string& s){os << s << c;}) //隐式捕获c 显示捕获os
}

```

lambda 返回
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