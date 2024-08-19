==Android 初识==
吴主华

Andriod整体层次架构示意

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240505140038968.png" alt="image-20240505140038968" style="zoom:50%;" />

# Android常用adb命令

adb，Android Debug Bridge，Android调试桥，C/S架构的命令行工具

主要由三部分组成

- 运行在 PC 端的 Client : 可以通过它对 Android 应用进行安装、卸载及调试

Eclipse 中的 ADT、SDK Tools 目录下的 DDMS、Monitor 等工具，都是同样地用到了 adb 的功能来与 Android 设备进行交互
PC 端的手机助手，如 360 手机助手、豌豆荚、应用宝等，其除了安装第三方应用方便，其他的功能，基本上都可以通过 adb 命令去完成

- 运行在 PC 端的 Service : 其管理客户端到 Android 设备上 adb 后台进程的连接

adb服务启动后，Windows可在任务管理器中找到adb.exe进程

- 运行在 Android 设备上的 adb 后台进程

执行`adb shell ps | grep adbd`，可找到后台进程，windows使用`findstr`替代`grep`

**`adb` 使用的端口号，`5037`**

---

## adb 命令

可以通过 adb 来管理多台设备，其一般的格式为：`adb [-e | -d | -s <设备序列号>] <子命令>`

在配好环境变量的前提下，在命令窗口当中输入 adb help 或者直接输入 adb ，将会列出所有的选项说明及子命令

常用的命令：

- `adb shell dumpsys activity top`：查看当前应员工的activity信息

- `adb shell dumpsys package`：查看指定包名应用的详细信息

- `adb shell dumpsys meminfo`：查看指定进程名或者进程id的内存信息

- `adb shell dumpsys dbinfo`：查看指定包名应用的数据库存储信息

- `adb forward`：设备端口转发，`adb forward [协议：端口号] [协议：端口号]`

- `adb jdwp`：查看设备中可被调试的应用进程号

- `adb devices`, 获取设备列表及设备状态

- `adb get-state` , 获取设备的状态

  设备的状态有 3 钟，`device` , `offline` , `unknown`

  `device`：设备正常连接；`offline`：连接出现异常，设备无响应；`unknown`：没有连接设备

- `adb kill-server , adb start-server` , 结束 adb 服务， 启动 adb 服务，通常两个命令一起用

  一般在连接出现异常，使用 adb devices 未正常列出设备， 设备状态异常时使用 kill-server，然后运行 start-server 进行重启服务

- `adb logcat` , 打印 Android 的系统日志

- `adb bugreport` , 打印dumpsys、dumpstate、logcat的输出，用于分析错误

- `adb install` , 安装应用，覆盖安装是使用 -r 选项

- `adb uninstall` , 卸载应用，后面跟的参数是应用的包名，不是 apk 文件名；-k 选项，卸载时保存数据和缓存目录

- `adb pull` , 将 Android 设备上的文件或者文件夹复制到本地

  注意权限，复制系统权限的目录下的文件，需要 root ，并且一般的 Android 机 root 之后并不能使用命令去复制，而需要在手机上使用类似于 RE 的文件浏览器，先对系统的文件系统进行挂载为可读写后，才能在手机上复制移动系统文件

- `adb push `, 推送本地文件至 Android 设备

- `adb root , adb remount`, 只针对类似小米开发版的手机有用，可以直接已这两个命令获取 root 权限，并挂载系统文件系统为可读写状态

- `adb reboot `, 重启 Android 设备

  `bootloader` , 重启设备，进入 fastboot 模式，同 adb reboot-bootloader 命令

  `recovery` , 重启设备，进入 recovery 模式

- `adb forward` , 将 宿主机上的某个端口重定向到设备的某个端口

- `adb connect `远程连接 Android 设备

## adb shell 命令

adb 命令是 adb 这个程序自带的一些命令
adb shell 则是调用的 Android 系统中的命令，这些 Android 特有的命令都放在了 Android 设备的 system/bin 目录下

这些文件里面有些命令实际上是一个 shell 脚本

打开 monkey 文件：

```plaintext
# Script to start "monkey" on the device, which has a very rudimentary
# shell.
#
base=/system
export CLASSPATH=$base/framework/monkey.jar
trap "" HUP
exec app_process $base/bin com.android.commands.monkey.Monkey $*
```

再比如打开 am：

```shell
#!/system/bin/sh
#
# Script to start "am" on the device, which has a very rudimentary
# shell.
#
base=/system
export CLASSPATH=$base/framework/am.jar
exec app_process $base/bin com.android.commands.am.Am "$@"
```

常用的 adb shell 命令

### pm

Package Manager , 可以用获取到一些安装在 Android 设备上得应用信息

直接运行 adb shell pm 可以获取到该命令的帮助信息

