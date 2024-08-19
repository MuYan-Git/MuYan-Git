Linux进程管理
吴主华_还未完善

# 一、基本概念

## 1.1 进程

正在运行的程序的实例，包含两部分

- 一个组成部分是操作系统用来管理进程的内核对象。内核对象也是系统用来存放关于进程的统计信息的地方
- 另一个组成部分是地址空间，包含所有可执行模块或DLL模块的代码和数据，包含动态内存分配的空间（线程堆栈和堆栈分配空间）

进程运行过程中实际就是把磁盘的二进制文件加载（映射）到内存空间中，并且指引到cpu去内存中寻址，然后计算且返回（I/O）的过程


## 1.2 进程运行过程

### 1.2.1 进程运行过程描述：

1. 将磁盘的程序装载道内存，即实例化
2. 读取内存中的程序段内容，给变量分配空间，在调用时寻址操作

### 1.2.2 进程运行特性

- 多任务，多进程“并发”（分时）：利用**进程调度**实现
- 彼此独立，所处内存隔绝：利用**虚拟内存**实现

### 1.2.3 进程生命周期

- Android进程生命周期状态：前台 可见 服务 背景 空
- 进程的三大状态：运行 挂起 消亡

## 1.3 进程设计结构



# 二、进程管理调度

## 2.1 进程表

## 2.2 进程树

## 2.3 进程调度算法

## 2.4 进程的实现

### 2.4.1 得到进程ID

`pid_t getpid(void)`：得到当前进程的ID
`pid_t getppid(void)`：得到父进程的ID

### 2.4.2 执行命令行

### 2.4.3 进程创建与销毁

#### （1）进程创建

**fork**

`pid_t fork(void)` 返回值：失败返回-1；成功返回0（子进程）和大于0（父进程）
	`fork`时完全**copy**了一份父进程的地址空间给予子进程，让彼此可以**独立的执行**，父子进程执行的先后顺序不定，子进程有自己的pid
				由于创建过程是整个内存的负责，所以开销较大
				写时复制技术介绍：按需开辟空间，尽可能延缓对空间的开辟，fork速度由此得到提升

```C
#include <unistd.h>
#include <stdio.h>
int main()
{
    pid_t pid;
    pid = fork();
    if(pid == -1)
        perror("failure\n");
    else if(pid == 0)
        printf("chile process:%d\n", getpid());
    else
        printf("parent process:%d\n", getpid());
    
    return 0;
}
```

`fork`函数分析：`do_fork` 执行流程

- **`copy_process`** ：调用函数拷贝一个进程
    检查标志 -> `dup_task_sruct`拷贝进程结构 -> 检查资源限制 -> 初始化`task_struct` -> `sched_fork` -> 复制/共享进程的各个部分(`copy_semundo/copy_files/copy_fs······`)  -> 设置各个ID，进程关系等  
- 确定pid：拷贝完成返回一个pid
- 初始化`vfork`的完成处理程序和`ptrace`标志（跟踪标志）
- `wake_up_new_task` ：调用函数启动新的进程
- 检查是否设置了`conle_vfork`标志

**vfork**

`pid_t vfork(void)` 返回值：失败返回-1；成功返回0（子进程）和大于0（父进程）
	`vfork`是与父进程共享地址空间，并且**父进程回等待子进程执行完毕后才继续运行**，子进程有自己的pid

```C
#include <unistd.h>
#include <stdio.h>
int main()
{
    pid_t pid;
    pid = vfork();
    if(pid == -1)
        perror("failure\n");
    else if(pid == 0)
    {
        printf("chile process:%d\n", getpid());
        exit(0);	//手动退出，否则由于vfork的原因会循环执行 循环跳到调用vfork函数处往下执行
    }
    else
    {
        printf("parent process:%d\n", getpid());
        exit(0);
    }
    return 0;
}
```

#### （2）进程销毁

**exit**

销毁进程时会清空缓存

```C
#include <stdio.h>
int main()
{
    printf("hello linux");
    exit(0);		//可以打印hello linux
}
```

**_exit**

销毁进程时不会清空缓存

```C
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
int main()
{
    printf("hello linux");
    _exit(0);		//不打印hello linux 
}
```

### 2.4.4 进程切换

### 2.4.5 进程等待与僵死进程

