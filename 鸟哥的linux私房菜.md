鸟哥的linux私房菜_ 基础篇
吴主华_嵌入式软件岗

# 第一部分

## 计算机概论

北桥：负责连接速度较快的CPU、内存与显卡等组件（主流架构的北桥内存控制器会整合到CPU中后）

南桥：负责连接速度较慢的设备接口，包括硬盘、USB设备、网卡灯

外频：CPU与外部组件进行数据传输时的速度

倍频：cpu内部用来加速工作性能的一个倍速
	外频 * 倍频 = CPU频率速度

让CPU直接与内存显卡等设备分别进行通信的技术：Intel，QPI（quick path interconnect）和DMI技术；AMD，Hyper Transport技术

硬件的所有操作都要通过操作系统来实现，因此**硬件架构**和**操作系统程序**相匹配

应用程序参考操作系统API，应用程序运行在操作系统之上

驱动程序：

- 操作系统有驱动硬件的能力，应用程序才能够使用该硬件功能
- 操作系统提供API，开发商编写驱动程序
- 使用新硬件功能，必须安装厂商提供的驱动程序才行
- 驱动程序由厂商提供，与操作系统无关

## 第一章 Linux是什么与学习

层次结构：硬件 -> 内核 -> 系统调用 -> 应用程序；操作系统包含**内核和系统调用**两层

软件移植：操作系统从一个硬件平台到另一个不同架构的硬件平台运行的过程

Linux的可移植性：它的程序代码是开源的，可以被修改成适合在各种硬件框架上运行

POSIX规范：Portable Operating System Interface，可移植操作系统接口，规范内核与应用程序之间的接口

Linux：操作系统最底层的内核及其提供的内核工具

## 第二章 主机规划与磁盘分区

在Linux系统中，每个设备都被当做一个文件来对待，几乎所有硬件设备文件都在`/dev`这个目录内

| 设备                      | 设备在linux中的文件名                                        |
| ------------------------- | ------------------------------------------------------------ |
| SCSI、SATA、USB磁盘驱动器 | `/dev/sd[a-p]`                                               |
| U盘                       | `/dev/sd[a-p]`                                               |
| Virtio接口                | `/dev/vd[a-p]`                                               |
| 软盘驱动器                | `/dev/fd[0-7]`                                               |
| 打印机                    | `/dev/lp[0-2]（25针打印机） /dev/usb/lp[0-15]（USB接口）`    |
| 鼠标                      | `/dev/input/mouse[0-15]（通用）`<br />`/dev/psaux（PS/2接口）`<br />/dev/mouse（当前鼠标）` |
| CD-ROM\DVD-ROM            | `/dev/scd[0-1]（通用）`<br />`/dev/sr[0-1]（通用，CentOS较常见）`<br />`/dev/cdrom（当前CD-ROM）` |
| 磁带机                    | `/dev/ht0（IDE接口）`<br />`/dev/st0（SATA/SCSI接口）`<br />`/dev/tape（当前磁带）` |
| IDE磁盘驱动器             | `/dev/hd[a-d]（旧式系统才有）`                               |

磁盘的分区格式

- MBR，Master Boot Record，主引导记录，分区表格式与限制

  主引导记录：可以安装启动引导程序的地方，有446字节

  分区表：记录整块硬盘分区的状态，有64字节	

  MBR分区表的设计**规范定义**了一个分区表项（Partition Table Entry）为16字节，因此整个分区表可以容纳4个分区表项，总共64字节。这种设计是为了在硬盘的第一个扇区（512字节）中同时容纳分区表和引导加载程序

  

- GPT，GUID partition table磁盘分区表

  GPT使用34个LBA（默认512字节）区块来记录分区信息，整个磁盘的最后34个LBA也用来作为另一个备份

### 启动流程中的BIOS与UEFI启动检测程序

主机系统在加载硬件驱动方面的程序的方式：BIOS（旧）、UEFI（新）

CMOS：记录各项硬件参数且嵌入在主板上面的存储器
BIOS：写入到主板上的一个固件（主板启动时，计算机系统会主动执行的第一个程序）

**基于BIOS的启动流程**

- BIOS：启动主动执行的固件，会认识第一个可启动的设备
- MBR：第一个可启动设备的第一个扇区内的主引导记录块，内含启动引导代码
- 启动引导程序（boot loader）：一个可读取内核文件来执行的软件
- 内核文件：开始启动操作系统

Boot loader的主要任务

- 提供选项：用户可以选择不同的启动选项，这也是多重引导的重要功能；
- 加载内核文件：直接指向可使用的程序区段来启动操作系统；
- 转交其他启动引导程序：将启动管理功能转交给其他启动引导程序负责

> 安装多重引导，最好先装Windows再安装Linux
> 原因：Windows在安装时，它的安装程序会主动地覆盖掉MBR以及自己所在分区的启动扇区；Linux在安装时，可以选择将启动引导程序安装在MBR或各别分区的启动扇区（同时可手动设置启动选项）

**UEFI BIOS搭配GPT启动的流程**

UEFI，Unified extensible Firmware Interface，统一可拓展固件接口

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240321094545900.png" alt="image-20240321094545900" style="zoom:50%;" />

UEFI安全启动功能：secure boot，即将启动的操作系统必须要被UEFI锁验证，否则无法顺利启动

### Linux的磁盘分区选择

目录树结构：以根目录为主，向下呈现分支状的目录结构的一种文件架构（Linux所有文件有根目录衍生而来）

挂载：结合目录树的架构与磁盘内的数据，即利用一个目录当成进入点（称挂载点），将磁盘分区的数据放置在该目录下，也就是说进入该目录就可以读取该分区

# 第二部分 Linux文件、目录与磁盘格式

## 第一章 Linux的文件权限与目录配置

### 1.1 用户与用户组

Linux是一个多人多用户的系统因此文件拥有者的角色十分重要

用户（家庭中成员）与用户组（同一个家庭）：家庭有客厅和房间，用户拥有自己的房间，但共用房间之外的客厅，厨房等家庭共同空间
		在Linux中，任何一个文件都具有用户（User）、所属群组（Group）及其他人（Others）三种身份的个别权限

三个相关文件

- `/etc/passwd`：保存系统所有账号、一般身份用户及root相关的喜喜
- `/etc/shadow`：保存个人密码
- `/etc/group`： 保存Linux所有的组名

### 1.2 Linux的文件权限

`ls -al`：`ls`重点显示文件的文件名和相关属性，`-al`表示列出所有的文件详细权限与属性（包含隐藏文件）
每一栏组分别表示：共七个栏组
		[文件类型与权限]  [文件名链接数]  [文件/目录的拥有者账号]  [文件所属用户组]  [文件大小Byte]  [修改日期]  [文件名]

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240321145902360.png" alt="image-20240321145902360" style="zoom:50%;" />

​																	***各权限对应的数字：r -> 4、w -> 2、x -> 1***

**文件属性与权限的修改**

- `chgrp` ：修改文件所属用户组（要修改该的组名须在`/etc/group`文件中存在）
- `chown`：修改文件拥有者（要修改该的组名须在`/etc/passwd`文件中存在）
- `chmod`：修改文件的权限

如果你在某目录下不具有x的权限，那么就无法切换到该目录下，也就无法执行该目录下的任何命令

**linux目录配置**

Linux目录配置的依据：FHS，File system Hierarchy Standard标准，主要目的在于让用户可以了解到已安装软件通常放置在那个目录下

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240321154949940.png" alt="image-20240321154949940" style="zoom:50%;" />

[FHS官网英文文档]([Filesystem Hierarchy Standard (pathname.com)](https://www.pathname.com/fhs/))

**目录树**：
	--- 目录树的起始点为根目录
	--- 每一个目录不止能使用本地分区的文件系统，也可使用网络上的文件系统（通过NFS挂载某特定目录）
	--- 每个文件在此目录树中的文件名（包含完整路径）都是独一无二的

绝对路径：由根目录（/）开始写起的文件名或目录名称
相对路径：相对于目前路径的文件名写法（`./ 或 .`表示当前目录，`../ 或 ..`表示上一层目录）

## 第二章 Linux文件与目录管理

### 2.1 目录与路径

相对路径方便，绝对路径安全

常见处理目录的命令：

- `cd`切换目录，change，directory
- `pwd`显示当前目录，print working directory
- `mkdir`建立一个新目录，make directory
- `rmdir`删除一个**空**目录

**执行文件路径的变量：`$PATH`**

环境变量PATH的存在可以使得我们在任何地方执行类似于`ls`这样的指令

**文件与目录管理**

`ls`：查看文件与目录，可搭配选项与参数

`cp`、`rm`、`mv`：复制、删除、移动
		默认条件中`cp`指令的目标文件的拥有着通常是命令操作者本身

**文件内容查看**

- `cat`：concatenate(串联)，由第一行开始显示文件内容
- `tac`：从最后一行开始显示
- `nl`：显示的时候，同时输出行号
- `more`：一页一页地显示文件内容，可配套命令/键盘辅助阅读
- `less`：一页一页显示内容，可往前翻页，可配套命令/键盘辅助阅读
- `head`：只看前面几行
- `tail`：只看后面几行
- `od`：以二进制方式读取文件内容
- `touch`：修改文件时间或创建新文件
           Linux下文件的时间参数：修改时间（mtime）、状态时间（ctime）、读取时间（atime）
- `umask`：文件默认权限，指定目前用户在建立文件或目录时的权限默认值
           数值权限表示的情况下，新建目录/文件的真实权限为设定的权限数值 - `umask`的数值

文件的隐藏属性：对于系统安全方面十分重要
	`chattr`：配置文件隐藏属性（该命令仅在`ext2、ext3、ext4`的Linux文件系统上可生效）
	`lsattr`：查看隐藏的属性

文件的特殊权限：`SUID、SGID、SBIT`

观察文件的类型：`file` ，可简单判断文件的格式

**命令与文件的查找**

- `which`：查找执行文件，根据[PATH]环境变量规范的路径，去**查找执行文件**的文件名
- `whereis`：只找系统中某些特定目录下的文件
- `locate`：寻找的数据是由已建立的数据库（`/var/lib/mlocate`）中的数据所查找得到
  `updatedb`：根据`/etc/updatedb.conf`的设置去查找系统硬盘内的文件，并更新`/var/lib/mlocate`内的数据库文件
- `find`：可配合时间参数选项来查找文件、可查找特殊权限文件

## 第三章 Linux磁盘与文件系统管理

### 3.1 Linux文件系统

Linux最早的文件系统是`ext2`；文件系统是建立在磁盘上面的
	`ext2`是索引式文件系统，不太需要进行碎片整理，标准的`ext2`以inode为基础

磁盘分区完毕后需要进行格式化之后，操作系统才能够使用该文件系统（为了适配文件属性/权限）

**`ext2`**

格式化过程：区分多个区块群组（block group），每个区块群组都有独立的inode，数据区块，超级区块系统
	文件系统最前面有一个启动扇区（boot sector），这个启动扇区课安装启动引导程序

inode（128 B）：主要记录文件的权限与相关属性，一个文件一个inode，同时记录此文件的数据所在的区块号码
数据区块：记录文件的实际内容，若文件过大，会占用多个区块（理论上一个区块存放一个文件的数据）
超级区块：记录此文件系统的整体信息，包括inode与数据区块的总量、使用量、剩余量以及文件系统的格式与相关信息

区块对照表：block bitmap，查看区块是否被使用，即记录使用与未使用的区块号码
inode对照表：inode bitmap，记录使用与未使用的inode号码
`dumpe2fs`：查询ext系列超级区块信息的命令

**目录树**

目录：Linux下文件系统建立一个目录时，文件系统会分配一个inode与至少一块区块给该目录（`ls -li`可查看inode号）
文件：Linux下的`ext2`建立一个一般文件时，`ext2`会分配一个inode与相对于该文件大小的区块数量给该文件
目录树读取：由根目录开始读起，系统通过**挂载**的信息可以找到挂载点的inode号码，即可得到根目录的inode内容，然后向下读取

**`ext2~4`文件的存取与日志式文件系统的功能**

新增一个文件，文件系统的操作流程：

1. 先确定用户对于欲新增文件的目录是否具有w与x的权限，若有才能新增
2. 根据inode对照表找到没有使用的inode号码，并将新文件的权限/属性写入
3. 根据区块对照表找到没有使用中的区块号码，并将实际的数据写入区块中，且更新inode的区块指向信息
4. 将刚刚写入的inode与区块数据同步更新inode对照表和区块对照表，并更新超级区块的内容

**日志式文件系统**（为避免文件系统不一致（断电、内核错误···）的情况发生）
		文件系统中规划出一个区块，专门记录写入或修改文件时的步骤

1. 预备：当系统写入一个文件时，会先在日志记录区块中记录某个文件准备写入的信息
2. 实际写入：开始写入文件的权限与数据，开始更新metadata的数据
3. 结束：完成数据与metadata的更新后，在日志记录区块当中完成该文件的记录

**挂载点**

挂载：将文件系统与目录树结合的操作；挂载点一定是目录，该目录为进入该文件系统的入口

**VFS**

Linux VFS，Virtual Filesystem Switch，整个Linux识别的文件系统都是通过VFS进行的，VFS可自行设置读取文件系统的行为

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240322111658613.png" alt="image-20240322111658613" style="zoom: 50%;" />

**XFS文件系统**

适合搞容量磁盘与巨型文件，性能较佳，实际上XFS时一个日志式文件系统（`xfs_info`命令查看信息）
XFS在数据分布上主要规划为数据区（data section）、文件系统活动登陆区（log section）、实时运行区（realtime section）

- 数据区：类似ext系列的区块群组，分多个存储区群组（allocation groups）分别防止文件系统所需的数据
- 文件系统活动登陆区：主要被用来记录文件系统的变化
- 实时运行区：主要作用在文件被建立时，起缓冲/转接作用

### 3.2 文件系统的简单操作

- `df`：列出文件系统的整体磁盘使用量
- `du`：查看文件系统的磁盘使用量
- `ln`：硬链接与符号链接
  	硬链接：hard link，只在某个目录下新增一条文件名链接到某**inode号码**的关联记录（不能跨文件系统、不能链接目录）
  	符号链接：symbolic link，建立一个**独立文件**，文件会让数据的读取指向它链接的哪个文件的文件名

### 3.3 磁盘的分区、格式化、检验与挂载

系统中新增磁盘的操作流程：

1. 对磁盘进行分区，以建立可用的硬盘翻去；
2. 对该磁盘分区进行格式化，以建立系统可用的文件系统
3. 对刚建立的文件系统进行检验
4. 在Linux系统上，需要建立挂载点（目录），并将起挂载上来

**磁盘分区状态观察**

`lsblk`：列出系统上的所有磁盘列表，list block device
`blkid`：列出设备的UUID等参数
`parted`：列出磁盘的分区表类型与分区信息

**磁盘分区**

`gdisk`：磁盘分区（新增分区、删除分区）
`partprobe`：更新Linux内核的分区表信息
`fdisk`：处理MBR分区表必须使用该命令

**磁盘格式化**

`mkfs.xfs`：XFS文件系统的创建/格式化
`mkfs.ext4`：`ext4`文件系统的创建/格式化
`mkfs`：综合命令，可支持多种文件系统的格式化功能

**文件系统检验**（使用`xfs_repair`和`fsck.ext4`时，被检查的硬盘分区需要在卸载状态）

`xfs_repair`：当有xfs文件系统错乱时使用该命令处理，可以检查/修复文件系统
`fsck.ext4`：检查和修正`ext4`文件系统错误，`fsck`是综合命令

**文件系统的挂载与卸载**

前提：
	--- 单一文件系统不应该被重复挂载在不同的挂载点（目录中）
	--- 单一目录不应该挂载多个文件系统
	--- 要作为挂载点的目录，理论上应该都是空目录

`mount`：挂载文件系统
`umount`：将设备文件卸载
`mknod`：查看设备
`xfs_admin`：修改xfs文件系统的UUID与Label name
`tune2fs`：修改`ext4`的label name与UUID

### 3.4 设置启动挂载

可在`etc/fstab`中修改

特殊设备loop挂载：挂载CD/ DVD 镜像文件 参数：`[-o loop]`

创建内存交换区（swap）：物理分区创建、文件创建

## 第四章 文件与文件系统的压缩

压缩技术就是将文件中空余的空间填满或将重复的数据进行统计

### 4.1 Linux系统常见压缩命令

- `gzip`：默认状态下原本的文件会被压缩称为`.gz`后缀的文件，源文件就不再存在
- ``bzip2`：压缩比比`gzip`要好，用法和`gzip`相似，压缩文件后缀名为`.bz2`
- `xz`：压缩比更高，但花费时间较多

