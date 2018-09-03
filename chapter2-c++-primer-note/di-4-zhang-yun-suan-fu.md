# 第4章运算符

>看完这篇后对运算符更深入理解：
>1、运算符实际也可以看成函数：操作数是输入参数；左值或右值是返回值；
>2、右值：值内容；左值：数据在内存中位置；对象经运算符处理后，注意返回值是左值或右值


## decltype 

### 基本说明

功能：计算一个变量的数据类型。不要值，只要数据类型；实际应用不多，代码中不常见。

关键字和运算符区别：？
1、decltype是关键字

2、运算符可以重载；关键字不可以。。

decltype 和 auto 区别：？

1、decltype与auto关键字一样，用于进行**编译时**类型推导。
2、类型推导：auto是从变量声明的初始化表达式获得变量的类型；decltype是以一个普通表达式作为参数，返回该表达式的类型,而且并不会对表达式进行求值。

### 应用场景

* 重用匿名类型

```c++
struct{
    int a;
    double b;
} anon_s; //变量anon_s

decltype(anon_s) anon_s_other;//重新使用了没有名称结构体类型，定义变量

```

* 泛型编程结合auto，追踪函数的返回值类型

```c++
//decltype最典型的应用场景

template <typename _Tx, typename _Ty>
auto multiply(_Tx x, _Ty y)->decltype(x*y)
{
    return x*y;
}


/*
In C++11, there are two syntaxes for function declaration:
return-type identifier ( argument-declarations... )
and
auto identifier ( argument-declarations... ) -> return_type
这种函数定义方式也找到应用场景了，为什么这样定义？
参考：https://stackoverflow.com/questions/22514855/arrow-operator-in-function-heading
*/

```







----
参考：
1、decltype介绍，参考：https://www.cnblogs.com/QG-whz/p/4952980.html