#### （1） 进程等待

`pid_t wait(int *status-ptr)` 父进程调用，等待子进程退出，回收子进程的资源
	成功返回被等待进程pid，失败返回-1

```C
#include <stdio.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <stdlib.h>
int main()
{
    pid_t pid = fork();
    if(pid == 0)
    {
        printf("child process\n");
        sleep(5);	//休眠5s
        exit(0);
    }
    else if(pid > 0)
    {
        wait(NULL);	//等待 让子进程先执行
        printf("parent process \n");
        exit(0);
    }
}
```



`pid_t waitpid(pid_t pid, int *status-ptr, int options)` 等待指定ID号的子进程结束，回收其资源

#### （2） 僵死进程

### 2.4.6 执行文件exec族

exec函数族的作用是根据指定的文件名找到可执行文件，并用它来取代调用进程的内容，即在调用进程内部执行一个可执行文件
		这里的可执行文件既可以是二进制文件，也可以是任何Linux下可执行的脚本文件

```C
/*
返回值
  成功：无返回值，exec函数族的函数执行成功后不会返回，因为调用进程的实体，包括代码段，数据段和堆栈等都已经被新的内容取代，只留下进程ID等一些表面上的信息仍保持原样
  失败：返回一个-1，从原程序的调用点接着往下执行
参数说明
path：可执行文件的路径名字
arg：可执行程序所带的参数，第一个参数为可执行文件名字，没有带路径且arg必须以NULL结束
file：如果参数file中包含/，则就将其视为路径名，否则就按 PATH环境变量，在它所指定的各目录中搜寻可执行文件。

说明：
l : 使用参数列表
p：使用文件名，并从PATH环境进行寻找可执行文件
v：应先构造一个指向各参数的指针数组，然后将该数组的地址作为这些函数的参数。
e：多了envp[]数组，使用新的环境变量代替调用进程的环境变量
*/
int execl(const char *path, const char *arg, ...);
int execlp(const char *file, const char *arg, ...);
int execle(const char *path, const char *arg,..., char * const envp[]);
int execv(const char *path, char *const argv[]);
int execvp(const char *file, char *const argv[]);
int execvpe(const char *file, char *const argv[], char *const envp[]);
```

# 三、守护进程

`/etc/init.d/` `/etc/rcS.d/`目录可查看Linux守护进程



## 3.1 Linux守护进程

### 3.1.1 概念

进程：每个进程都有一个父进程；当子进程终止时，父进程会得到通知并能取得子进程的退出状态

命名空间：供了一种内核级别隔离系统资源的方法，通过将系统的全局资源放在不同的`Namespace`中，来实现资源隔离的目的

进程组：每个进程属于一个进程组；每个进程组都有一个进程组号，该组号等于该进程组组长的PID号
				一个进程只能为它自己或子进程设置进程组ID号

进程组组长：groupleader，组长进程可以创建进程组及该组中的进程，进程组的创建**从第一个进程（组长进程）加入开始**

会话期：会话期（session）是一个或多个进程组的集合；`setsid()`函数可以建立一个会话期

### 3.1.2 linux守护进程编写步骤

1. 脱离控制终端tty，让父进程为`init`
2. 禁止进程重新打开控制终端
3. 关闭打开的文件描述符：进程从创建它的父进程那继承了打开的文件描述符，不关闭会浪费系统资源，造成进程所在的文件系统无法卸下以及引起无法预料的错误
   	伪代码：`for(i = 0; i关闭打开的文件描述符close(i))`
4. 改变当前工作目录：进程活动时，其工作目录所在的文件系统不能卸下，一般需要将工作目录改变到根目录
   	`chdir()`
5. 重设文件创造掩码：进程从创建它的父进程哪里继承了文件创建掩码，它可修改守护进程所创建的文件的存取位，因此需要将文件掩码清除（`umask(0)`)

### 3.1.3 sample_写日志守护进程

### 3.1.4 杀死守护进程

## 3.2 额外需要研究的内容

核心服务通常在独立的进程中执行

必须让应用程序可以进行跨进程的通信

因为共享，所以必须确保多线程安全

生命周期管理（SM）

# 随笔

2^32 bit = 4 G

2^10 bit = 1 k

2^20 bit = 1 M

2^30 bit = 1 G