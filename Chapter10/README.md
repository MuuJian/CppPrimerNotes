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

## 10.4
除了为每个容器定义的迭代器之外，标准库在头文件 iterator 中还定义了额外的几种迭代器：
  - 插入迭代器：这些迭代器被绑定到到一个容器上，可用来向容器插入元素。
  - 流迭代器：这些容器被绑定到输入或输出流，可用来遍历所有关联的 I/O 流。
  -  反向迭代器：这些迭代器向后而不是向前移动。除了 forward_list 之外的标准容器都有反向迭代器。
  - 移动迭代器：这些专用的迭代器不是拷贝其中的元素，而是移动它们。

## 10.4.1
插入器是一种迭代器适配器，它接受一个容器，生成一个迭代器，能实现向给定容器添加元素。

当通过一个插入迭代器进行赋值时，该迭代器调用容器操作来向给定容器指定位置插入一个元素。

插入器有三种类型，差异在于元素插入的位置：

`back_inserter`：创建一个使用 `push_back` 的迭代器。
`front_inserter：创建一个使用 `push_front` 的迭代器。
`inserter`：创建一个使用 `inserter` 的迭代器，接受一个指向给定容器的迭代器，元素将被插入到给定迭代器所表示的元素之前。
注意：

只有在容器支持 `push_front` 的情况下，才可以使用 `front_inserter`。
只有在容器支持 `push_back` 的情况下，才可以使用 `back_inserter`。

```C++
list<int> lst = { 1,2,3,4 };
list<int> lst2, lst3;

lst3.push_back(5);

//lst2 包含 4,3,2,1
copy(lst.cbegin(), lst.cend(), front_inserter(lst2));
//lst3 包含 1,2,3,4,5
copy(lst.cbegin(), lst.cend(), inserter(lst3, lst3.begin()));


```
## 10.4.2

当创建一个 `istream_iterator` 时，我们可以将它绑定到一个流，还可以默认初始化迭代器，这样就创建了一个可以当作尾后值使用的迭代器。
```C++
istream_iterator<int> int_it(cin);	//从cin读取int
istream_iterator<int> int_eof;	//尾后迭代器
ifstream in("afile");
istream_iterator<string> str_it(in);	//从afile读取字符串
```
用 `istream_iterator` 从标准输入读取数据，存入一个`vector`:
```C++
vector<int> vec;
istream_iterator<int> in_iter(cin);	
istream_iterator<int> eof;	

while (in_iter != eof)	//当有数据可读取时
    //后置递增运算读取流，返回迭代器的旧值
    //解引用迭代器，获得从流读取的前一个值
    vec.push_back(*in_iter++);
```
对于一个绑定到流的迭代器，一旦其关联的流遇到文件尾或遇到 IO错误，迭代器的值就与尾后迭代器相等。

可以将程序重写为如下形式：
```C++
istream_iterator<int> in_iter(cin),eof;
vector<int> vec(in_iter, eof);
```
采用一对表示元素范围的迭代器来构造 vec，这两个迭代器是 `istream_iterator`，这意味着元素范围是通过从关联的流中读取数据获得的。

使用算法操作流迭代器
可以用一对 `istream_iterator` 来调用 `accumulate`：
```C++
istream_iterator<int> in_iter(cin),eof;
cout << accumulate(in_iter, eof, 0) << endl;
```
`istream_iterator` 允许使用懒惰求值
当将一个 `istream_iterator` 绑定到一个流时，标准库并不保证迭代器立即从流读取数据。具体实现可以推迟从流中读取数据，直到使用迭代器时才真正读取。

标准库中的实现所保证的是，在我们第一次解引用迭代器之前，从流中读取数据的操作已经完成了。

对于大多数程序来说，立即读取还是推迟读取没什么区别，但是，如果创建了一个 `istream_iterator` ，没有使用就销毁了，或者我们正在从两个不同的对象同步读取一个流，那么何时读取可能就很重要了。

可以对任何具有输出运算符(<<)的类型定义 `ostream_iterator` 。

当创建一个 `ostream_iterator` 时，可以提供可选的第二参数，它是一个字符串，在输出每个元素后都会打印此字符串。此字符串必须是一个C风格字符串(即，一个字符串字面值或者一个指向一个以空字符结尾的字符数组的指针0)。

不允许将 `ostream_iterator` 绑定到一个指定的流，不允许空或表示尾后位置的 `ostream_iterator` 。

使用 `ostream_iterator` 来输出值的序列：
```
vector<int> vec = { 1,2,3,4,5,6 };
ostream_iterator<int> out_iter(cout, " ");
for (auto e : vec)
	*out_iter++ = e;
