# c++ 四大组成
```
c: blocks, statements, preprocessor, pointers
面向对象c++: class, 封装，继承，多态，virtual函数（动态绑定）
template c++: 泛型编程
STL: template程序库。容器，迭代器，算法，函数对象。。。
```

# 类型别名
```cpp
#define BYTE char  // 预处理器，预编译阶段替换，无脑替换
#define addNum(x,y) (x + y)

typedef char BYTE  // 重命名，参与编译。

using FP = void (*) (int, const std::string&); 
// 类似于typedef，但是支持template，仅可用于c++11之后
// 等价于 typedef void (*FP) (int, const std::string&);
```

# memory management
自动存储: 在函数内部定义的常规变量使用自动存储空间，被称为自动变量。在函数被调用时自动产生，在函数结束时消亡。（不用手动管理）
静态存储：整个程序执行期间都存在。使变量称为静态的方式有两种：1.static关键字，2.在函数外定义它。
动态存储：new, delete


### new and delete
使用new请求正确数量的内存，返回指向该块内存的指针。使用指针来跟踪内存的位子，使用delete释放使用new分配的内存。在c中我们需要使用malloc和free来处理内存的分配和回收

```c
int *parr = new int[10];
delete[] parr; // 方括号告诉程序，需要释放整个数组，而不仅仅是指针指向的元素。

当计算机内存不够时，new将会返回0。在c++中，值为0的指针被称为空值指针。
不要使用delete来释放不是new分配的内存。
不要使用delete释放同一个内存块两次。
如果使用new[]为数组分配内存，则应使用delete[]来释放。
如果使用new[]为一个实体分配内存，则应使用delete来释放。
对空值指针应用delete是安全的。
```

# 函数默认参数
```cpp
char * left (const char * str, int n = 1);
```

# template 
### template function
```cpp
template <typename T>
void swapit(T & a, T & b) {
    T tmp = a;
    a = b;
    b = tmp;
}

string s = "abc";
string t = "cba";
swipit(a, b);
```

### template class
```cpp
template <class T>
class Stack {
   private:
      vector<T> elems;    // elements

   public:
      void push(T const&);  // push element
      void pop();               // pop element
      T top() const;            // return top element

      bool empty() const {      // return true if empty.
         return elems.empty();
      }
};

template <class T>
void Stack<T>::push (T const& elem) {
   elems.push_back(elem);
}

template <class T>
void Stack<T>::pop () {
   if (elems.empty()) {
      throw out_of_range("Stack<>::pop(): empty stack");
   }

   // remove last element
   elems.pop_back();
}

template <class T>
T Stack<T>::top () const {
   if (elems.empty()) {
      throw out_of_range("Stack<>::top(): empty stack");
   }

   // return copy of last element
   return elems.back();
}

int main() {
   try {
      Stack<int>         intStack;  // stack of ints
      Stack<string> stringStack;    // stack of strings

      // manipulate int stack
      intStack.push(7);
      cout << intStack.top() <<endl;

      // manipulate string stack
      stringStack.push("hello");
      cout << stringStack.top() << std::endl;
      stringStack.pop();
      stringStack.pop();
   } catch (exception const& ex) {
      cerr << "Exception: " << ex.what() <<endl;
      return -1;
   }
}
```


# 虚函数
[为啥需要virtual函数](https://stackoverflow.com/questions/2391679/why-do-we-need-virtual-functions-in-c)
没有virtual的话，就会early binding，也就是说调用的method再编译时就被决定了

纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加“=0”
virtual void funtion1()=0
含有纯虚拟函数的类称为抽象类，它不能生成对象。

# tips 
在你不清楚的时候，应该把值放在堆上。不要依赖于栈的语义，或者指定区域，而是要直接使用malloc创建它。

Explicit 指定符 修饰构造函数，被修饰的类不能发生隐式类型转换

# 头文件与链接
只包含cmath头文件可以提供原型，但不一定会导致编译器能搜索正确的库文件，可以使用如下命令帮助编译器链接到库文件

```sh
g++ sqrt.c -lm
```


# 异常 (try, catch, throw)
```c
try {
    z = hmean(x,y);
} catch (const char * s) {
    std::cout << s << std::endl;
    ...
}
   
double hmean(double a, double b) {
    if (a == -b) {
        throw "ban hmean() arguments: a = -b not allowed";
    }
    return 2.0 * a * b / (a + b);
}
```

c++异常的主要目的是为了设计容错程序提供语言级支持，异常使得在程序设计中包含错误处理功能更容易，以免事后采取更严格的错误处理方式。

```c
#include <exception>
class bad_hmean : public std::exception {
public:
    const char * what() {
        return "bad argument to hmean()";
    }
    ...
};

class bad_gmean : public std::exception {
public:
    const char * what() {
        return "bad arguments to gmean()";
    }
    ...
}
```

标准库中定义了几个异常类: domain_error, invalid_argument, length_error, out_of_bounds, runtime_error等等

```c
try {...}
catch(out_of_bounds & oe) {...}
catch(logic_error & oe) {...}
catch(exception & oe) {...}
```

对于处理使用new时可能出现的内存分配问题。你可以选择让new返回一个空指针，或者让new引发bad_alloc异常。


# RTTI (Runtime Type Identification)
运行时类型识别，旨在为程序在运行阶段确定对象的类型。很多类库已经为其类对象提供了实现RTTI功能的方式。警告： RTTI只适应于包含虚函数的类。

假设有一个类层次结构，其中的类都是从同一个基类派生的，则可以让基类指针指向其中任何一个类的对象。问题是，如何知道指针指向的是哪个类的对象呢？？

### dynamic_cast
dynamic_cast是最常用的RTTI组件，它不能回答“指针指向的是哪类对象”的问题，但可以回答“是否可以安全地将对象的地址赋给特定类型的指针”的问题。

```c
class Grand{ // has virtual methods }
class Superb: public Grand {...};
class Magnificant: public Superb{...};
Grand *pg = new Grand;
...

Superb *pm = dynamic_cast<Superb *>(pb);
```

### typeid操作符和type_info类
```c
typeid (Magnificant) == typeid(*pg)
```

### 类型转换操作符
dynamic_cast;
const_cast;
static_cast;
reinterpret_cast;
