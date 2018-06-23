# 数组
```c
int compare(const void * a, const void * b){
    return (*(int *)a - *(int *)b);
}

int main() {
    int a[10] = {5,6,1,2,3,4,5,6,7,8};  // 声明并初始化
    qsort(a, 10, sizeof(int), compare);  // 快排
}
```

# 指针
```c
int x = 1, y = 2, z[10];
int * ip       // ip 是一个指向int的指针
ip = &x;       // ip 现在指向x
y = *ip;        // y = 1
*ip = 0;        // x = 0
ip = &z[0];     // ip现在指向z[0]

double atof(char *)    // atof函数接收一个指向char的指针，并返回一个double类型的值
void * vp    // 可以指向任意类型的指针
void swap(int *px, int *py)   // 指针参数使得我们可以在函数中访问和改变对象
```

### 指针与数组
```c
int a[10];
int *pa;
pa = &a[0];    // 等价于 pa = a
int a1 = *(pa+1)   // a[i] 等价于 *(a+i)，事实上c就是把 a[i] 转换成 *(a+i)
```
数组名和指针很类似，但是它们之间有一个区别，指针是一个变量，但数组名不是。
在作为函数的参数时，数组和指针是等价的，但一般用指针更好（更清晰）,如下：

```c
int strlen(char *s) {...}

strlen("hello, world");   // 传入字符串合法
strlen(array);            // 传入char数组合法
strlen(ptr);              // 传入char指针合法
```

### 指向指针

### 指向函数
```cpp
void estimate(int lines, double(*pf)(int)) {
        cout << (*pf) (lines) << endl;
}

double pam(int lns) {
        return 0.03 * lns + 0.0004 * lns * lns;
}

estimate(12, pam);
```

### const int * （指向const的指针）
```c
int a1 = 1;
int a2 = 3;
const int * p = &a1;
printf("%p: %d\n", p, *p);
p = &a2;                  // 合法，指针可以指向其他地方
printf("%p: %d\n", p, *p);
*p = 3;   // 报错，指针指向的值不能改变
```

### int* const  （const指针）
```c
int a = 1;
int b = 2;
int* const p = &a;
*p = 3;     // 合法, 指针的值可以改变
p = &b;     // 报错，指针不能指向其他地方
```

### const int* const 
指针不能指向其他地方，指针的指向的值也不能改变

# string (char *)
c语言里面处理字符串非常糟糕，还好string.h里面定义了很多方法

```c
// c里面复制字符串需要使用strcpy函数
void strcpy(char *s, const char *t) {
  while(*s++ = *t++);
}

// c里面的字符串比较strcmp
int strcmp(char *s, char *t) {
  for (; *s == *t; s++; t++) {
    if (*s == '\0') return 0;
  }
  return *s - *t
}
```

# struct 

```c
typedef struct Person {
    string name;
    string country;
    int age;
}Person;
```
-> is a shorthand for (*x).field.
如果你有一个指针p, 则毫不犹豫地使用 p->field。如果你在处理一个值v,则使用v.field


# 预处理

# extern vs static
extern关键字告诉编译器“这个变量已经存在，但是他在别的外部区域”。通常它的意思是一个c文件要用到另一个c文件中定义的变量
static （文件级别） 这个变量只能在当前的.c文件中使用，程序其他部分不能访问。
static (函数级别) 该变量只能在该函数中访问 （很少使用）

# memory management
malloc返回一个空指针，用于分配内存。比如开辟一块内存给一个数组，这个数组可以放入howMany个int数。free用于释放内存。

```c
char *ptr;
ptr = (char *)malloc(24);   // 分配24个char大小的内存空间
strcpy(ptr, "hello there");
free(ptr);
```

# io
man stdio

### word count
```c
#define IN 1
#define OUT 0

int main() {
    int c, nl, nw, nc, state;
    state = OUT;
    nl = nw = nc = 0;
    while ((c = getchar()) != EOF) {
        ++nc;
        if (c == '\n') ++nl;
        if (c == ' ' || c == '\n' || c == '\t') {
            state = OUT;
        } else if (state == OUT) {
            state = IN;
            ++nw;
        }
    }
}
```

### cat
```c
int main(int argc, char *argv[]) {
    FILE *fp;
    void filecopy(FILE *, FILE *);

    if (argc == 1) {
        filecopy(stdin, stdout);
    }else {
        while (--argc > 0) {
            if ((fp = fopen(*++argv, "r")) == NULL) {
                printf("can't open %s\n", *argv);
                exit(1);
            } else {
                filecopy(fp, stdout);
                fclose(fp);
            }
        }
    }
    return 0;
}

void filecopy(FILE *ifp, FILE *ofp) {
    int c;
    while ((c = getc(ifp)) != EOF)
        putc(c, ofp);
}
```

### restrict
关键字restrict只用于限定指针；该关键字用于告知编译器，所有修改该指针所指向内容的操作全部都是基于(base on)该指针的，即不存在其它进行修改操作的途径；这样的后果是帮助编译器进行更好的代码优化，生成更有效率的汇编代码。