### 4.2 打包命令tar

tar可将多个目录或文件打包成一个大文件，同时还可以通过`gzip、bzip2、xz`的支持将该文件同时进行压缩

压缩：`tar -jcv -f filename.tar.bz2`要被压缩的文件或目录名称
查询：`tar -jtv -f filename.tar.bz2`
解压缩：`tar -jxv -f filename.tar.bz2 -C`欲解压缩的目录

XFS文件系统的备份与还原：备份`xfsdump`、还原`xfsrestore`

其他常见的压缩与备份工具：
		--- `dd`可读取磁盘设备的内容（几乎直接读取扇区），然后将整个设备备份成一个文件
		--- `cpio`配合`find`等可以查找文件的命令来告知`cpio`该备份的数据在哪里

# 第三部分 学习shell与shell script

## 第一章 vim程序编辑器

vi的三种模式：命令模式、编辑模式、命令行模式
		--- 一般命令模式：vi打开文件默认进入的模式，可以利用方向按键移动光标，可以删除字符/整行，可以复制/粘贴内容
		--- 编辑模式：按下`[i l o O a A r R]`任一字符进入编辑模式（界面左下方出现`[INSERT]/[REPLACE]`），`Esc`退出编辑模式
		--- 命令行模式：读取/存储文件其他额外功能
*一般命令模式可与编辑模式及命令行模式切换，但编辑模式与命令行模式之间不可互相切换*

[vi操作基础参考学习视频]([Linux基础-vi编辑器_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1w4411T7Jx/?spm_id_from=333.337.search-card.all.click&vd_source=0300b13771e59496da2d2b2161cc684c))

## 第二章 BASH

硬件、内核与用户的相关图例：硬件 -> 内核 -> 使用者界面

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240323110905788.png" alt="image-20240323110905788" style="zoom: 50%;" />

系统某些服务在运行过程中，会去检查用户的可用shell，而这些shell的查询就是通过`/etc/shells`这个文件实现的

### 2.1 shell的变量功能

变量：以一组文字或符号等，来替换一些设置或一串保留的数据

**影响bash环境操作**
		`PATH`变量，执行命令时，系统通过`PATH`这个变量里面的内容所记录的路径顺序查找命令（无记录则`command not found`）
		辅助脚本程序设计（shell script），用变量来替代路径

