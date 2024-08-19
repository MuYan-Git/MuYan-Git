2023-2024
吴主华_嵌入式软件学习 _Linux

# Linux基础命令笔记

## Shell

Shell界面：外壳，Linux Shell就是Linux操作系统的外壳，为用户提供**操作系统的接口**

是命令解析器、交互界面，从标准输入上接受用户命令，将命令进行解析并传递给内核，内核根据命令作出相应的动作

## 查看和配置网卡信息

|    命令     |           对应英文            |               作用                |
| :---------: | :---------------------------: | :-------------------------------: |
|  ifconfig   | configure a network interface | 查看/配置计算机当前的网卡配置信息 |
| ping ip地址 |             ping              |  检测到目标ip地址的连接是否正常   |

1. 网卡和IP地址

- 网卡：专门负责网络通信的硬件设备
- IP地址：是设置在网卡上的地址信息

> 可把电脑比作电话，则网卡-->SIM卡，IP地址-->电话号码

---

2. 终端中快速定位网卡的IP地址

```
ifconfig				//查看网卡配置信息
ifconfig | grep inet    //查看网卡对应的IP地址
```

> 一台计算机中又可能会有一个物理网卡和多个虚拟网卡，在Linux中物理网卡的名字通常以`ensXX`表示

- `127.0.0.1 `被称为本地回环/环回地址，一般用来测试本机网卡是否正常

---

3. ping

`ping `一般用于检测当前计算机到目标计算机之间的网络是否通畅，数值越大，速度越慢

​			原理：网络上机器都有==唯一确定==的IP地址，我们给目标**IP**地址发送一个数据包，对方就要返回一个数据包，根据返回的数据包以及时间，就可以确定主机的存在

```
ping IP地址		//检测到目标主机是否连接正常
Ping 127.0.0.1	 //检测本地网卡工作正常
```

---

域名和端口号

域名：由一串用点分割的名字组成，例www.itcast.cn ，是IP地址的别名，方便用户记忆
端口号：通过端口号可以找到计算机上运行的应用程序

+ SSH服务器的默认端口号是`22`，如果是默认端口号，在连接时，可省略
+ 常见服务端口号列表：

| 端口号 |   服务    |
| :----: | :-------: |
|   20   | SSH服务器 |
|   80   | Web服务器 |
|  443   |   HTTPS   |
|   21   | FTP服务器 |

## 虚拟机的网络连接三种方式说明

- **桥连接**：Linux可以和其他系统通信，通信畅通，但可能造成IP冲突

教室网络环境下有张三和李四两台电脑（两台设备两个ip地址），在同一个网络环境则在同一个网段，李四在电脑上装虚拟机，如果是桥联，那么虚拟机网段也在同一网段（192.168.0.xxx），在同一网段下张三可以找到李四的操作系统，同样张三也可以和李四的linux通信，但是如果教师网络环境下太多设备就会IP地址不够用，去掉广播（255）和网关（1）地址只剩255-2= 253个设备IP

![image-20240122133258238](C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240122133258238.png)

- **NAT模式**：网络地址转换方式，Linux可以访问外网，不会造成ip冲突，但张三李四找不到这个linux（一般推荐该模式）

教室网络环境下还有个王五，装了个Linux系统，如果它选了NAT模式，那它的Windows就会出现两个IP地址，一个是原来的192.168.0.xxx，另外一个就是192.148.100.200，那么这个linux操作系统就会在.100.xxx这里，这时windows里面会有两个ip，原IP仍然和教室网络环境构成网络，另外一个ip和Linux自成一个网络环境，这是linux不占用原本的192.168.0的IP地址，这样就不会产生IP冲突

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240122134226305.png" alt="image-20240122134226305" style="zoom: 80%;" />

- 主机模式：IP独立，liunx是一个独立的主机，不访问外网，

Linux磁盘空间的划分

/boot分区 200M足够

swap分区 2048M 当系统内存不够用时可以用swap暂时替代内存，但是是虚拟内存，不是真实的，所以不能分配太多

空闲全部分给根分区

**为什么需要远程登录Linux**

公司开发情况：

1. Linux服务器是开发小组共享的
2. 正式上线的项目是运行再公网的
3. 因此程序员需要远程登录到centos进行项目管理和开发
4. 画出简单的网络拓扑示意图
5. 远程登录客户端有Xshell，Xftp 

