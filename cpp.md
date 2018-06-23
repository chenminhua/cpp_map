# c++ 四大组成
c: blocks, statements, preprocessor, pointers
面向对象c++: class, 封装，继承，多态，virtual函数（动态绑定）
template c++: 泛型编程
STL: template程序库。容器，迭代器，算法，函数对象。。。

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
```

当计算机内存不够时，new将会返回0。在c++中，值为0的指针被称为空值指针。

不要使用delete来释放不是new分配的内存。
不要使用delete释放同一个内存块两次。
如果使用new[]为数组分配内存，则应使用delete[]来释放。
如果使用new[]为一个实体分配内存，则应使用delete来释放。
对空值指针应用delete是安全的。

# 在函数后面加上const可以将this限定为const

```cpp
class Foo{
    std::string Name;
public:
    const std::string &getName() const {
        return Name;
    }
}
```

# 内联函数
程序编译后得到的可执行程序是由一组机器语言指令组成。运行程序时，操作系统将这些指令载入到计算机内存中，因此每条指令都有特定的内存地址。执行函数调用时，程序将在函数调用后立即存储改指令的内存地址，并将函数参数复制到栈中，然后跳到标记的函数起点的内存单元开始执行函数代码。等执行完成后在跳回原来的地址。这种来回跳跃需要一定的开销。
在函数声明前以及函数定义前加上关键字inline，在编译的时候，编译器会将函数调用替换为函数代码。

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