**变量的使用与设置**
		`echo`：使用变量命令； `unset`：取消变量的方法
	--- 变量的设置规则：
			a 变量与变量内容以一个等号`=`来连接，例：`myname=VBird`（等号两边不能直接空格）
			b 变量名称只能是英文字母与数字，但开头不能有数字
			c 双引号内的特殊字符如`$`等可以保有原本的特性、单引号内地特殊字符仅为一般字符（纯文本）

**环境变量的功能**
		根目录（主文件夹）的变换、提示符的显示，执行文件查找的路径
		`env`：该命令可观察环境变量与常见变量说明
		`set`：该命令可观察所有变量（含环境变量和自定义变量）
		`export`：自定义变量转成环境变量，决定该变量**是否会被子进程所继续引用**
		`locale -a`：查看Linux支持的语系种类

**变量的有效范围**
		当启动一个shell，操作系统会分配一内存区域给shell使用，此内存中的变量可以让子进程使用
		若在父进程利用`export`功能，可以让自定义变量的内容写到上述的内存区域当中
		当加载**另一个shell**时(即启动子进程，而离开原本的父进程)，子shell可将父shell的环境变量所在的内存区域导入自己的环境变量中

**变量键盘读取、数组与声明**
		`read`：读取来自键盘输入的变量
		`example[1]="muyan"`：数组变量类型
		`declare`/`typeset`：声明变量类型

`ulimit`：限制用户的某些系统资源，包括可以开启的文件数量，可使用的CPU时间、可用的内存总量等

变量内容的删除、取代和替换（可选）
		`echo ${path#/*:} / echo ${path##/*:}`：符合替换文字的最短（#）和最长（##）那一个
		变量的测试与内容替换：包括多种设置方式

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240323143912045.png" alt="image-20240323143912045" style="zoom:50%;" />
<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240323143948387.png" alt="image-20240323143948387" style="zoom:50%;" />

**命令别名与历史命名**
		`alias`：查看当前命令别名列表，添加命令别名
		`unalias`：取消命令别名
		`history`：历史命令

### 2.2 Bash shell的操作环境

由于系统中存在环境配置文件，让bash在启动时直接读取者系配置文件，以规划号bash的操作环境
		这些配置文件可以分为**全局系统配置文件**和**用户个人偏好配置文件**

login与non-login shell
	login shell：取得bash时需要完整的登录流程，该流程称为login shell即登陆时需要输入用户的账号密码，此时取得的bash即为login shell
	non-login shell：取得bash的方法不需要重复登陆的操作，举例，以X window登录Linux后，再以X的图形化接口启动终端，此时这个终端接口并没有需要在此输入账号密码，该bash的环境被称为non-login shell
			*以上两种情况下取得的bash，读取的配置文件并不一样*

**login shell读取的配置文件**
		`/etc/profile`：系统整体的设置、`~/.bash_profile 或 ~/.bash_login 或 ~/.profile`：属于用户个人设置，添加自己的数据即写入
		`source`：读入环境配置文件的命令

**`/etc/profile`**：该配置文件会利用用户标识符（UID）来决定很多重要的变量数据
	主要包括的变量有：**`PATH MAIL USER HOSTNAME HISTSIZE umask`**
<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240323145705938.png" alt="image-20240323145705938" style="zoom:50%;" />

### 2.3 数据流重定向

将某个命令执行后应该出现在屏幕上的数据，给它传输到其他的地方
	命令执行过程的数据传输情况如下：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240323150703471.png" alt="image-20240323150703471" style="zoom:50%;" />

数据重定向可以将标准输出（standard output，stdout）和标准错误输出（standard error output，stderr）分别**传送**到其他的文件或设备
	标准输出：命令执行成功，将该文件内容显示到屏幕上
	标准错误输出：命令执行失败，返回错误信息
**传送**所用的特殊字符
		标准输入，stdin，代码为0，使用`<`或`<<`
		标准输出，stdout，代码为1，使用`>`或`>>`
		标准错误输出，stderr，代码为2，使用`2>`或`2>>`

### 2.4 管道命令pipe

- 管道命令仅会处理标准输出，对于标准错误会予以忽略
- 管道命令必须要能够接受来自前一个命令的数据称为标准输入继续处理才行

**选取命令**

`cut`：主要用于将同一行里面的数据进行分解，最常使用在分析一些数据或文字数据时
`grep`：分析一行信息，并提取所需要的信息

**排序命令**

`sort`：根据不同的数据形式排序
`uniq`：将重复的行删除掉只显示一个
`wc`：查看文件中有多少字，多少行，多少字符

**双向重定向**：`tee`，同时将数据流分送到文件与屏幕，而输出到屏幕的即`stdout`（实际上是双重输出）

**字符转换命令**

`tr`：删除一段信息当中的文字，或进行文字信息的替换
`col`：处理将[tab]按键替换称为空格键
`join`：处理两个文件当中的数据，有相同数据的一行，加在一起
`paste`：将两行“贴”在一起，且中间以[tab]键隔开
`expand`：将[tab]键转成空格键

`split`：划分命令，将大文件依据文件大小或行数进行划分成小文件

`xargs`：参数代换，产生某个命令的参数，`xargs`可读入`stdin`的数据并以空格符或换行符作为识别符，将`stdin`的数据分隔成参数

## 第三章 正则表达式与文件格式化处理

正则表达式可以选取系统重要信息，以便查看报表简化管理流程

正则表达式：处理字符串的方法，以行为单位来进行字符串的处理操作，通过一些特殊符号的父子，让用户轻易完成【查找、删除、替换】某特定字符串的**处理过程**

### 3.1 语系对正则表达式的影响

由于不同语系的编码数据并不相同，因此会造成数据选取结果的差异

**grep的其他选项用法**

`grep -n 'the' [某文件]` ：查找某文件的the这个字符串，`-n`是显示行号
`grep -n 't[ae]st' [某文件]`：查找某文件中共同含有[t?st]的关键词语
`grep -n '^the' [某文件]`：查找某文件中行首以the开头的的
`grep -n 'g..d' [某文件]`：查找某文件中出现[g??d]字符串的，
		`.`（小数点）：代表一定有一个任意字符的意思
		`*`（星星号）：代表重复前一个字符，0~∞多次的意思

### 3.2 基础正则表达式字符集合

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240324131826104.png" alt="image-20240324131826104" style="zoom:50%;" />

**sed工具**：本身是管道命令，可分析标准输入，可以对数据进行替换、删除、新增、选取特定行等功能
	sed后面接的操作，必须以单引号括住，`nl /etc/passwd | sed '2,5d'`：将`/etc/passwd`文件输出到屏幕的第2~5行删除

### 3.3 文件格式化与相关处理

`printf '打印格式' 实际内容`：格式化打印
`awk '条件类型1{操作1} 条件类型2{操作2} ···' filename` ：数据处理工具，主要处理每一行的字段内数据
`diff`：对比两个文件之间的差异，以**行单位**来对比，一般用在ASCII村文本文件的对比，常用在同一个文件/软件新旧版本差异上
`cmp`：对比两个文件差异，以**字节单位**对比
`pr`：文件打印设置

## 第四章 shell脚本

shell脚本：shell script，程序化脚本，可分为两部分：shell部分（命令行模式下与系统沟通的工具接口）；script部分：剧本，脚本

shell脚本利用shell的功能所写的一个程序，该程序为纯文本文件，将一些shell的语法与命令（含外部命令）写在里面，搭配正则表达式、管道命令与数据流重定向等功能，完成一定的处理操作

shell可简单的被看成是批处理文件

**shell脚本执行原则**

- 命令从上而下、从左到右执行
- 命令、选项与参数间的多个空格都会被忽略掉
- 空白行也被忽略掉，并且[tab]按键所产生的空白同样视为空格键
- 如果读到Enter符号（CR），就尝试开始执行该行（或该串）命令
- 如果一行内容太多，则可以使用[\Enter]来扩展下一行
- `#`为注释

**shell程序脚本的执行**，假设程序文件名为：`/home/dmtsai/shell.sh`

- 直接命令执行：`shell.sh`文件必须要具备可读可执行（rx）的权限然后：
  		绝对路径：使用`/home/dmtasi/shell.sh`来执行
       相对路径：假设工作目录在`/home/dmtsai/`，则使用`./shell.sh`来执行
- 变量[PATH]功能：将`shell.sh`放在`PATH`指定的目录内，如`:~/bin/`
- 以bash程序来执行：通过`[bash shell.sh]`或`[sh shell.sh]`来执行

脚本示例：hello world

```shell
#!/bin/bash		
#program:hello world!
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "hello world! \a \n"
exit 0
#说明：
#第一行：#!/bin/bash 声明这个脚本使用的shell名称，由于使用的是bash，所以必须以此来声明这个文件内使用的是bash的语法
#以[#!]开头的行被称为：shebang行，当程序执行时，可以加载bash的相关环境配置文件
#第三、四行：环境变量的声明
#第五行：主要程序部分
#第六行：程序中断，并返回一个数值给系统，exit 0表示退出脚本并返回一个0给系统
```

脚本示例：乘法

```shell
#!/bin/bash		
#program:两个数乘法
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "You SHOULD input 2 numbers,Iwill multiplying them! \n"
read -p "first number: " firstnu
read -p "second number: " secnu
total=$((${firstnu}*${secnu}))
echo -e "\nThe result of ${firstnu} x ${secnu} is ==> ${total}"
```

**脚本执行方式的差异**

