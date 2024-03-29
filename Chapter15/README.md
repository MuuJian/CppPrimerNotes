# Chapter15

使用基类的指针或引用时会发生动态绑定
任何构造函数之外的函数都可以是虚函数，虚函数解析过程发生在运行时，而其他函数发生在编译时。
一个派生类的虚函数，形参和返回类型必须一致，返回类类型指针或引用除外。（派生类向基类转换）

赋值兼容规则是指在需要父类对象的地方可以使用子类对象来代替:

    通过public继承，子类得到了父类除构造/析构函数之外的所有成员，且所有成员的访问属性和父类的完全相同。这样，public继承的子类实际就具备了父类的所有功能，凡是父类能解决的问题，子类都可以解决。
    赋值兼容规则是发生在父类和子类之间的:
    1.子类的对象可以赋值给父类对象对象，过程会发生隐式类型转换
    2.父类类型的指针可以指向子类对象
    3.父类类型的引用可以用子类对象初始化

 发生赋值兼容后，子类对象只能被作为父类对象使用，即只能使用从父类继承而来的成员。

虚函数如果使用默认实参，最后派生类和基类一致，否则就算使用派生类版本，传入的依然是基类实参。

| 继承方式 | public | protected | private |
| ---- | ---- | ---- | ---- |
| public | public | protected | private |
| protected | protected  | protected | private |
| private | private | private | private |

```C++
class Base
{
public:
    int pub_mem;
protected:
    int pro_mem;
private:
    int pri_mem;

public:
    Base(int a = 1, int b = 2, int c = 3) : pub_mem(a), pro_mem(b), pri_mem(c) {
      }
};
```

public继承
```C++
class Pub_Derv : public Base
{
public:
    void out_put_base_data()
    {
        cout << pub_mem << endl;    
        cout << pro_mem << endl;
    }

    Pub_Derv() : Base(2, 4, 6) {}
};

int main(void)
{
    Base* p = NULL;
    Pub_Derv d1;
    p = &d1;        //派生类类型自动转换为基类类型

    return 0;
}
```

 protected继承
 ```C++
 class Pro_Derv : protected Base
{
public:
    void out_put_base_data()
    {
        cout << pub_mem << endl;    
        cout << pro_mem << endl;
    }

    Pro_Derv() : Base(2, 4, 6) {}
};

int main(void)
{
    Base* p = NULL;
    Pro_Derv d2;
    p = &d2;    //error

    return 0;
}
```
提示转换时不可达的。这是因为protected继承自Base的派生类Pro_Derv将自身的Base类中的public成员的访问属性设置为了protected。假设这样的编译能过，也就意味着能实现赋值兼容原则–需要基类的地方可以使用派生类代替，自然而然用户可以通过p指针访问派生类中基类的成员pub_mem，即p->pub_mem，但是它的属性现在是protected了，不能被访问。正是这样的悖论，c++编译器直接视为语法错误。换句话说，现在的基类已经不能用派生类来代替。

private继承

private继承，那么派生类中所有public、protected基类成员都是private的，和protected类似，派生类也不能代替基类，所以派生类对象的地址将不会初始化或者赋值给基类的指针。