- XShell：远程登录到Liunx
- XFpt：远程上传和下载文件软件
- mysql 安装文件

> 使用远程服务需要Linux需要先开启一个sshd服务（监听22端口等待连接）
>
> 理论上来说为了保证Linux的安全性只会开放22号端口 端口开得越多，安全性越弱

XShell远程登录Linux后就能使用指令操作

Xftp5

基于windows平台的SFTP、FTP文件传输文件

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240122155154700.png" alt="image-20240122155154700" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240122155430769.png" alt="image-20240122155430769" style="zoom:50%;" />

**关机重启注销**

shutdown halt reboot syn

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240122160448571.png" alt="image-20240122160448571" style="zoom:50%;" />

**用户登录注销**

用户、文件所在组均可改变

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240122160602623.png" alt="image-20240122160602623" style="zoom:50%;" />

**用户管理** **创建用户指定密码**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240122161125928.png" alt="image-20240122161125928" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240122161512802.png" alt="image-20240122161512802" style="zoom:50%;" />

删除用户时一般不会删除家目录

高权限用户到低权限用户时不需要输入密码

用户名 用户密码 用户ID 组ID 家目录 用户对应的shell编程



<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123165917186.png" alt="image-20240123165917186" style="zoom:80%;" />

## **面试题：如何找回root密码：**

（注：这种方式必须到线下服务器进行登录，不能远程登录）

思路进入到单用户模式，然后修改该root密码。因为进入单用户模式，root不需要密码就可以登录

操作：开机->在引导时输入回车键->看到一个界面输入“e”->看到一个新的界面，选中第二行（编辑内核）在输入“e”->在这行最后输入“1”->在输入回车键->再输入b，这时就会进入单用户模式。

这是，就进入到单用户模式，使用passwd指令来修改root密码。

> 参考网址：[尚硅谷韩顺平Linux入门]([22.Linux实操篇_实用指令 运行级别和找回root密码_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1aE411s7tw?p=22&vd_source=0300b13771e59496da2d2b2161cc684c))

## **常用指令**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123172205958.png" alt="image-20240123172205958" style="zoom: 50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123172345971.png" alt="image-20240123172345971" style="zoom:50%;" />

**文件目录类指令**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123172723712.png" alt="image-20240123172723712" style="zoom:50%;" />

绝对路径：从根目录开始定位 /home

相对路径：从当前目录开始定位到需要的目录去 ../home

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123173420432.png" alt="image-20240123173420432" style="zoom:67%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123173557311.png" alt="image-20240123173557311" style="zoom: 33%;" />

案例1：cd /root

案例2：cd ../../root

案例3：cd ..

案例4：cd ~

### （1） **mkdir(make directory)**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123174146113.png" alt="image-20240123174146113" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123174256170.png" alt="image-20240123174256170" style="zoom: 67%;" />

### （2） **rmdir(remore directory)**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123174743163.png" alt="image-20240123174743163" style="zoom:50%;" />

### （3） **touch**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123174933682.png" alt="image-20240123174933682" style="zoom:50%;" />

### （4） **cp（重要）**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123175200079.png" alt="image-20240123175200079" style="zoom:50%;" />

案例1：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123175442684.png" alt="image-20240123175442684" style="zoom:50%;" />

案例2：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123175829493.png" alt="image-20240123175829493" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123175950182.png" alt="image-20240123175950182" style="zoom:50%;" />

### （5） **rm**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123180032917.png" alt="image-20240123180032917" style="zoom: 80%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123204358753.png" alt="image-20240123204358753" style="zoom:50%;" />

### **（6） mv**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123205019980.png" alt="image-20240123205019980" style="zoom:50%;" />

### （7） **cat**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123205341704.png" alt="image-20240123205341704" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123211440544.png" alt="image-20240123211440544" style="zoom:50%;" />

### （8） **more**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123211644369.png" alt="image-20240123211644369" style="zoom: 67%;" />

### （9） **less**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123212449798.png" alt="image-20240123212449798" style="zoom:50%;" />

### （10） 重定向 “>” 和 追加 “>>”

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123212958318.png" alt="image-20240123212958318" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240123213205113.png" alt="image-20240123213205113" style="zoom: 50%;" />

### （11）echo

### （12）head / tail 

### （13） ln（小写）

### （14） history

### （15） date