直接执行方式执行脚本：不论是绝对路径/相对路径还是`${PATH}`内，或是利用bash（或sh）来执行脚本是，该脚本都会使用一个新的bash环境来执行脚本内地命令，也就是说使用这种执行方式时，脚本是在子进程bash中执行的（其变量在父进程的bash中不存在）

利用source来执行脚本：在父进程中执行，执行命令`source showname`，则`showname.sh`会在父进程中执行，因此各项操作都会在原本的bash内生效

### 4.1 判断式

`test`：配合参数检测系统上的某些文件或是相关属性
`[]`：为判断符号，利用该判断符号进行数据到判断，作为shell的判断式时[]内的两端要又空格符来空格
			在中括号[]内的每个组件都需要有空格来分隔
			在中括号内的变量，最好都以双引号括号起来
			在中括号内的常数，最好都以单或双引号括号起来

shell脚本的默认变量：`$0 $1 ··· $# $@ $*`
	`$#`：代表后接的参数【个数】
	`$@`：代表每个变量都是独立的，用双引号括起来”$1“
	`$*`：代表变量之间以空格隔开

`shift`：造成参数变量号码偏移，shift后面可接数字，代表拿掉最前面的几个参数的意思

### 4.2 条件判断式

**`if [条件判断式];then`**：当条件判断式成立时，可以进行then后面的命令操作

```shell
#!/bin/bash
#program:if then
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
read -p "Please input (Y/N): " yn
if ["${yn}" == "Y"] || ["${yn}" == "y"]; then
	echo "OK, continue"
	exit 0
fi
if ["${yn}" == "N"] || ["${yn}" == "n"]; then
	echo "Oh, interrupt!"
	exit 0
fi
echo "I don't know what your choice is" && exit 0

#多重判断语句
if [条件判断式]; then
	#当条件判断式成立时，可执行的命令
else
	#当条件判断式不成立时，可执行的命令
fi
#更多重
if [条件判断式1]; then
	#当条件判断式1成立时，可执行的命令
elif [条件判断式2]; then
	#当条件判断式2成立时，可执行的命令
else
	#当条件判断式1和2都不成立时，可执行的命令
fi
```

**`case ··· esac`**：判断， `switch ... case` 语句类似，是一种多分枝选择结构
	也可以用`$变量`类获取内容（直接执行，交互执行，即让用户输入）

```shell
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo 'Input a number between 1 to 4'
echo 'Your number is:\c'
read aNum
case $aNum in
    1)  echo 'You select 1'
    ;;
    2)  echo 'You select 2'
    ;;
    3)  echo 'You select 3'
    ;;
    4)  echo 'You select 4'
    ;;
    *)  echo 'You do not select a number between 1 to 4'
    ;;
esac
```

### 4.3 function功能

函数可以在shell脚本当中做出一个类似自定义执行命令的东西
		function可以拥有内置变量，内置变量与shell脚本类似，函数名称代表是`$0`，后续接的变量也可以是`$1、$2···`来替换

```shell
#!/bin/bash
#求阶乘示例
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
#解释器选第一个或第二个
factorial() {
value=1
for((i=1;i<=$1;i++))
do
#       value=$[$value * $i]
#       value=$(($value*$i))  等价于   value=$(($value * $i))
        let value*=$i       #等价于  let value=$value*$i  不等价于 let value=$value * $i
done
echo "$1的阶乘是: $value"
}

#调用函数并传参
factorial $1
```

### 4.4 loop循环

**`while do done、until do done`**：不定循环

示例

```shell
#while do done
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
while [ "${yn} != "yes" -a "${yn}" != "YES" ]
do
	read -p "Please input yes/YES to stop this program: " yn
done
echo "OK! You input the correct answer."

#until do done
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
until [ "${yn} == "yes" -o "${yn}" == "YES" ]
do
	read -p "Please input yes/YES to stop this program: " yn
done
echo "OK! You input the correct answer."
```

**`for ··· do ··· done`**：固定循环

必须要符合某个条件的状态，以下例子当中，变量`$var`在循环工作时，第一次循环时，`$var`的内容为`con1`，第二次循环时，`$var`的内容为`con2`，第二次循环时，`$var`的内容为`con3`···

```shell
#语法
for var in con1 con2 con3 ···
do
	#程序段
done
#示例，检测多台主机网络状态
#实现显示出192.168.1.1~192.168.1.100共一百台主机目前是否能与你的机器连通
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
network="192.168.1"		#先定义一个域名的前面部分
for sitnu in $(seq 1 100)	#seq为sequence,连续的意思
do
	#下面的程序在获取ping的返回值是正确的还是错误的
	ping -c 1 -w 1 ${network}.${sitenu} &> /dev/null && result=0 || result=1
	#开始显示结果是正确启动还是错误没有连通
	if [ "${result}" == 0 ]; then
		echo "Server ${network}.${sitenu} is up."
	else
		echo "Server ${network}.${sitenu} is DOWN."
	fi
done
```

`for do done`的数值处理，类似C语言的for循环

```shell
#语法
for ((初始值; 限制值; 赋值运算))
do
	程序端
done
#示例
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
read -p "Please input a number, I will count for 1 + 2 + 3+···+ your_input: " nu
s=0
for (( i=1; i<=${nu}; i=i+1))
do
	s=$((${s}+${i}))
done
echo "The result of '1 + 2 + 3+···+${nu}' is ==> ${s}"
```

### 4.4 shell脚本的调试

可以直接用bash的相关参数进行判断

`sh [-nvx] scripts.sh`
	`-n`：不执行脚本，仅查询语法搭问题
	`-v`：在执行脚本前，先将脚本文件的内容输出到屏幕上
	`-x`：将使用到的脚本内容显示到屏幕上，相当于断点调试

# 第四部分 Linux使用者管理

## 第一章 Linux账号管理与ACL权限设置

### 1.1 Linux的账户与用户组

ID与账号对应的信息存储在`/etc/passwd`

用户标识符：UID与GID
	用户ID： User ID，简称UID
	用户组ID： Group ID，简称GID

linux系统的用户登录主机工作处理过程

- 先查找`/etc/passwd`中是否存在账号，不存在则退出，存在则读出账号对应的UID与GID（`/etc/group`文件中）
- 读出该账号的家目录与shell设置
- 核对密码表，Linux会进入`/tec/shadow`中查找该账号与UID，核对输入的密码是否相符
- 进入shell管理阶段

**`/etc/passwd`文件结构**（查看命令：`head -n 4 /etc/passwd`）

- 每一行代表一个账号信息，第一行代表系统管理员root
  	每一行中各个信息用`:`隔开，从左到右分别对应：账号名称、密码(显示x)、UID、GID、用户信息说明栏、家目录、shell
- 系统账号包括：`bin daemon adm nobody`

**`/etc/shadow`文件结构**（查看命令：`head -n 4 /etc/shadow`）

shadow每行信息用`:`作为分割符，含九个字段
	账号名称：相同用户和`/etc/passwd`中信息相同
	密码：真实密码，经过编码（被加密）
	最近修改密码日期：记录修改密码那一天的实践
	密码不可被修改的天数：表示该账号在最近一次被修改后需要经过几天才可以再被修改
	密码需要重新修改的天数：指定最近一次修改密码后，在多少天数内需要再次修改密码（否则账号密码变为【过期特性】）
	密码需要修改该期限前的警告天数：当账号密码有效期限接近时，根据字段的设置发出警告
	密码过期后的账号宽限时间：过来该期限后用户依旧没有更新密码，则密码过期（需要重新设置密码采纳继续使用）
	账号失效日期：该账号在此规定的日期之后将无法使用
	保留：保留新功能的加入

**用户组：有效与初始用户组**

**`/etc/group`文件结构**
	该文件记录GID与组名的对应记录，文件每一行代表一个用户组，以`:`作为字段分隔符，分四栏
		组名：即组名
		用户组密码：一般不需要设置，密码已经移动到`/etc/gshadow`中，该字段显示`x`
		GID：用户组ID
		此用户组支持的账号名称：一个账号可以加入多个用户组，该字段存储多个用户在该组下的账号

有效用户组（effective group）与初始用户组（initial group）
	initial group：用户一登陆即拥有这个用户组的相关权限
	`groups`：有效与支持用户组的观察
	`newgrp`：有效用户组的切换，可修改该目前用户的有效用户组
	`/etc/gshadow`：文件结构同样以`:`作为分隔符，包含四个字段：组名、密码栏、用户组管理员的账号、有加入该用户组所支持的所属账号

### 1.2 账号管理

`useradd`：新增用户账号
	新增账号时系统自动处理事件：
		在`/etc/passwd`中建立一行于账号相关的数据，包括进阿里UID/GID/家目录等
		在`/etc/shadow`中将此账号的密码相关参数写入，但是尚未有密码
		在`/etc/group`中加入一个与账号名称一摸一样的组名
		在`/home`中建立一个与账号同名的目录作为用户家目录，且权限仅为700

`passwd`：给用户设置密码，默认情况下使用`useradd`建立账号后，该账号暂时被锁定无法登录，需要设置新密码
`chage`：处理账号的密码属性
`usermod`：微调`usermod`增加的用户参数
`userdel`：删除用户相关数据，包括用户账号/密码相关参数、用户组相关参数和用户个人文件数据

**用户功能**

`id`：查询某人或自己相关UID/GID等信息
`finger`：查看用户相关信息，将用户相关属性列出（大部分信息来源`/etc/passwd`）
`chfn`：修改用户属性信息，即修改`/etc/passwd`中内容，文件权限为SUID
`chsh`：change shell，修改`/etc/passwd`中部分内容，文件权限为SUID

