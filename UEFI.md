==UEFI==
吴主华

UEFI编程与原理

UEFI架构图概述

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240324011144715.png" alt="image-20240324011144715" style="zoom:50%;" />

# 第一章 概述

## 1.1 BIOS、UEFI

BIOS，全称基本输入/输出系统，存储在主板ROM中的一组程序代码，包括

- 加电自检程序，用于开机时对硬件的检测
- 系统初始化代码，包括硬件设备的初始化、创建BIOS中断向量等
- 基本的外围I/O处理的子程序代码
- CMOS设置程序

UEFI，Unified Extensible Firmware Interface，统一可拓展固件接口
**定义了操作系统和平台固件之间的接口，一种标准，没有提供具体实现**

UEFI实现可分为两部分
	-- 平台初始化：遵循Platform Initialization标准，由UEFI Forum发布
	-- 固件-操作系统接口

基于EFI的计算机系统的组成示意图：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240506202837214.png" alt="image-20240506202837214" style="zoom:67%;" />





# 随笔

UEFI纯粹地是一个接口规范，它不会具体涉及平台固件是如何实现的。“如何实现”这一内容是PI（Platform Initialization）要解决的问题

UEFI是关于如何进行引导过程的。引导就是一个将控制权限连续，逐级地移交，从而启动整个OS的过程，这就是OS Loader所肩负的职责

UEFI建立在被称为平台初始化（Platform Initialization，简称PI）标准的框架之上。PI标准为前期硬件初始化提供了标准流程和架构

《UEFI原理与编程》--戴正华 P131，第一行单词错误“Patition”，漏拼“r”

缩略词

| 缩写 |                 全称                  |        含义        |
| :--: | :-----------------------------------: | :----------------: |
| BIOS |       Basic Input/Output System       | 基本输入/输出系统  |
| POST |          Power On Self Test           |      加电自检      |
| UEFI | Unified Extensible Firmware Interface | 统一可拓展固件接口 |
|  PI  |        Platform Initialization        |     平台初始化     |
|      |                                       |                    |
|      |                                       |                    |
|      |                                       |                    |
|      |                                       |                    |