### （16） find

查找文件 按照文件大小 按照通配符

### （17） locate

查找文件 快速定位文件路径

### （18） grep 和 管道符 | 

grep 过滤查找 

管道符 | 将前一个命令的处理结果输出传递给后面的命令处理

（19）gzip gunzip

（20）组管理

（21） 权限（0-9位）

文件类型：b：块文件（硬盘）；l：软连接；-：普通文件；d：目录；c：字符设备（键盘、鼠标）

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125121537967.png" alt="image-20240125121537967" style="zoom:50%;" />

rwx权限作用在**文件上**和作用在**目录上**是有区别的

目录是一种特殊的文件

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125122122372.png" alt="image-20240125122122372" style="zoom:50%;" />

修改权限

chmod

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125122734309.png" alt="image-20240125122734309" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125123112499.png" alt="image-20240125123112499" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125123313439.png" alt="image-20240125123313439" style="zoom:50%;" />

数字变更权限

实践：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125130833891.png" alt="image-20240125130833891" style="zoom:50%;" />

（1） 创建组 groupadd police/ groupadd bandit

（2） 创建用户

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125131353145.png" alt="image-20240125131353145" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125131451391.png" alt="image-20240125131451391" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125131544514.png" alt="image-20240125131544514" style="zoom:50%;" />

## 任务调度

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125132401401.png" alt="image-20240125132401401" style="zoom:67%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125132746450.png" alt="image-20240125132746450" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125133322622.png" alt="image-20240125133322622" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125133814181.png" alt="image-20240125133814181" style="zoom:50%;" />

参数占位符说明

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240125133901631.png" alt="image-20240125133901631" style="zoom:50%;" />

## 磁盘分区

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126141649654.png" alt="image-20240126141649654" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126142330836.png" alt="image-20240126142330836" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126142721226.png" alt="image-20240126142721226" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126152718852.png" alt="image-20240126152718852" style="zoom:50%;" />

[Linux添加磁盘]([43.Linux实操篇_给Linux添加一块新硬盘_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1aE411s7tw?p=43&vd_source=0300b13771e59496da2d2b2161cc684c))

## 网络配置