**新增与删除用户**

`groupadd`：新建用户组
`groupmod`：类似`usermod`，在进行group相关参数修改
`groupdel`：删除用户组
`gpasswd`：用户组管理员功能，让某个用户组具备一个管理员，管理哪些账号可以加入/移出该用户组

### 1.3 主机的详细权限规划：ACL的使用

解决传统Linux权限无法针对某个特定账号设置专属权限的问题

ACL，Access Control List，访问控制列表，目的：提供传统的属主、所属群组、其他人的读、写、执行权限之外的详细权限设置
	ACL可针对单一用户、单一文件或目录来进行r、w、x的权限设置，对于需要特殊权限的使用状况非常有帮助

ACL的启动：一般已加入Linux文件系统的挂在参数当中，查看命令：`dmesg | grep -i acl`

**ACL的设置**

`getfacl`：获取某个文件/目录的ACL设置选项
`setfacl`：设置某个目录/文件的ACL规范

使用默认权限设置目录未来文件的ACL权限继承，即新建文件/目录时，该文件可以具备ACL设置：`d:[u/g]:[user/group]:权限`

### 1.4 用户身份切换

`su`：身份切换命令，可以进行然后身份切换；单纯使用`su`切换到root身份时，读取的身份设置方式为非登录shell的方式
	完成切换到新用户环境，需使用`su - username / su -| username`才会连通PATH、USER、MALL等变量转成新用户的环境
	如果仅要执行一次root命令，可以利用`su - -c "命令串"`的方式来处理
	使用root切换成为任何用户时，并不需要输入新用户密码

`sudo`：可让其他用户身份执行命令（仅有规范到**`/etc/sudoers`**内的用户才能执行`sudo`这个命令）
`visudo`：修改`/etc/sudoers`文件内容设置，可让sudo执行属于root的权限命令
		`/etc/sudoers`数据行组件意义：用户账号、登陆者的来源主机名、可切换的身份、可执行的命令

### 1.5 用户的特殊shell与PAM模块

无法登录：该用户无法使用bash或其他shell来登录系统，但不代表该账号无法使用其他的系统资源
	`passwd`文件结构中的系统账号，它的shell即使用`sbin/nologin`，无法登录的合法shell

**PAM模块**

Pluggable Authentication Modules，插入式验证模块，为一套应用程序编程接口，提供一连串的验证机制，只要用户将验证阶段的需求告知PAM后，PAM就能够返回用户验证的结果（成功/失败）
	PAM用来验证的数据称为模块

**PAM模块设置语法**

执行`passwd`后，程序调用PAM的流程为：
	-- 用户开始执行`/usr/bin/passwd`这个程序，并输入密码
	-- `passwd`调用PAM模块进行验证
	-- PAM模块会到`/etc/pam.d/`找寻与程序（`passwd`）同名的配置文件
	-- 根据`/etc/pam.d/passwd`内的设置，引用相关的PAM模块逐步进行验证分析
	-- 将验证结果（成功/失败以及其他信息）返回给`passwd`这个程序
	-- `passwd`这个程序会根据PAM返回的结果决定下一个操作（重新输入新密码或通过验证）

**`/etc/pam.d/passwd`配置文件**：第一行声明PAM版本，#开头的为注释，每一行都是一个独立的验证流程，每一行区分三个字段
	第一个字段：验证类别Type：分四种
			`auth`：检验用户身份，需要用密码来检验
			`accout`：用于进行授权，主要检验用户是否具有正确的权限
			`session`：会话期间，管理用户在这次登录期间，PAM所给予的环境设置（记录用户登录与注销时的信息）
			`password`：密码，提供验证的修订任务，即修改密码
	第二个字段：验证的控制标识，Control flag，分四种：required、requisite、sufficient、optional
	第三个字段：PAM模块与该模块的参数

**常用模块简介**：查看PAM默认文件内容：`cat /etc/pam.d/login`
	`pam_securetty.so`：限制系统管理员root只能够从安全的终端登录
	`pam_nologin.so`：该模块可限制一般用户是否能够登录主机，当`/etc/nologin`文件存在时，所有一般用户均无法再登录系统
	`pam_seLinux.so`：SELinux是个针对程序进行详细管理权限的功能
	`pam_console.so`：帮助处理一些文件的权限问题，让用户可以通过特殊终端接口（console）顺利登录系统
······
login的PAM验证机制流程：验证阶段(auth) ==->== 授权阶段(account) ==->== 密码阶段(password) ==->== 会话阶段(session) 

详细模块信息查找
	`/etc/pam.d/*`：每个程序的PAM配置文件
	`/lib64/security/*`：PAM模块文件的实际放置目录
	`/etc/security/*`：其他PAM环境的配置文件
	`/usr/share/doc/pam-*`：详细的PAM说明文件
其他相关文件：`limits.conf`、`/vaar/log/secure、/var/log/meeesges`

### 1.6 Linux主机上的用户信息传递

**查询用户**
	`last`：检查用户登录时间
	`w/who`：查看当前已登录在系统上的用户
	`lastlog`：知道每个账号最近登录的时间

**用户对谈**
	`write`：直接将信息传给接收者
	`mesg`：设置是否接受信息，root除外
	`wall`：对所有系统上的用户发送短信

**用户邮箱：**每个Linux主机上用户都具有一个mailbox，可使用`mail`命令来寄出/接收邮件（mailbox一般放置在`/var/spool/mail/*`中）

## 第二章 磁盘配额与高级文件系统管理

### 2.1 磁盘配额

磁盘配额（quota），即有多少限制额度，目的限制用户的硬盘容量，以妥善分配系统资源

磁盘配额的使用限制
	ext文件系统仅能针对整个文件系统，无法针对某个单一目录来设计它的磁盘配额
	内核必须支持磁盘配额
	值对一般身份用户有效
	若启用SELinux，费所有目录均可设置磁盘配额

磁盘配额的规范设置选项（对xfs文件系统的设置选项）
	分别针对用户、用户组或个别目录
	容量限制或文件数量限制
	限制inode使用量：管理用户可以建立的文件数量
	限制block使用量：管理用户磁盘容量的限制
	软限制和硬限制：soft/hard，soft，会有容量超出警告，给予宽限时间；hard：用户使用量绝不能超过限制值，会锁定	

磁盘配额命令：`xfs_quota -x -c "命令" [挂载点]`
	`-x`：专家模式，后续才能加入`-c`的命令参数
	`-c`：后面假的即命令
	`"命令"`：`printf df report state ···`

### 2.2 软件磁盘阵列

software RAID
磁盘阵列，Redundant Arrays of Inexpensive Disks，RAID，中文称独立冗余磁盘阵列，RAID可通过技术（软件/硬件）将多个较小的磁盘整合成为一个较大的磁盘设备，同时具备存储和数据保护功能等

RAID的level： RAID 0、RAID 1、RAID 1+0，RAID 0+1、RAID 5、RAID 6、Spare Disk
	磁盘阵列的优点：
		数据安全与可靠行：指当硬件顺坏是，数据是否还能否安全地恢复/使用
		读写性能：加强I/O读写性能
		容量：可让多块磁盘组合

硬件RAID：通过磁盘阵列卡来完成磁盘阵列功能，功能优秀，成本高
软件RAID：软件模拟磁盘阵列，消耗系统资源，成本低，使用的设备文件名是系统设备文件，名为`/dev/md0、/dev/md1等`

软件磁盘阵列设置：**`mdadm`**
格式化与挂载使用RAID：`mkfs.xfs`

软件RAID的配置文件：**`/etc/mdadm.conf`**

关闭软件RAID：先卸载(`umount`)其删除配置文件内设置 -> 覆盖掉RAID的metadata以及xfs的superblock -> 调用`mdadm`命令关闭

### 2.3 逻辑卷管理器

Logical Volume Manager，LVM，逻辑卷管理器，将几个物理的分区/磁盘通过软件组合成为一块看起来是独立的大磁盘(VG)，然后将这块磁盘再进行划分成为可使用的分区(LV)，即可挂载使用
	主要用处：实现一个可弹性调整容量的文件系统上，而不是建立一个性能为主的磁盘上

物理卷：Physical Volume，PV
卷组：Volume Group，VG是LVM组合起来的大磁盘（将许多的PV整合）
物理扩展块：Physical Extent，PE，类似文件系统中的block大小
逻辑卷：Logical Volume，LV，最终VG灰分切成LV，可被格式化使用的，类似分区，LV设备文件名：`/dev/vgname/lvname`

通过PV、VG、LV的规划后，再利用`mkfs`就可将LV格式化成为可利用的文件系统（LVM必须有内核支持，且需安装`lvm2`软件）

**LVM创建流程**
	--- Disk阶段，`gdisk`物理分区
	--- PV阶段，`pvcreate`
	--- VG阶段，`vg*`
	--- LV阶段，`lv*`
	--- 文件系统挂载阶段，`mkfs.xfs`
放大LV容量
	--- VG阶段需要有剩余的容量
	--- LV阶段产生更多的可用容量
	--- 文件系统阶段的放大

LVM动态自动调整磁盘使用率：LVM thin Volume，建立一个可以实用实取、用多少容量才分配实际写入多少容量的磁盘容量存储池(thin pool)，然后再由这个thin pool去产生一个指定要固定容量大小的LV设备

LVM的磁盘快照：快照即将当时系统信息记录下来，未来若有任何数据修改，则原始数据就会被搬移到快照区，没有被修改的区域则由快照区与文件系统共享（因此快照区与被快照的LV必须要在同一个VG上面）

