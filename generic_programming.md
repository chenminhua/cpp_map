# template function

```cpp
// 注意class关键字可以替换为typename关键字
template <class Any>
void Swap(Any &a, Any &b) {
  Any temp;
  temp = a;
  a = b;
  b = temp;
}
int x,y;
...
swap(x,y);
```

如上，在编译时，当遇到一个调用两个int引用参数时，编译器生成swap函数的int版本。

为了进一步了解模板，必须理解实例化和具体化。在代码中编写函数模板并不会生成函数定义，它只是一个用于生成函数定义的方案。而当编译器发现程序调用swap(x,y)时，会生成一个swap(int &, int &)的实例。这种实例化被称为**隐式实例化**。

现在c++还允许显示实例化，可以直接命令编译器创建特定的实例，如Swap<int>()。编译器看到这个声明将直接创建函数实例。

```cpp
template void Swap<int> (int, int);
```

除此之外，还可以使用显示具体化方法，让编译器不按照swap()模板来生成函数定义。

```cpp
template <> void Swap<int> (int &, int &);
```

编译器在选择函数原型时，遵循的规则是：非模板版本 优先于 显示具体化 优先于 模板版本。

