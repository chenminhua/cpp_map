# 文件描述符
在unix操作系统中，一切皆文件，所有的IO都是通过读写文件来实现的。在程序可以读写文件之前，程序必须打开文件来告知操作系统自己将对文件进行操作。打开文件的系统调用如果成功，操作系统会返回一个非负整数（文件描述符）给操作系统。当程序开始运行时，自动会打开三个文件：stdin,stdout,stderr，文件描述符分别为0，1，2

```sh
prog < infile > outfile
```

### 底层IO (read, write)

```c
// int n_read = read(int fd, char *buf, int n);
// int n_written = write(int fd, char *buf, int n);
// 把stdin写入到stdout
#include<unistd.h>
int main() {
	char buf[100];
	int n;
	while ((n = read(0, buf, 100)) > 0)
		write(1, buf, n);
	return 0;
}
```

### open, creat, close, unlink系统调用
open 打开文件，获取文件描述符
close 关闭文件，释放文件描述符（断开程序与文件间的关联）
creat 创建并打开文件
unlink 移除文件，可以使用这个系统调用一行实现rm

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

#define log_err(M, ...) fprintf(stderr, "[ERROR] (%s:%d: ) " M "\n", __FILE__, __LINE__, ##__VA_ARGS__)

int main(int argc, char *argv[]) {
	int f1, f2, n;
	char buf[100];
	if (argc != 3) 
		log_err("usage: cp from to");
	if ((f1 = open(argv[1], O_RDONLY, 0)) == -1)
		log_err("cp cant't open %s", argv[1]);
	if ((f2 = creat(argv[2], 0666)) == -1)
		log_err("cp: can't create %s, mode %03o", argv[2], 0666);
	while ((n = read(f1, buf, 100)) > 0) {
		if (write(f2, buf, n) != n) {
			log_err("cp: write error on file %s", argv[2]);
		}
	}
	return 0;
}
```

### 随机访问 lseek
```c
long lseek(int fd, long offset, int origin);

// 定位到文件末尾
lseek(fd, 0L, 2);

//定位到文件开头
lseek(fd, 0L, 0);
```

# 线程
```cpp
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

void *myfunc(void *myvar);

int main(){
        pthread_t thread1, thread2;
        char *msg1 = "First thread";
        char *msg2 = "Second thread";
        int ret1, ret2;

        ret1 = pthread_create(&thread1, NULL, myfunc, (void *) msg1);
        ret2 = pthread_create(&thread2, NULL, myfunc, (void *) msg2);

        printf("main function after pthread_create\n");

        pthread_join(thread1, NULL);
        pthread_join(thread2, NULL);

        printf("First thread ret1 = %d\n", ret1);
        printf("Second thread ret2 = %d\n", ret2);

        return 0;
}

void *myfunc (void *myvar) {
        char *msg;
        msg = (char *) myvar;
    for (int i = 0; i < 10; i++){
                printf("%s %d\n", msg,i);
                sleep(1);

    }
        return NULL;
}
```

# 进程
```cpp
#include <iostream>
#include <unistd.h>
using namespace std;
int main() {
    int pid;
    pid = fork();
    if (pid == 0) {
        cout << "child process " << getpid() << endl;
    } else {
        cout << "parent process " << getpid() << endl;
    }
}
```