LVM的关闭
	--- 卸载系统上的LVM文件系统，包括快照与所有LV
	--- 使用`lvremove`删除LV
	--- 使用`vgchange -a n VGname`让`VGname`这个VG不具有Active标志
	--- 使用`vgremove`删除VG
	--- 使用`pvremove`删除PC
	--- 使用`fdisk`修改ID

## 第三章 计划任务（crontab）

类似日常生活中的备忘录提醒功能
Linux计划任务通过`crontab 和 at`来完成

Linux系统常见的例行性任务：执行日志文件的轮询(`logrotate`)、日志文件分析`logwatch`任务、建立`locate`的数据库、`manpage`查询数据库的建立、RPM软件日志文件的建立、删除缓存、与网络服务有关的分析操作

### 3.1  单一计划任务

`at`命令

**`atd`的启动与`at`运行的方式**

`atd`服务，负责单一计划任务的服务，启动：`restart atd` -> `enable atd` -> `status atd`

`at`的运行方式：
	产生所要运行的任务，并将这个任务以文本文件的方式写入`/var/spool/at/`目录中，该任务便可等待`atd`服务的使用与执行
`at`的工作情况：
	--- 先找寻`/etc/at.allow`文件，在该文件中的用户才可使用`at`
	--- `/etc/at.allow`不存在，则查找`/etc/at.deny`文件，不存在此文件的用户则可使用`at`
	--- 以上两个文件都不存在，则只有root可使用`at`

执行`at`命令关键在于指定时间，尽可能使用绝对路径执行命令，避免出现错误

`at`任务管理：`atq`查询`at`计划任务、`atrm`可删除指定任务号码`at`计划任务
`batch`：系统有空时才执行后台任务，CPU的任务负载（单一时间点所负责的任务数量）小于0.8时，才执行工作任务

### 3.2 循环执行计划任务

循环执行的计划任务由cron系统服务来控制

相关配置文件：
	`/etc/cron.allow`：将可使用的`crontab`账号写入，记录到该文件的用户可以使用`crontab`
	`/etc/cron.deny`：将不可使用`crontab`账号写入，未记录到该文件的用户可以使用`crontab`

当用户使用`crontab`命令来建立计划任务后，该项任务会被记录到`/var/spool/cron`中，且以账号来作为判断依据

### 3.3 可唤醒停机期间的工作任务

`anacron`命令功能（实际为一个程序），到指定时间主动执行还未执行的计划任务
`anacron`会默认以一天、七天、一个月为期区检测系统未执行的`crontab`任务(在文件`timestamps`帮助下可获取系统关机时间，执行任务)

## 第四章 进程管理与SELinux

进程：Linux系统中，触发任何要给事件时，系统都会将它定义成为一个进程，并且给予这个进程一个ID，即PID，同时根据触发这个进程的用户与相关属性，给予这个PID一组有效的权限设置

**程序与进程**
	程序：通常为二进制程序，放置在存储媒介中，如硬盘、光盘、软盘、磁带等，以物理文件的形式存在
	进程：程序被触发后，执行者的权限与属性、程序的代码与所需数据都会被加载到内存中，操作系统给予这个内存中的单元一个标识符PID，可以说进程就是一个正在运行的程序

子进程与父进程：子进程可获取父进程的环境变量

fork and exec：程序调用的流程，Linux的程序调用通常称为fork-and-exec的流程。进程都会借由父进程以复制(fork)的方式产生一个一摸一样的子进程，然后复制出来的子进程再以exec的方式执行实际的进程，最终成为一个子进程

### 4.1 任务管理

当我们登陆系统获取bash shell之后，在单一终端下同时执行多个任务的操作管理
执行任务管理的操作中，每个任务都是目前bash的子进程，即彼此之间是有相关性的，无法用任务管理的方式由tty1的环境去管理tty2的bash

任务管理的限制
	--- 任务所触发的进程必须来自于你shell的子进程，只管理自己的bash
	--- 前台：可以控制与执行命令的这个环境称为前台的任务
	--- 后台：可以自动执行的任务，你无法使用[ctrl] + c终止它，可使用`bg、fg`调用该任务
	--- 后台中执行的进程不能等待terminal或shell的输入

**job control的管理**

`&`：直接将命令丢到后台中执行，输入命令后，在该命令最后加个`&`代表将该命令丢到后台中
`[ctrl]-z`：在vim的一般模式下，按下ctrl及z按键，屏幕上会出现[1]，表示这是第一个任务，而`+`代表最近一个被丢到后台的任务，且时目前后台默认会被使用的那个任务；默认情况下使用`[ctrl]-z`丢到后台当中的任务都是暂停状态
`jobs`：查看当前后台任务状态
`fg`：将后台任务拿到前台来处理
`bg`：让人物在后台状态下变成运行中
`kill`：管理后台当中的任务

**脱机管理问题**

任务管理的后台依旧与终端有关；`at`是将任务放置在系统后台而与终端无关

`nohub`：脱机或注销系统后，还可以让任务继续执行（一般`nohub`搭配`&`进行操作）

### 4.2 进程管理

**查看进程**

`ps`：将某个时间点的进程运行情况截取下来
`ps -l`：仅查看自己的bash相关进程
`ps aux`：查看系统所有进程
`top`：动态查看进程的变化（`ps`为静态），默认情况下，每5秒更新进程资源
`pstree`：查看进程之间的相关性
		所有进程都是依附在`systemd`这个进程下面，该进程的PID是一号，是由Linux内核主动调用的第一个进程

**进程管理**：通过进行的一个信号(signal)来操控管理进程
	`kill -l`：查看信号类型，更多信号可通过`man 7 signal`来查询
发送一个信号给某个进程：`kill -signal PID`或`killall -signal 命令名称`

**进程的执行顺序**：考虑优先级和CPU调度
	优先级：priority，PRI，PRI的值越低表示约优先，该值可由内核动态调整，用户无法直接调整PRI值
	nice值：调整进程优先级的间接方式，PRI(new) = PRI(old) + nice
		--- nice值可调整范围为-20~19；root可随意调整自己或他人进程的nice值，调整范围为0 ~ 19；
		--- 一般用户金科调整自己进程的nice值，范围为0 ~ 19；
给予某进程nice值的方法
	--- 一开始执行进程就立即给予一个特定的nice值，用 `nice`命令（新执行的命令及给予新的nice值）
	--- 调整某个已经存在的PID的nice值，用`renice`命令

**查看系统资源信息**
	`free`：查看内存使用情况
	`uname`：查看系统与内核相关信息
	`uptime`：查看系统启动时间与任务负载
	`netstat`：追踪网络或socket文件
	`dmesg`：分析内核产生的信息
	`vmstat`：检测系统资源变化

### 4.3 特殊文件与进程

三种特殊权限：`SUID、SGID、SBIT`

SUID：
	--- SUID权限进队二进制程序有效
	--- 只想着对于该程序需要具有x的可执行权限
	--- 本权限仅在执行该程序的过程中有效（run-time）
	--- 执行者将具有该程序拥有者的权限

`/proc/*`：内存中写入的数据存放目录

查询以使用文件或已执行进程使用的文件
	`fuser`：借由文件（或文件系统）找出正在使用该文件的进程
	`lsof`：列出被进程所使用的文件名称
	`pidof`：找出某个正在执行的进程的PID

### 4.4 SELinux

Security Enhanced Linux，安全强化Linux，设计目标：避免资源的误用
SELinux是在进行进程、文件等详细权限配置时依据的一个内核模块，包括网络服务

自主访问控制，Discretionary Access Control，DAC，基本上就是依据进程的拥有着与文件资源的rwx权限来决定有无读写的权限
强制访问控制，Mandatory Access Control，MAC，以策略规则制定特定进程读取特定文件

**SELinux运行模式**，SELinux通过MAC的方式来管理进程，控制主体为进程，而目标是该进程能否读取的文件资源
	主体：进程
	目标：主体进程能否读写目标资源，一般就是文件系统
	策略：依据某些服务来制订基本的读写安全性策略，`tarfeted`，`minimum`，`mls`	

**安全上下文**，Security Context
	可将安全上下文当作SELinux内必备的rwx
--- 安全上下文存在于主体进程与目标文件资源中，安全上下文放置到文件的inode中
--- 查看安全上下文可用`ls -Z`命令，内容包含三个字段：身份识别：角色：类型
		主体与慕白哦之间是否具有可读写的权限，与进程的domain及文件的type有关

SELinux 3种模式的启动关闭与查看
	目前SELinux依据启动与否共有三种模式：Enforcing()、Permissive(宽容模式)、Disabled(关闭模式)
	`getenforce`：查看目前SELinux模式；`sestatus`：查看SELinux的策略；`/etc/seLinux/config`：SELinux的配置文件
	

SELinux策略内的规则管理
	`getsebool`：SELinux各个规则的布尔值查询
	`seinfo、sesearch`：SELinux各个规则规范的主体程序能够读取的文件SELinux类型查询
	`setsebool`：修改SELinux规则的布尔值

SELinux安全上下文的修改该
	`chcon`：手动修改文件的SELinux类型
	`restorecon`：让文件恢复正确的SELinux类型
	`semanage`：默认目录的安全上下文查询与修改

# 第五部分 Linux系统管理员

## 第一章 认识系统服务

系统为了某些功能必须要提供一些服务，该服务称为service，完成service的程序被称为daemon
所有的服务启动脚本放置于`/etc/init.d/`目录中

### 1.1 daemon与服务service

init运行所有系统所需要的服务，管理机制如下