cout << endl;
```

实际上是可以忽略解引用和递增运算，即写成：
```C++
for (auto e : vec)
	out_iter = e;
cout << endl;
```
运算符 * 和 ++ 实际上对 `ostream_iterator` 对象不做任何事情，因此忽略了也没有影响。

但是推荐使用第一种方式，在这种写法中，迭代器的使用和其它迭代器的使用保持一致。

可以使用copy来打印 vec 中的元素，比编写循环更为简单：
```C++
copy(vec.begin(),vec.end(), out_iter);
cout << endl;
```
使用流迭代器处理类类型
可以为任何具有输入运算符 >> 的类型创建 `istream_iterator` 对象。

可以为任何具有输出运算符 << 的类型创建 `ostream_iterator` 对象。
```C++
istream_iterator<Sales_item> item_iter(cin), eof;
ostream_iterator<Sales_item> out_iter(cout,"\n");
//将第一笔交易记录存在sum中，并读取下一条记录
Sales_item sum = *item_iter++;
while (item_iter != eof{
    //如果当前交易记录有着相同的ISBN号
    if (item_iter->isbn() == sum.isbn())
   		sum += *item_iter++;	//将其加到sum上并读取下一条记录
    else {
        out_iter = sum;		//输出sum当前值
        sum = *item_iter++;	//记录下一条记录
    }
}

out_iter = sum;	//打印最后一组记录的和
```
反向迭代器
反向迭代器就是在容器中从尾元素向首元素移动的迭代器。对于反向迭代器，递增和递减的操作会颠倒过来。递增一个反向迭代器会移动到前一个元素，递减一个迭代器会移动到下一个元素。

除了 `forward_list` 之外，其它容器都支持反向迭代器。

可以通过调用 `rbegin`、`rend`、`crbegin`、`crend` 成员函数获得反向迭代器，这些成员函数返回指向容器尾元素和首元素之前一个位置的迭代器。


使用反向迭代器逆序打印一个 vector 中的元素：
```C++
vector<int> vec = { 0,1,2,3,4,5,6,7,8,9 };
for (auto r_iter = vec.crbegin();	//r_iter绑定到尾元素
		r_iter != vec.crend();	//crend 指向首元素之前的位置
		++r_iter)			//实际是递减，移动到前一个元素
cout << *r_iter<< endl;
```
使用`sort`将`vector`整理为逆序序列：
```C++
//顺序排列
sort(vec.begin(), vec.end());
//逆序排列
sort(vec.rbegin(), vec.rend());
```
反向迭代器需要递减运算符
只能从既支持 ++ 也支持 -- 的迭代器来定义反向迭代器。

除了 `forward_list` 之外，标准容器上的其它迭代器都既支持递增运算又支持递减运算。但是流的迭代器不支持递减运算，因为不可能在一个流中反向移动，因此，不可能从一个 `forward_list` 或一个流迭代器创建反向迭代器。

反向迭代器和其它迭代器之间的关系
找到一个字符串中第一个 , 分隔符前面的单词：
```C++
string line = "FIRST,MIDDLE,LAST";
auto comma = find(line.cbegin(), line.cend(), ',');
cout << string(line.cbegin(), comma) << endl;
```
如果使用反向迭代器：
```C++
auto rcomma = find(line.crbegin(), line.crend(), ',');
cout << string(line.crbegin(), rcomma) << endl;
```
实际上打印出来的是 `TSAL` 。

`reverse_iterator` 的 `base` 成员函数可以返回一个普通迭代器：
```C++
cout << string(rcomma.base(), line.cend()) << endl; //LAST
```