- pm list package 列出安装在设备上的应用

  `adb shell pm list package`：不带任何选项，列出所有的应用的包

  `adb shell pm list package -s `：**-s**：列出系统应用

  `adb shell pm list package -3`：**-3**：列出第三方应用

  `adb shell pm list package -f`：**-f**：列出应用包名及对应的apk名及存放位置

  `adb shell pm list package -i`：**-i**：列出应用包名及其安装来源

  参数组合使用，例如，查找三方应用中知乎的包名、apk存放位置、安装来源：

  `adb shell pm list package -f -3 -i zhihu`

- `pm path` 列出对应包名的 .apk 位置：`adb shell pm path com.tencent.mobileqq`

- `pm list instrumentation` , 列出含有单元测试 case 的应用，后面可跟参数 -f （与 pm list package 中一样），以及 [TARGET-PACKAGE]

- `pm dump` , 后跟包名，列出指定应用的 dump 信息，里面有各种信息，自行查看

  `adb shell pm dump com.tencent.mobileqq`

- `pm install `, 安装应用
  目标 apk 存放于 PC 端，请用 adb install 安装

  目标 apk 存放于 Android 设备上，请用 pm install 安装

- `pm uninstall `, 卸载应用，同 adb uninstall , 后面跟的参数都是应用的包名

- `pm clear `, 清除应用数据

- `pm set-install-location , pm get-install-location` , 设置应用安装位置，获取应用安装位置

  [0/auto]：默认为自动；[1/internal]：默认为安装在手机内部；[2/external]：默认安装在外部存储

### am

- am start , 启动一个 Activity，已启动系统相机应用为例

  `adb shell am start -n com.android.camera/.Camera`：启动相机

  `adb shell am start -S com.android.camera/.Camera`：先停止目标应用，再启动

  `adb shell am start -W com.android.camera/.Camera`：等待应用完成启动

  `adb shell am start -a android.intent.action.VIEW -d http://testerhome.com`：启动默认浏览器打开一个网页

  `adb shell am start -a android.intent.action.CALL -d tel:10086   `：启动拨号器拨打 10086

- `am instrument` , 启动一个 instrumentation , 单元测试或者 Robotium 会用到

- `am monitor` , 监控 crash 与 ANR

- `am force-stop `, 后跟包名，结束应用

- `am startservice` , 启动一个服务

- `am broadcast` , 发送一个广播

### input

盖命令可以向 Android 设备发送按键事件

- input text , 发送文本内容，不能发送中文

  `adb shell input text test123456`：前提先将键盘设置为英文键盘

- `input keyevent` , 发送按键事件

  `adb shell input keyevent KEYCODE_HOME`：模拟按下 Home 键 ，源码里面有定义：public static final int KEYCODE_HOME = 3，因此可以将命令中的 `KEYCODE_HOME` 替换为 `3`

- `input tap` , 对屏幕发送一个触摸事件

  `adb shell input tap 500 500`：点击屏幕上坐标为 500 500 的位置

- `input swipe `, 滑动事件

  `adb shell input swipe 900 500 100 500`：从右往左滑动屏幕

*MonkeyRunner 能做到的事情，通过 adb 命令都可以做得到，如果进行封装，会比 MR 做得更好*

### screencap

截图命令

`adb shell screencap -p /sdcard/screen.png`：截屏，保存至 sdcard 目录

### screenrecord

新增的录制命令
`adb shell screenrecord sdcard/record.mp4`：执行命令后操作手机，ctrl + c 结束录制，录制结果保存至 sdcard

### uiautomator

执行 UI automation tests ， 获取当前界面的控件信息
runtest：executes UI automation tests
dump：获取控件信息
`adb shell uiautomator dump `：不加 [file] 选项时，默认存放在 sdcard 下

### ime

输入法

`adb shell ime list -s`：列出设备上的输入法

`adb shell ime set com.baidu.input_mi/.ImeService`：选择输入法

### wm

`adb shell wm size `：获取设备分辨率

### monkey

在 PC 端执行 monkey 命令，将信息保存至 D 盘 monkey.log：
`adb shell monkey -p com.android.settings 5000 > d:\monkey.log`

在 PC 端执行 monkey 命令，将信息保存至手机的 Sdcard：（注意须加上引号）
`adb shell "monkey -p com.android.settings 5000 > sdcard/monkey.log"`

### settings

`adb shell settings` ：命令允许您查看和修改设备的系统设置
		`adb shell settings list system`：查看所有系统设置
		`adb shell settings list secure`：查看所有安全设置
		`adb shell settings list global`：查看所有全局设置

### dumpsys

- `adb shell dumpsys activity top`：查看当前应员工的activity信息

- `adb shell dumpsys package`：查看指定包名应用的详细信息

- `adb shell dumpsys meminfo`：查看指定进程名或者进程id的内存信息

- `adb shell dumpsys dbinfo`：查看指定包名应用的数据库存储信息

### log

在 logcat 里面打印你设定的信息