- 服务的启动、关闭于查看等方式：启动(`/etc/init.d/daemon start`)、重新启动(`/etc/init.d/daemon restart`)
  													  关闭(`/etc/init.d/daemon stop`)、查看状态(`/etc/init.d/daemon status`)
- 服务启动的分类：独立启动模式：stand alone：服务独立启动，该服务直接常驻于内存当中
  							超级守护进程：super daemon，由特殊的xinetd/inetd这两个总管程序提供socket对应或端口对应的管理
- 服务依赖性问题：init在管理员自己手动处理这些服务时，没有办法协助唤醒依赖服务
- 运行级别的分类：init可根据用户自定义的运行级别(runlevel)来唤醒不同服务，已进入不同的操作界面
- 制定运行级别默认要启动的服务：默认要启动：`chkconfig daemon on`；默认不启动：`chkconfig daemon off`													  查看默认为启动与否：`chkconfig --list daemon`
- 运行级别的切换操作：只要`[init 5]`即可主动切换命令行界面到runlevel 5，init会主动分析`/etc/rc.d/rc[35].d`这两个目录内的脚本
  然后启动转换运行级别中需要的服务，即完成整体的运行级别的切换	

**systemd使用的unit分类**
	并行处理所有服务，加速开机流程、一经要求就响应的`on-demand`启动方式、服务依赖性的自我检测、依daemon功能分类、将多个daemons集合为一个组群、向下兼容旧有的init服务脚本

### 1.2 systemctl管理服务

一般来说服务启动有两个阶段：一是开机时设置要不要启动该服务；二是当前要不要启动该服务

**`systemctl`管理单一服务的启动/开机启动与查看状态**
	`systemctl [command] [unit]`
	服务查看：`systemctl status [服务]`；服务关闭：`stop [服务]`
	强迫服务注销：`mask`，取消注销`unmask`

`systemctl`查看系统上所有服务：`systemctl list-unit-files`

`systemctl`管理不同的操作环境
	列出跟操作界面有关的target项目：`systemctl list-units --type=target --all`

`systemctl`分析各服务之间的依赖性：`systemctl list-dependencies [unit] [--reverse]`；`[--reverse]`：反向追踪谁使用了`unit`

`systemctl`系统默认的配置文件主要放在`/usr/lib/systemd/system`目录

## 第二章 日志文件

记录系统活动信息的几个文件

Linux常见的日志文件文件名（日志文件的权限通常设置为仅为root能够读取）
	`/var/log/boot.log`：记录开机启动是系统内核检测硬件的信息
	`/var/log/cron`：记录计划任务执行情况
	`/var/log/demsg`：记录系统开机时内核检测过程所产生的各项信息
	`/var/log/lastlog`：记录系统上所有账号最近一此登录系统时的信息
	`/var/log/messsages`：系统发送的所有错误信息都会记录在此
······

日志文件的一般格式
	事件发送的日期与时间、发送此事件的主机名、启动此事件的服务名称或命令与函数名称、该信息的实际内容

### 2.1 记录日志文件的服务

Linux的日志文件由`ssyslog.service`负责

`rsyslog.service`配置文件：`/etc/rsyslog.conf`，文件**规定了**[什么服务]、[等级信息]、[需要被记录在哪里（设备/文件）]
	--- 服务名称：可通过`man 3 syslog`查询或通过`syslog.h`文件夹查询
	--- 信息等级：同一服务所产生的信息有差别，Linux内核的`syslog`将信息分为八个等级，在`syslog.h`中定义
	--- 信息记录的文件名或设备或主机：信息放置位置的设置，一般放置`/var/log`

日志文件的安全性设置：设置日志文件只能被增加，而不能被删除`chattr +a [日志文件]`

日志文件服务器的设置：让某一台主机当成日志文件服务器，记录其余主机的信息

### 2.2 日志文件的轮循

`logrotate`主要针对日志文件进行轮循操作，主要功能时将现有的日志文件重新命名以做备份

`logrotate`的配置文件：
	`/etclogrotate.conf`主要参数设置文件
	`/etc/logrotate.d`目录，该目录中所有文件都会被主动读入`/etc/logrotate.conf`中使用

### 2.3 systemd-journald.service

协助记录日志文件，可记录开机启动过程中的所有信息，包括启动服务与服务启动失败情况；
可用来管理与擦寻启动后的登录信息

`journalctl`：查看`systemd-journald.service`数据中的登录信息
`logger`：让数据存储到日志文件当中

## 第三章 启动流程、模块管理与Loader

### 3.1 Linux启动流程

- 加载BIOS的硬件信息与进行自我检测（自检），并根据设置取得第一个可启动的设备
- 读取并执行第一个启动设备内MBR的启动引导程序（亦即时grub2、spfdisk等程序）
- 根据启动引导程序的设置加载kernel，kernel会检测硬件与加载驱动程序
- 在硬件驱动成功后，kernel会主动调用systemd程序，并以`default.target`流程启动

**BIOS**

系统加载BIOS，并通过BIOS程序去加载CMOS的信息，借由CMOS内地设置值取得主机的各项硬件配置（包括CPU与接口设备的沟通频率、启动设备的查找程序等），然后BIOS进行启动自我检测（Power-哦那Self Test，POST），然后开始执行硬件检测的初始化，并设置PnP设备，之后定义出可启动的设备顺序，接下来开始启动设备的数据读取

**boot loader的功能**（最终目的为**加载内核文件**）

- 提供选项：用户可选不同的启动选项，多重引导的功能
- 加载内核文件：直接指向可启动的程序区域来启动操作系统
- 转交其他loader：将管理功能转交给其他loader负责

具备选项功能，则可以选择不同的内核来启动，即可加载其他boot sector中的loader；
*windows的loader默认不具有控制权转交的功能，因此不能使用windows的loader来加载Linux的loader*

**加载内核检测硬件与`initramfs`的功能**

Linux内核会以自己的功能重新检测一次硬件，而不一定会使用BIOS检测到达硬件信息，也就是说此时**内核才开始接管BIOS后的工作**
	**内核文件放置位置`/boot/vmlinuz`** `ls --format=single-column -F /boot`
		内核模块放置的目录：`/lib/modules/`

虚拟文件系统（Initial RAM Disk或 Initial RAM Filesystem）一般使用的文件名为`/boot/initrd`或`/boot/initramfs`，该文件能够通过boot loader来加载到内存中，然后这个文件会被解压缩并且在内存中模拟成一个根目录，且此模拟在内存中的文件系统能够提供一个可执行的程序，通过该程序来**加载启动过程中所需要的内核模块，通常这些模块为USB、RAID、LVM、SCSI等文件系统与磁盘接口的驱动程序。加载完成后帮助内核重新调用`systemd`来开始后续的正常启动流程
	`man initrd`：查看详细的`initramfs`说明

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240327153609013.png" alt="image-20240327153609013" style="zoom: 67%;" />

实际上`initram`是一个小型的根目录，通过`systemd`来管理，同时该小型系统通过`initrd.target`来启动

**systemd与`sefault.target`进入启动程序分析**

systemd：主机硬件准备就绪后内核主动调用的第一个程序，因此systemd的PID号码为一号
	主要功能：主备软件执行环境，包括系统的主机名、网络设置、语言设置、文件系统格式及其他服务的启动等

**启动过程的主要配置文件**
	`/etc/modules-load.d/*.conf`：单纯要内核加载模块的位置
	`/etc/modprobe.d/*.conf`：可以加上模块参数的位置
	`/etc/sysconfig/*`：规范用户身份认证功能、Linux内核操作CPU的原则要求、防火墙、网卡设置等

### 3.2 内核与内核模块

内核的的工作：成功驱动主机的硬件设备

内核：`/boot/vmlinuz 或 /boot/vmlinuz-version`
内核模块：`/lib/modules/version/kernel 或 /lib/modules/$(uname -r)/kernel`
内核源代码：`/usr/src/linux 或 /usr/src/kernels`
内核版本：`/proc/version`
系统内核功能：`/proc/sys/kernel`

**内核模块与依赖性**
	内核模块扩展名以`.ko`结尾，利用`depmod`命令即可建立文件需要

**查看内核模块**
	`lsmod`：显示内容包括模块名称、模块大小、此模块是否被其他模块所使用

**内核模块的加载与删除**
	`modprobe`：主动查找`modules.dep`内容，先确定模块依赖性后，决定需要加载的模块
	`insmod`：完全由用户自行加载一个**完成的文件名**模块，不会主动分析模块依赖性，`rmmod`

### 3.3 Boot Loader： Grub2

boot loaser的程序代码执行与设置值加载分为两个阶段

- 执行boot loader主程序，该主程序必须安装在启动区，及MBR或启动扇区(boot sector)
- 主程序加载配置文件：加载所有配置文件与相关的环境参数文件，包括文件系统定义与主要配置位机`grub.cfg`

`/boot/grub2/grub.cfg`：grub2配置文件

`/etc/default/grub`：主要环境配置文件，设置参数有倒数时间参数、是否隐藏选项、内核的外加参数功能、默认启动选项等

**`initramfs`**

提供启动过程中所需要的内核模块
内核模块放置于`/lib/modules/$(uname -r)/kernel`中，这些模块必须要根目录被挂载时才能够被读取
`initramfs`可将`/lib/modules/`内的模块包成一个文件，该文件名即为`initramfs`，然后在启动时通过主机的INT 13硬件中断功能将该文件读出来解压缩，并且`initramfs`在内存内会模拟成为根目录

### 3.4 常见问题解决

