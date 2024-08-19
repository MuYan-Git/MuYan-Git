==**Linux_韦东山**==

# 一 基础概念

挂载

对于Windows，可认为C盘挂载了磁盘的第一个分区，D盘挂载了第二个分区，根挂载在第一个分区，则访问这个根的时候就是在访问这个磁盘的第一个分区，根下创建了个文件，那这个文件也放在这个磁盘的第一个分区，挂载在哪个分区，则这个文件就在哪个分区

进程：进行中的实例程序，每个进程都包含有进程运行环境、内存地址空间、进程ID和至少一个被称为线程的执行控制流等资源。同一个程序可以实例化为多个进程实体

## Ubuntu文件系统

- Linux的目录中有且只有一个根目录 /
- linux的各个目录存放的内容是规划好的 ，不乱放文件
- Linux是以文件的形式管理我们的设备，因此**Linux系统，一切皆文件**

Linux目录结构

/bin Binary缩写

/sbin SuperUser

/selinux security-enhanced 安全子系统

![image-20240121161149761](C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240121161149761.png)



**认识终端**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240121162731700.png" alt="image-20240121162731700" style="zoom: 50%;" />

Linux的命令格式

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240121162907782.png" alt="image-20240121162907782" style="zoom: 50%;" />

**Shell是什么**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240121170302078.png" alt="image-20240121170302078" style="zoom: 67%;" />

**怎么设置PATH**

​        以在PATH中添加/home/book目录为例：

- 永久设置方法1

修改该/etc/environment，比如：`sudo gedit /etc/environment`，然后最后添加/home/book，然后重启系统或重新登录：

`PATH = "/usr/local/sbin:/usr/local/bin:usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/localgames:home/book"`

- 永久设计方法2

修改~/.bashrc(**终端输入 gedit ~/.bashrc**)，在行尾添加或修改以下代码，然后重启系统或重新登录

`xport PATH=$PATH:/home/book`

- 临时设置

在终端执行以下命令，这只对当前终端有效：

`export PATH=$PATH:/home/book`

shell会把第一个字符串寻找应用程序，然后把剩下的字符串当作参数传给应用程序的main函数

sda1：sd表示磁盘，a表示第一个磁盘， 1表示这个磁盘里面的第一个分区

Linux开发的时候为什么要用Linux服务器

Linux服务器里面可以编译Uboot、kernel、APP/驱动程序

为什么不用用windows：因为有些链接文件、设备节点，Windows来说不支持这些文件格式和设备节点

Linux Shell简介：

Shell的意思是“外壳”， 在Linux中它是一个程序，比如/bin/sh、/bin/bash等。它负责接收用户的输入，根据用户的输入找到其他程序并运行。比如我们输入“ls”并回车，shell程序找到“ls”程序并运行，把结果打印出来

# 常用命令

## VI编辑器

```
gedit [要处理的文件]
```





# Makefile



---

所以Linux下，mount挂载的作用，就是**将一个设备（通常是存储设备）挂接到一个已存在的目录上。**访问这个目录就是访问该存储设备。linux操作系统将所有的设备都看作文件，它将整个计算机的资源都整合成一个大的文件目录。我们要访问存储设备中的文件，必须将文件所在的分区挂载到一个已存在的目录上，然后通过访问这个目录来访问存储设备。挂载就是把设备放在一个目录下，让系统知道怎么管理这个设备里的文件，了解这个存储设备的可读写特性之类的过程。

新分区会覆盖掉旧分区，新分区被删除之后，旧分区的挂载也会重现

如果执行的程序没有绝对路径和相对路径那么shell就会到环境变量所指示位置来寻找。

> 在PATH环境变量中所指示的目录中都没有对应的程序那么就会显示：“Command xxx not found”

RiChard Stallman

主要发行版本：Ubuntu、Redhat、CentOSE、红旗Linux、Surse

主要操作系统有：Windows、车载系统、Linux、Android

韩顺平Linux、兄弟连Linux