`adb shell log -p d -t xuxu "test adb shell log"`：-p：优先级，-t：tag，标签，后面加上 message

### getprop

查看 Android 设备的参数信息，只运行 `adb shell getprop`，结果以 `key : value` 键值对的形式显示，如要获取某个 key 的值：

`adb shell getprop ro.build.version.sdk`：获取设备的 sdk 版本

## 部分linux相关命令

操作你的 Android 设备，常用到的命令

cat、cd、chmod、cp、date、df、du、grep、kill、ln、ls、lsof、netstat、ping、ps、rm、rmdir、top、touch、重定向符号 ">" ">>"、管道 "|"

### 进程命令

- `cat /proc/[pid]/maps`：查看当前进程的内存加载情况，比如加载了那些so文件，dex文件
- `cat /proc/[pid]/status`：查看当前进程的状态信息
- `cat /proc/[pid]/net/tcp/tcp7/udp/udp6`：获得当前应用使用到的端口号信息

# Java及其系统架构

一个 Java 程序可以认为是一系列对象的集合，而这些对象通过调用彼此的方法来协同工作

- **对象**：对象是类的一个实例，有状态和行为。如，一条狗是一个对象，它的状态有：颜色、名字、品种；行为有：摇尾巴、叫、吃等
- **类**：类是一个模板，它描述一类对象的行为和状态
- **方法**：方法就是行为，一个类可以有很多方法。逻辑运算、数据修改以及所有动作都是在方法中完成的
- **实例变量**：每个对象都有独特的实例变量，对象的状态由这些实例变量的值决定

java的基本语法可参考菜鸟教程：[Java 基础语法 | 菜鸟教程 (runoob.com)](https://www.runoob.com/java/java-basic-syntax.html)



**java系统架构**

Java体系结构的组成主要有四部分

- Java编程语言
- 字节码
- Java API
- Java虚拟机

字节码：任何编程语言的编译结果满足并包含Java虚拟机的内部指令集、符号表以及一些其他的辅助信息，这个编译结果就是一个有效的字节码文件

Java API：是一些预先定义的接口，目的是提供应用程序与开发人员基于某软件或硬件的以访问一组例程的能力，而又不需要访问源码或者理解内部工作机制的细节

Java虚拟机：其主要任务为将字节码装载到内部，解释/编译为对应平台上的机器指令执行



Java程序执行流程：

编写Java程序的源代码，Java前端编译器负责将源代码编译为字节码，Java虚拟机负责将编译好的字节码装载进内部，解释/编译为对应平台上的机器指令运行

流程示意图：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240506095914509.png" alt="image-20240506095914509" style="zoom: 50%;" />

**番外**

Kotlin for Android

*在Google I/O 2017中，Google 宣布 Kotlin 成为 Android 官方开发语言*

Kotlin是一个基于JVM的新的编程语言，可以编译成Java字节码，也可以编译成JavaScript，方便在没有JVM的设备上运行
Kotlin是面向对象和功能编程功能的JVM和Android的通用、开源、静态的实用的编程语言

目前超过50%的专业安卓开发人员使用Kotlin作为主要语言，而只有30%的人使用Java作为主要语言。70%以Kotlin为主要语言的开发人员表示，Kotlin使他们的工作效率更高（数据来源googl官网）

Kotlin优点

- 简洁: 大大减少样板代码的数量
- 安全: 避免空指针异常等整个类的错误
- 互操作性: 充分利用 JVM、Android 和浏览器的现有库
- 工具友好: 可用任何 Java IDE 或者使用命令行构建



# Android Application

## app components与callback

### app components介绍

组件

- [assembly]：供装配整台机器、构件或元件的零件组合
- [module；package]：在电子或机械设备中组装在一起形成一个功能单元的一组元件
- [unit]：组装产品时所组合的通常或多或少重复的部分
- [section]：可备组装或被重新组装的几个部件之一

Android四大应用程序组件（app components）

Activities：表示一个带有用户接口的显示界面，处理UI上的交互

Services：运行在后台，执行耗时的操作或者远程调用

Content providers：管理应员工程序的共享数据

Broadcast receivers：接收系统中的广播事件



# 随笔

Android Application的学习模型

- 先整体把握
- 再具体学习
- 持续迭代不断完善

如何学习AndroidApp开发的具体实现----> 学会寻找答案

- 通过官网等先整体把握
- 通过官网或相关教程再具体学习
- 通过实际项目或其他交叉持续迭代

[Android 开发者  | Android Developers (google.cn)](https://developer.android.google.cn/?hl=zh-cn)

[Android 常用 adb 命令总结 - 澄和 - 博客园 (cnblogs.com)](https://www.cnblogs.com/bravesnail/articles/5850335.html#:~:text=adb 命令 1 adb devices %2C 获取设备列表及设备状态 [xuxu%3A~]%24,adb pull %2C 将 Android 设备上的文件或者文件夹复制到本地 ... 更多项目)