忘记root密码：按下`systemctl reboot`来重新启动 -> 进入启动画面，在可启动的选项上按下e进入编辑模式 -> 然后再Linux 16的内核项目种使用`rd.break`参数来处理 -> 修改完之后按下`[ctrl + x]`开始启动

因文件系统错误而无法启动：常见错误为`/etc/fstab`编辑错误，可利用`fsck.ext3`检测`/dev/md0`错误
	如果为xfs系统，则可使用`xfs_repair`命令处理

## 第四章 基础系统设置与备份

### 4.1 系统级别设置

网络设置，找到网络管理原或ISP（Internet Service Provider），获取网络参数
	手动设置固定IP： IP、子网掩码、网关、DNS主机的IP四个信息；
	网络参数自动获取：DHCP协议自动获取
	光纤到户与ADSL宽带拨号

手动设置网络参数：`nmcli`	
修改主机名：`hostnamectl`

**时间与日期设置**
	时区设置：`timedatectl`
	手动网络校时：`ntpdate`

语系设置：`localectl`查看

### 4.2 服务器硬件数据的收集

`dmidecode`：查看硬件设备，包括cpu型号、主板型号、与内存相关的型号
`lspci`：显示目前硬件设备，主板、控制芯片、显卡、网卡，还可以了解某设备的详细信息
`lsusb`：查看系统接了多少个USB设备
`iostat`：查看磁盘已读写的数据量；`smartd`：监测目前常见的ATA与SCSI接口的磁盘健康检查，被监测的磁盘需支持SMART协议

### 4.3 备份

重要备份文件目录：
	操作系统本身：`/etc /home/ /var/spool/mail/ /var/spoll/{at|cron}/ /boot/ /root/`
	网络数据库：`/usr/local`

累积备份：系统在进行完一次完整备份后，经过一段时间的运行，比较系统与备份文件的差异，仅备份有差异的文件
	`dd cpio xfsdump/xfsrestore`

差异备份：在系统进行完一次完整备份的前提下，每次的备份都是与原始的完整备份比较的结果

`rsync`：上传备份文件

## 第五章 软件安装

可执行文件：文件权限上表示有`x`权限，Linux系统上真正识别的可执行文件为**二进制程序**
	`file`：该命令可查看文件，二进制文件会显示执行文件类别`ELF 64-bit LSB executable`，一般的文本文件显示`text exectables`

源代码：程序代码文件本质上是一般的纯文本文件 -> 经过编译器编译、链接后 -> 可执行的二进制文件

**函数库**，分为静态函数库和动态函数库

Linux内核提供了很多与内核相关的函数库与外部参数，相关信息放置`/usr/include /usr/lib /usr/lib64` 
函数库类似子程序，可被调用来执行一段功能函数

`make`：一个程序，进行编译过程的简化；`configure 或 config`：检测程序，检测操作环境
	检测内容：检测是否有适合的编译器可编译
					  是否存在本软件所需的函数库，或其他需要依赖的软件
					  操作系统平台是否适合本软件，包括Linux内核版本
					  内核的头文件是否存在，驱动程序的检测

configure与make进行编译的示意图：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240329115318335.png" alt="image-20240329115318335" style="zoom:67%;" />

**Tarball软件**：将软件的所有源代码文件先以tar打包，然后再以压缩技术来压缩，通常最常见的以`gzip`来压缩

**makefile的基本语法与变量**

`目标(target):目标文件1 目标文件2`
`<tab> gcc -o 欲建立的执行文件 目标文件1 目标文件2`

makefile变量基本语法
	--- 变量与变量内容以[=]隔开，同时两边可具有空格
	--- 变量左边不可以有<tab>，变量与变量内容在[=]两边不能具有[:]
	--- 运用变量时，以`${变量}`或`$(变量)`使用

### 5.1 函数库管理

软件之间会互相使用彼此提供的函数库来使用其特殊功能

静态函数库：扩展名：`libxxx.a`、编译时直接整合到执行程序中（编译文件偏大）、编译成功的可执行文件可独立运行、函数库升级时，程序需要重新编译

动态函数库：扩展名：`libxxx.so`、编译时程序动态读取函数库（编译文件较小）、编译出来的程序不能独立执行，函数库属性不能随意更改、函数库升级后，程序不需要重新编译

`ldd`：该命令可判断某个可执行的二进制文件中含有的动态函数库

### 5.2 软件安装管理器

厂商先在系统上编译好了用户所需的软件，然后该编译好的可执行的软件直接发布给用户安装

**RPM**

RPM，RedHat Package Manager，红帽开发，以一种数据库记录的方式将所需要的软件安装到Linux系统的一套软件管理机制

将安装的软件先编译，并且打包成为PRM机制的文件，通过打包好的软件内默认的数据库，记录该软件要安装时必须具备的依赖属性软件
	--- 软件安装的环境必须与打包时的环境需求一致或相当
	--- 需要满足软件的依赖属性需求
	--- 反安装时需注意，最底层的软件不可先删除
**SRPM**： Source RPM，提供的软件内容并没有经过编译，即为源代码，可在不同平台上修改参数配置文件进行编译适配

---

YUM在线升级：解决RPM属性依赖文集，YUM分析软件的依赖属性问题，将软件内的记录信息记录下来，然后主动向网络上的YUM服务器的软件源地址将依赖属性的软件同时安装

**RPM软件管理程序`rpm`命令**	
		RPM默认安装路径：`/var/lib/rpm/`，查询操作也对该目录下的数据库文件查询

## 第六章 X Window设置

Linux上的图形用户界面模式称为X Window System，简称为X或X11
	X11是一个软件并非操作系统
	X11是利用网络架构来进行图形用户接口的执行与绘制

**主要组件**：X Server/X Client/Window Manager/Display Manager
	--- X Server：硬件管理、屏幕绘制与提供字体功能，管理设备包括键盘、鼠标、手写板、显示器、显卡等
	--- X Client：负责X Server要求的事件处理，处理来自X Server的操作，产生绘图数据
	--- X Window System：特殊的X Client ，负责管理所有的X Client
	--- Display Manager：提供登录需求

X Window System的启动流程
	--- 在命令行模式启动X：通过`startx`命令，主要找出用户或是系统默认的X Server与X Client的配置文件
	--- 有`startx`调用执行的`xinit`：当`startx`找到所需设置后，调用`xinit`实际启动X
	--- 启动X Server文件：`xserverrc`
	--- 启动X Client文件：`xinitrc`

### 6.1 X Server配置文件

默认放置位置：`/etc/X11`，相关的显示模块/总管模块主要放置在`/usr/lib64/xorg/modules`
	提供的显示字体：`/usr/share/X11/fonts/`
	显卡驱动模块：`/usr/lib64/xorg/modules/drivers/`

`X -version`：root身份下执行该命令可查看X Server版本

**字体管理**：主要放置目录`/usr/share/X11/fonts/ /usr/share/fonts/`

显示器参数微调：`gtf`功能命令来调整

显卡驱动程序安装：不同的显卡厂商安装配置不同，需安装相应官网的驱动程序

## 第七章 Linux内核编译与管理

内核：Kernel，实际为系统上的一个文件，该文件包含了驱动**主机各项硬件的检测程序与驱动模块**

内核模块：将一些不常用的类似驱动程序的东西独立出内核，编译称为模块，内核可在系统正常运行过程中加载模块
	模块放置目录：`/lib/modules/$(uname -r)/kernel/`中

内核源代码的获取：内核官网，镜像网站

**内核编译与内核功能选择**

查看主机硬件环境：通过`/proc/cpuinfo及lspci`查看

保持干净的源代码：第一次编译前执行`make mrproper`命令处理编译过程的目标文件及配置文件，该操作会删除以前进行过的内核功能选择文件

选择内核功能：`make menuconfig`、`make oldconfig`、`make xconfig`、`make gconfig`、`make config`
	内核功能列表文件：`/boot/config-xxx`，以上`make`为建立该文件的方法

内核功能详细选项选择：
	`General setup`：针对内核与程序直接的相关性设计
	`loadable module + block layer`：让内核能够支持动态的内核模块
	CPU的类型与功能选择：选择主机实际的CPU类型
	电源管理功能：系统电源管理机制
	总线Bus选项：与总线支持有关
	编译会执行文件的格式：Linux内核执行文件所用到的模块
	内核的网络功能：包含防火墙相关选项，重要
	各设备的驱动程序：与主机硬件紧密联系
	文件系统的支持、内核开发、信息安全、密码引用、虚拟化与函数库······

**编译内核**
	`make -j 4 clean`：先清除缓存文件
	`make -j 4 bzImage`：先编译内核
	`make -j 4 modules`：再编译模块
	`make -j 4 clean bzImage modules`：连续操作，`-j 4`表示对系统上4个CPU内核同时编译，不同主机cpu修改相应数值

安装新内核与多重内核选项

- 移动内核到`/boot`且保留旧内核文件，保留旧内核文件可以新内核让主机无法成功启动时启动主机
- 建立相对应的`Initial Ram Disk(initrd)`，建立`initramfs`
- 编辑启动选项（grub）：`grub2-mkconfig -o`命令处理grub2的启动选项设置
- 重新以新内核启动、测试、修改

单一模块的编译：获取源代码 -> 重新编译称为系统可加载的模块，需要用到`make 、gcc`以及内核所提供的`include`头文件与函数库等









---



# 随笔

[网络基础](http://www.study-area.org/network/network_what_is.htm)

4 k = 4096 byte

启动引导程序：boot loader 安装在启动设备的第一个扇区，即MBR中，BIOS通过硬件的INT 13中断功能来读取的MBR，即BIOS可检测你的磁盘