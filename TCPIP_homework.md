## 11章
1.什么是进程间通信？分别从概念上和内存的角度进行说明。
- 进程间通信意味着两个不同进程间可以交换数据
- 只要有两个进程可以同时访问的内存空间，就可以通过此空间交换数据

2.进程间通信需要特殊的IPC机制，这是由操作系统提供的。进程间通信时为何需要操作系统的帮助？
进程间有完全独立的内存结构。就连通过fork函数创建的子进程也不会与父进程共享内存空间。

 IPC(Inter-Process Communication）  

3.管道是经典的IPC技法。关于管道，请回答如下问题。
a.管道是进程间交换数据的路径。如何创建此路径？由谁创建？
- 使用`int pipe(int fileds[2])`创建，由操作系统创建

b.为了完成进程间通信，2个进程需同时链接管道。那2个进程如何连接到同一管道
- 父进程创建管道时，同时获取对应于出入口的文件描述符。将入口或出口中的一个文件描述符传递给子进程，则2个进程连接到同一管道。

c.管道运行进行2个进程间的双向通信。双向通信需要注意哪些内容？
- 数据进入管道后成为无主数据。也就是通过read函数先读取数据的进程将得到数据，即使该进程将数据传到了管道。
- 只用1个管道进行双向通信需要预测并控制运行流程，这在每种系统中都不同，可以视为不可能完成的任务。
- 双向通信需要创建2个管道，各自负责不同的数据流动。

3.编写示例复习IPC技法，使2个进程相互交换3次字符串。当然，这2个进程应具有父子关系，各位可指定任意字符串。
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#define BUF_SIZE 30

int main()
{
    char msg1[] = "this is str1";
    char msg2[] = "this is str2";
    char msg3[] = "this is str3";
    char buf[BUF_SIZE];

    int fds1[2], fds2[2];
    pipe(fds1);
    pipe(fds2);
    pid_t pid = fork();
    if (pid == 0)
    {
        write(fds1[1], msg1, sizeof(msg1));
        read(fds2[0], buf, BUF_SIZE);
        printf("child proc: %s\n", buf);
        write(fds1[1], msg3, sizeof(msg3));
    }
    else
    {
        read(fds1[0], buf, BUF_SIZE);
        printf("parent proc: %s\n", buf);
        write(fds2[1], msg2, sizeof(msg2));
        read(fds1[0], buf, BUF_SIZE);
        printf("parent proc: %s\n", buf);
    }
    return 0;
}

```
