# 面向对象程序设计

> OOP 是强调封装、继承、多态的一种编程范式

## Introduction

### 封装

将数据和操纵数据的函数捆绑在一起，是封装(encapsulation)思想的一部分。

这种扩展后的 struct ,在 C++ 中也称为 class 。

类是一个共享相同结构和行为的对象的集合。

类是一种(用户自定义的)数据类型。状态可以用由数据变量表示，行为可以由函数来实现。这些变量和函数，是这个类的成员。

```c++
struct linkedlist {
    struct node* llist;
    static linkedlist create();
    int size() const;
    //……
}
```

#### 访问控制

```c++
struct User {
    
}
```

在类中

### 继承

### 多态

## 类

### 类的定义

`this`指针：每个成员函数都会被视为有一个 Implicit object parameter。成员函数的函数体中，访问任何成员时都会被自动添加 `this->`。

#### 构造函数

构造函数是一种特殊的成员函数，用于初始化该类的对象。

构造函数存在的意义是给该类的每个对象提供一定的保证。

我们称一个能够无参调用的构造函数是 defalut constructor。对于一个类，如果用户没有提供任何构造函数，编译器会自动为这个类创建一个 Public 的 implicitly-declared default constructor。

#### 构造和析构的时机和顺序

对于一个类对象，它的生命周期自它的初始化(构造)完成开始，到它的析构函数调用被启动为止。

对于下面的情况，构造函数会被调用：

- 对于全局对象，在 `main()` 函数运行之前，或者在同一个编译单元内定义的任一函数或对象被使用之前。在同一个编译单元内，它们的构造函数按照声明的顺序初始化。
- 对于 static local variables ，在第一次运行到它的声明的时候。
- 对于 automatic storage duration 的对象，在其声明被运行时。
- 对于 dynamic storage duration 的对象，在其用 new 表达式被创建时。

对于下面的情况，析构函数会被调用：

- 对于 static storage duration 的对象，在程序结束时，按照与构造相反的顺序。
- 对于 automatic storage duration 的对象，在所在的 block 退出时，按照与构造相反的顺序。
- 对于 dynamic storage duration 的对象，在 delete 表达式中。



## 拷贝赋值、运算符重载与引用

### 拷贝赋值运算符

在一个有运算符的表达式中，如果至少一个操作符是某个类的对象，则由重载解析查找对应的函数。

`x=y` 会被视为 `x.opoerator=(y)`进行查找。



### 运算符重载

重载：同一个函数（根据参数列表不同）具有不同的行为。

运算符重载也可以放在全局。

```c++
const int M = 100;
class Matrix {
    int data[M][M];
public:
    Matrix operator+(Matrix mat) { /* */ }
    Matrix operator*(int x) { /* */ }
    Matrix operator*(Matrix mat) { /* */ }
};
Matrix operator*(int x, Matrix mat) { /* */ }
```

当 `x*y` 的操作数中有类实例时，重载解析会尝试将它解释为 `x.operator*(y)` 和 `operator*(x,y)`。即 `x` 对应类中的成员 `operator*` 和全局的 `operator*` 都会被纳入候选函数集，然后再根据实际的参数列表完成重载解析。



C++ 允许一个类的定义中给一个外部发函数授予访问其 private 成员的权限，方式是将对应的函数在该类的定义中将其声明为一个友元。

```c++
const int M = 100;
class Matrix {
    int data[M][M];
public:
    Matrix operator+(Matrix mat) { /* */ }
    Matrix operator*(int x) { /* */ }
    Matrix operator*(Matrix mat) { /* */ }
    friend Matrix operator*(int x, Matrix mat); // Designates a function as friend of this class
};
Matrix operator*(int x, Matrix mat) {
    Matrix tmp = mat;   // copy mat
    for (int i = 0; i < M; i++)
        for (int j = 0; j < M; j++)
            tmp.data[i][j] *= x;        // can access private member Matrix::data
    return tmp;
}
```

友元只是一种权限授予的声明，友元函数并非类的成员。因此它并不受 access-specifier 的影响。

另一种解决方法：

```c++
const int M = 100;
class Matrix {
    int data[M][M];
public:
    Matrix operator+(Matrix mat) { /* */ }
    Matrix operator*(int x) { /* */ }
    Matrix operator*(Matrix mat) { /* */ }
};
Matrix operator*(int x, Matrix mat) {
    return mat * x;
}
```

这种方案复用了 `Matrix::operator*(int)`，这样一方面能提高代码的重用性和可维护性，另一方面又不需要把 `operator*(int, Matrix)` 设置成友元（因为没有访问 private 成员），因此事实上比前面那种解决方案更好。



### 引用(reference)

对于先前的矩阵类，对象占据的内存非常大，因此我们将对象作为参数传递时会有很大的开销。

可以通过传递指针的方式来减少不必要的拷贝。

==一个引用是一个已经存在的对象或者函数的别名==

```c++
int x = 2;
int & y = x;
```

对 `y` 所有操作和对 `x` 一样：`y` 不是 `x` 的指针，也不是 `x` 的副本，而是 `x` 本身。包括获取它的地址—— `&y` 和 `&x` 的值相同。

也是因此，我们无法重新约束一个引用所绑定的变量。因为：

```c++
int z = 3;
y = z;
```

上面的 `y = z` 实际上是给 `x` 赋值为 `z`，而非将 `y` 重新绑定到 `z`。

- 引用最广泛的用法是作为参数传递。
- 引用作为返回值：

```c++
class Container {
    elem* val;
    unsigned size = 0, capa;
    // ...
public:
    elem & operator[](unsigned index) {
        return val[index];
    }
    // ...
};
```



C++ 规定：可以将一个临时对象绑定给一个 const 引用，这个临时对象的生命周期被延长以匹配这个 const 引用的生命周期。