[韩顺平Linux网络配置](https://www.bilibili.com/video/BV1aE411s7tw?p=45)

指定固定IP

## 进程管理（重要）

基本介绍

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126165450856.png" alt="image-20240126165450856" style="zoom:50%;" />

**ps**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126170042467.png" alt="image-20240126170042467" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126170134236.png" alt="image-20240126170134236" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126170220714.png" alt="image-20240126170220714" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126170438643.png" alt="image-20240126170438643" style="zoom:50%;" />

终止进程

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240126170556965.png" alt="image-20240126170556965" style="zoom:50%;" />

## 服务管理（重要）

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127143203981.png" alt="image-20240127143203981" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127145014426.png" alt="image-20240127145014426" style="zoom:50%;" />

> 如果不小心将默认的运行级别设置成0或7，怎么处理？
>
> 进入单用户模式，修改成正常的即可

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127145724865.png" alt="image-20240127145724865" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127150450217.png" alt="image-20240127150450217" style="zoom:67%;" />

动态监控

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127150858215.png" alt="image-20240127150858215" style="zoom:67%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127150933101.png" alt="image-20240127150933101" style="zoom:67%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127151131012.png" alt="image-20240127151131012" style="zoom:67%;" />

案例1 直接输入`top`显示一下内容

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127151846167.png" alt="image-20240127151846167" style="zoom:67%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127152047341.png" alt="image-20240127152047341" style="zoom:50%;" />

监控网络服务指令

`netstat -anp | more` 查看所有的网络服务 **可以看到有哪些外部的IP连接到这个服务**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127152633046.png" alt="image-20240127152633046" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127152903068.png" alt="image-20240127152903068" style="zoom:67%;" />

## RPM包管理

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127153259719.png" alt="image-20240127153259719" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127153428814.png" alt="image-20240127153428814" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127154325642.png" alt="image-20240127154325642" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127155659613.png" alt="image-20240127155659613" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127160637893.png" alt="image-20240127160637893" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127160901055.png" alt="image-20240127160901055" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127160939163.png" alt="image-20240127160939163" style="zoom:50%;" />

yum

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127161428644.png" alt="image-20240127161428644" style="zoom:50%;" />

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240127162256367.png" alt="image-20240127162256367" style="zoom:50%;" />



## 一、Linux文件系统及文件基础

### 1.1 文件系统概述

#### 1.1.1 文件系统的定义

文件系统时一种组织计算机文件和资料的方法
操作系统中封装的系统服务程序，实际上是一个软件程序，用来存储和管理计算机文件和资料

#### 1.1.2 文件系统分类

- 磁盘文件系统：NTFS、EXT3
- 闪存文件系统：JFFS2，YAFFS
- 数据库文件系统：BFFS，WINFS
- 网络文件系统：NFS
- 虚拟文件系统：VFS（Proc）

#### 1.1.3 文件系统功能

- 能定义文件的组织方式：文件结构
- 提供简历和存取文件的环境：目录和文件
- 能对文件存储器空间进行组织和分配
- 负责文件存储并对存入的文件进行保护和检索
- 负责建立文件、存入、读出、修改、转储文件，控制文件的存取，撤销文件等

### 1.2 linux文件系统

#### 1.2.1 EXT3

基于日志方式的文件系统；系统中每个文件都是有索引，用户对对文件的每一个操作都会记录日志，形成一个任务队列排着执行

#### 1.2.2 SWAP

swap是交换分区的文件系统，类似windows的虚拟内存；swap是linux的虚拟内存，在安装时要设好大小，是物理内存的两倍

虚拟内存的实现有两种方式
		一：进行内存的排列像内存池一样，进行一个优化
		二：把硬盘上的空间模拟成内存

#### 1.2.3 linux文件系统特点

- Linux系统中一切皆文件：把设备（硬盘、软驱、光驱等）都看作文件，文件夹也看作文件
- Linux文件类型：普通文件（-）、目录文件（d）、链接文件（l）、块设备（b）、字符设备（c）、Socket（s）、管道文件（p）
- Linux文件属性：蓝色 ==->== 目录；绿色 ==->== 可执行；浅蓝色 ==->== 链接；红色 ==->== 压缩；灰色 ==->== 其他 （仅供参考）

#### 1.2.4 Linux文件系统的目录结构

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240121161149761.png" alt="image-20240121161149761" style="zoom: 50%;" />

#### 1.2.5 Linux与windows目录结构的区别

- 根目录：Linux：/；windows：\
- 命名大小写区分：Linux命名区分大小写；windows命名不区分大小写
- 结构管理：Linux：磁盘逻辑结构管理物理结构，格式化将磁盘分为很多的文件块区；
  				  Windows：物理结构管理逻辑结构，先分区再格式化建立结构

### 1.3 Linux 应用程序的安装与卸载基础

#### 1.3.1 Linux应用程序安装基础知识

1.软件安装包：类似windows下的安装程序，如打好包的exe文件；Linux下的打包文件通常都是tar，打包格式可自定义（自定义后缀名）

2.Linux下常见的软件程序包（安装包的具体格式规范可以到相应官网查看）
		rpm：红帽系统定义的软件包文件格式
		deb：ubuntu下面的主要的安装包的格式

3.Linux下的安装包的命名格式：`软件安装包名称_版本号-修订版本_体系架构.扩展名 例：aptitude_6.0.3-3.2ubuntu1_i386.deb`

4.Linux安装卸载应用程序的方式

- 安装包离线安装和卸载：`dpkg  dpkg -i <package> 安装包    dpkg -p <package> 移出包和配置文件`
- 源文件编译安装和卸载：配置configure、编译 make 、安装install
- 程序管理包在线安装和卸载：aptitude；`apt-get install <package> 安装 apt-get remove -purge <package> 卸载完全`

---

---

麦子学院_王海宁_嵌入式Linux底层系统开发 + 系统移植 + 内核文件系统
[B站链接]([【麦子学院】嵌入式Linux底层系统开发 +系统移植+内核文件系统_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV19b411i784/?spm_id_from=333.337.search-card.all.click&vd_source=0300b13771e59496da2d2b2161cc684c))

# 阶段一 嵌入式Linux系统移植

Linux内核的基本概念

1. **linux内核的特性：**

可移植性，支持的硬件平台广泛、超强的网络功能、多任务多用户系统、模块化的设计

2. **五大子系统：**

- 进程管理子系统
- 内存管理子系统
- 文件系统子系统
- 网络协议子系统
- 设备管理子系统

## 第一章：探寻系统移植的世界

系统移植的4大点：交叉编译器环境、bootloader功能子系统、内核核心子系统、文件系统子系统

### 1.1 概述

嵌入式Linux系统移植要点

- 搭建交叉开发环境
- bootloader的选择和移植
- **kernel的配置、编译、移植和调试**
- **根文件系统的制作**

#### 1.1.1 移植的基本步骤

1. 确定目标机、主机的连接方式

- UART异步串行通信接口（串口）：速率低，实用性强，既可输入也可输出
- USB串行通信接口：速度快，驱动要移植修改
- TCP/IP网络通信接口：速度快（10/100Mbps）驱动要移植
- Debug Jtag调试接口：方便快捷，价格高

2. 安装交叉编译器

X86和ARM程序不兼容，则需要它进行开发，方法：

- 安装芯片厂商已经编译好的工具链：`arm-none-linux-gnueabi-    arm-linux-    arm-none-eabi-    arm-elf-`

  > eabi：针对嵌入式的标准接口 gnu：开源

- 自己动手编译交叉工具链：耗时、麻烦、一般不推荐。学习参考：《The GNU Toolchain for ARM Target HOWTO》

3. 搭建主机-目标机数据传输通道

网络相关服务器的配置：TFTP、NFS

4. 编译三大子系统：bootloader功能子系统、内核核心子系统、文件系统子系统

5. 烧写测试

### 1.2 交叉编译工具集

完成在一个结构**编译另外**一个结构的编译器（主机和目标机不同平台 可能一个X86一个ARM）

flie命令：可以查看编译出来文件的具体属性（包括显示所在编译平台的架构）

### 1.3 工具集介绍

起分析问题和解决问题的辅助工具

- readelf：一般用于查看ELF格式的文件信息，常见的文件如在Linux上的可执行文件，动态库(.so)或者静态库(.a) 等包含ELF格式的文件
- size：用于查看目标文件、库或可执行文件中各段及其总和的**大小**，是 GNU 二进制工具集 GNU Binutils 的一员
- nm：列出 .o, .a, .so 中的符号信息（不是直接作用于 main.c、test.h、test.c 等文件），包括诸如符号的值、符号类型以及符号名称等。所谓符号，通常指定义出的函数、全局变量等等
- strip：通过除去绑定程序和符号调试程序使用的信息，减少扩展公共对象文件格式（XCOFF）的对象文件的大小
- strings：在对象文件或二进制文件中查找可打印的字符串。字符串是4个或更多可打印字符的任意序列，以换行符或空字符结束。 strings命令对识别随机对象文件很有用
- objdump：Linux下的反汇编目标文件或者可执行文件的命令，它以一种可阅读的格式让你更多地了解二进制文件可能带有的附加信息
- objcopy：将目标文件的一部分或者全部内容拷贝到另外一个目标文件中，或者实现目标文件的格式转换，是 GNU Binutils 的一员
- addr2line：用于将程序指令地址转换为所对应的函数名、以及函数所在的源文件名和行号。当含有调试信息(-g)的执行程序出现crash时(core dumped)，可使用addr2line命令快速定位出错的位置

### 1.4 环境搭建 *略*



### 1.5 系统移植初识

uboot是bootload中的一个子功能

#### 1.5.1 uboot常用命令介绍

- print：打印（查看）uboot中已经集成好了的**环境变量**

- setenv、saveenv：设置/添加/修改/删除变量值 -> `setenv abc 100 200` ；`saveenv` 把本次设置的环境变量写回存储器（RAM -> Flash）

- nand：`nand [动词] [内存地址] [nandflash的内部地址] [搬移大小]` 常见动词有read、write、erase
           nand中5M空间读到内存21000000 1k -> `nand read 21000000 500000 1024`
- tftp：用于传输文件，FTP让用户得以下载存放于远端主机的文件，也能将文件上传到远端主机放置。tftp是简单的文字模式ftp程序，它所使用的指令和FTP类似

- bootm：**用时一般要传入内核在内存中的地址，然后去该地址处启动内核**；如果不传入内核在内存中的地址，bootm会用默认地址去启动内核，这样不保证能正确启动内核

- go：将程序指针指向某个地方运行


#### 1.5.2 内核启动条件

##### （1） 启动参数设置 

变量bootargs

- root=：root代表启动的根文件系统在哪个设备
  		设备信息：Ram、NFS、flash
- init=：uboot告诉内核启动后，第一个可执行文件init进程从哪里来
- console=：（控制台）内核启动时，使用哪个设备作为控制台

##### （2） 文件系统支持

文件系统可以理解为一个模块或者执行程序，内核和用户交互的**中介**

- NFS：实际开发中用的较多，Network File System的缩写，它最大的功能就是可以**通过网络，让不同的机器、不同的操作系统可以共享彼此的文件**
- Ramdisk：内存磁盘的文件系统，把文件系统中的内容在RAM运行起来，直接把内核指向RAM中；此外在启动参数中要告诉内核是RAM启动的，同时还要告诉RAM需要的设备信息
  		示例：`root=/dev/ram   initrd=21000000,8M   init=/linuxrc   console=ttySAC0`

#### 1.5.3 文件系统的烧写

**NFS**：走TCP/IP协议 服务端是C-S架构 实际开发中debug用的最多的方式 NFS主要目的主要用于驱动工程师测试驱动/内核模块或者应用工程师测试应用程序在版上实际效果的简便方式

- 服务端：`sudo apt-get install nfs-kernel-server`
  	配置：/etc/exports
- 客户端：让内核去找到服务端具体的目录/数据，然后以根的形式取出来
      bootargs参数设置：`root=/dev/nfs nfsroot=192.168.10.110:/home/rocky/work/rootfs ip=192.168.10.122 init=/linuxrc conosole=ttySAC0,115200`

**Ramdisk**：在内存中运行，掉电易失，在路由器中常用Ramdisk作为核心设备

## 第二章 看懂uboot的神秘面纱



### 2.1 Bootloader概述

### bootloader的作用和步骤

boot + loader，

boot目的：跳到C语言中，即bl main
	关闭看门狗，中断，MMU，CACHE
	配置系统时钟
	配置SDRAM控制器（行地址数，列地址数，多少块，周期性充电）
	让sp（指针）指向可读可写的设备区间中（DRAM），满足递减栈的规则
		--- 用哪些模式，就要初始化哪些模式下的SP
		--- 每个模式值不能覆盖其他模式
	代码搬移
		--- 执行速度问题，把程序从存储器（nor-flash）搬移到快速的内存
		--- 只把存储器的一部分代码执行出来，把存储在其他位置上的代码搬移到内存，对应存储器的控制器的初始化

loader目的：执行应用逻辑：电灯、uart，load linux kernel





## 第三章 谈说linux内核的迷宫

### 3.1 linux源码目录层次结构

| 第一级目录          | 第二级目录及文件                                             |
| ------------------- | ------------------------------------------------------------ |
| **`arch`**          | 这个文件夹包含了一个Kconfig文件，它用于设置这个目录里的源代码编译所需的一系列设定。<br/><br/>每个支持的处理器架构都在它相应的文件夹中，如arm64、arm32、x86、mips等。<br/>/boot：内核需要的特定平台代码<br/>/boot/dts：设备树文件<br/>/lib：通用函数在特定体系结构的文件<br/>/math-emu：模拟FPU的代码，在ARM中，使用/math-xxx代替<br/>/mm：特定体系结构的内存管理文件<br/>/include：特定体系的头文件<br/> |
| **`block`**         | 包含块设备驱动程序的代码，该目录用于实现块设备的基本框架和块设备的I/O调度算法。块设备是以数据块方式接收和发送的数据的设备。数据块都是一块一块的数据而不是持续的数据流 |
| **`crypto`**        | 包含许多加密算法的源代码。例如，“sha1_generic.c”这个文件包含了SHA1加密算法的代码。存放`加密`、`压缩`、`CRC校验`等算法相关代码 |
| **`Documentation`** | 存放相关说明文档，很多实用文档，包括驱动编写等               |
| **`drivers`**       | 存放 Linux 内核设备驱动程序源码。驱动源码在 Linux 内核源码中站了很大比例，常见外设几乎都有可参考源码，对驱动开发而言，该目录非常重要。该目录包含众多驱动，目录按照设备类别进行分类，如`char`、`block`、`input`、`i2c`、`spi`、`pci`、`usb`等 |
| **`firmware`**      | 保存用于驱动第三方设备的固件                                 |
| **`fs`**            | 文件系统文件夹。理解和使用的文件系统所需要的所有的代码就在这里。在这个文件夹里，每种文件系统都有自己的文件夹。例如，ext4文件系统的代码在ext4文件夹内。 在fs文件夹内，开发者会看到一些不在文件夹中的文件。这些文件用来控制整个文件系统。例如，mount.h中会包含挂载文件系统的代码。文件系统是以结构化的方式来存储和管理的存储设备上的文件和目录。每个文件系统都有自己的优点和缺点。这是由文件系统的设计决定的 |
| **`include`**       | 存放内核所需、与平台无关的头文件，与平台相关的头文件已经被移动到 arch 平台的include 目录，如 ARM 的头文件目录`<arch/arm/include/asm/>` |
| **`init`**          | 包含内核初始化代码，init文件夹包含了内核启动的处理代码(INITiation)。main.c是内核的核心文件，这是用来衔接所有的其他文件的源代码主文件 |
| **`ipc`**           | 存放进程间通信代码，此文件夹中的代码是作为内核与进程之间的通信层。内核控制着硬件，因此程序只能请求内核来执行任务。假设用户有一个打开DVD托盘的程序。程序不直接打开托盘，该程序通知内核，然后，内核给硬件发送一个信号去打开托盘 |
| **`kernel`**        | 控制内核本身，在该文件夹下有个"power"文件夹，这里的代码可以使计算机重新启动、关机和挂起 |
| **`lib`**           | 包含了内核需要引用的一系列内核库文件代码                     |
| **`mm`**            | 包含了内存管理代码。内存并不是任意存储在RAM芯片上的。相反，内核小心地将数据放在RAM芯片上。内核不会覆盖任何正在使用或保存重要数据的内存区域 |
| **`net`**           | net文件夹中包含了网络协议代码。这包括IPv6、AppleTalk、以太网、WiFi、蓝牙等的代码，此外处理网桥和DNS解析的代码也在net目录 |
| **`samples`**       | 存放提供的一些内核编程范例，如kfifo；后者相关用户态编程范例，如hidraw<br/>此文件夹包含了程序示例和正在编写中的模块代码。假设一个新的模块引入了一个想要的有用功能，但没有程序员说它已经可以正常运行在内核上。那么，这些模块就会移到这里。这给了新内核程序员一个机会通过这个文件夹来获得帮助，或者选择一个他们想要协助开发的模块 |
| **`srcipts`**       | 内核编译所需的脚本。最好不要改变这个文件夹内的任何东西。否则，您可能无法配置或编译内核 |
| **`security`**      | 有关内核安全的代码。它对计算机免于受到病毒和黑客的侵害很重要。否则，Linux系统可能会遭到损坏 |
| **`sound`**         | 包含了声卡驱动，存放声音系统架构相关代码和具体声卡的设备驱动程序 |
| **`tools`**         | 编译过程中一些主机必要工具，这个文件夹中包含了和内核交互的工具 |
| **`usr`**           | 早期用户空间代码（所谓的initramfs）                          |
| **`virt`**          | 内核虚拟机KVM                                                |

平台无关的目录树

drive、firmware、fs、ipc、init、net、include、平台无关核心代码（kernel、lib、mm）

平台相关的目录树

arch

X86：kernel、lib、mm

arm：boot、include、kernel、lib、mm、mach-xxx、plat-xxx

powerpc

mips



内核源码开发的头文件命名规范

## 第四章 文件系统的制作

## 第五章 Linux内核及文件系统移植

系统启动流程：
	bootloader(uboot) -> Linux Kernel(uImage) -> Rootfs(Init) -> Applications

# 阶段二 嵌入式linux驱动基础知识

# 阶段三 嵌入式linux字符设备驱动





---

---

# 随笔

学校里没有一门课程是教你底层代码是怎么写的

理解底层代码的编写方式

ARM指令架构 ==----------==通过U-boot等开源软件==---------->== 直接理解系统原理

一般异常比中断优先级高 因为一些中断可以屏蔽不处理，但是异常不能不处理

通用的Makefile：支持SD卡启动和在uboot下直接运行ram
		差异：
			程序运行时的地址不同：DDR2：0x20000000、SD：0x0
			SD 16kb，需要加头信息进行校验、DDR不需要加头信息

**链接过程确定标签和地址之间的联系**

平台设备驱动机制
