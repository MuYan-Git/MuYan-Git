==X86-ARM==

X86和ARM是两种不同的处理器架构，主要区别包括以下几点

设计目标

- X86架构：主要用于个人计算机和服务器的通用的计算设备（日常使用的服务器和电脑基本上基于Intel或AMD，都属于X86架构）
- arm架构：更多用于低功耗嵌入式系统或者移动设备，注重能效低成本和高度集成（比如手机）

处理器复杂性

- X86架构：X86处理器设计更复杂，更高指令集，支持更广泛的操作和功能可以更好的去适应或计算密集型的任务，比如高性能计算、虚拟化或者是图形处理
- arm架构：采用精简指令集的设计，致力于减少指令集的数量和功耗，并在低功耗和嵌入式应用当中具有更多优势

生态系统和软件支持

- X86架构：成熟广泛的生态系统和软件支持，包括多个操作系统（Windows/Linux），以及应用软件和开发工具、技术社区
- arm架构：在持续增长，相对来说没有X86成熟和完善

X86架构是微处理器执行的计算机语言指令集，广义的X86架构泛指支持X86和X64架构的Intel、AMD的CPU

ARM架构是32位精简指令及（RISC）处理器架构，所有32位嵌入式处理器ARM占比75%

> 苹果M1、M2芯片基于arm架构

X86处理器发展简史

第一代：4位和低档8位处理器



# ARM

# 概述

## 主流芯片厂商

TI（德州仪器）

Samsung，三星

Freescale，飞思卡尔

Marvell，马维尔

Qualicomm，高通，骁龙系列

Nvidia，英伟达

## 非主流

Cortex-M系列，低功耗，低成本的微控制器

Cortex-R系列，实时方向

SecurCore，安全方向

# 一、ARM架构和处理器

ARM系列处理器

A系列：设计用于高性能的“开放应用平台”—接近电脑处理器 
R系列：用于高端的嵌入式系统，尤其是那些带有实时要求的—快、实时
M系列：用于深度嵌入的，单片机风格的系统中

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240320095520831.png" alt="image-20240320095520831" style="zoom:50%;" />

不同架构的特点技术差异

## 1.1 ARM Cortex-A5 processor

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240501153019508.png" alt="image-20240501153019508" style="zoom:67%;" />

- ARM CoreSight Multicore Debug and Trace： ARM CoreSight 多核调试与跟踪，提供先进的调试和追踪解决方案，可为这些平台上的开发人员提供帮助
- Generic Interrupt Controller：通用中断控制器，负责处理来自外部设备的中断请求，保证系统能够及时响应外部事件
- Cortex-A5 processor： Cortex-A5 处理器
- NEON Data Engine： NEON数据引擎，Arm Neon 是一种先进的单指令流多数据流(SIMD)架构扩展，适用于 ARM Cortex-A 和 Arm cortex-R 系列处理器，其功能极大地改善了移动设备上的用例，例如多媒体编码/解码、用户界面、二维/三维图形和游戏
- Floating-point unit： FPU，执行浮点数计算的逻辑部件
- Instruction Cache： i-cache（指令缓存）是计算机系统中的一个关键组件，也是处理器核心内部的一部分。 它专门用来存储最近使用过的指令，以满足CPU执行指令的需求。 当CPU需要执行一个指令时，首先会检查i-cache中是否已经缓存了该指令，如果命中（即缓存中已存在该指令），那么CPU可以直接从i-cache中读取指令，而无需等待主存的访问
- Data Cache：保存一段连续地址的数据
- SCU： Snoop control unit，当支持多个core时保持实现data cache的一致性
- ACP： Accelerator Coherency Port，加速器一致性接口，一个可选的从设备接口，运行外部AXI主设备（如DMA控制器、GPU等）以一种缓存一致性的方式访问内存系统
- Dual 64-bit AMBA3 AXI： 双64位AMBA3 AXI，一种面向高性能、高带宽、低延迟的片内总线。它的地址/控制和数据相位是分离的，支持不对齐的数据传输，同时在突发传输中，只需要首地址，同时分离的读写数据通道、并支持显著传输访问和乱序访问，并更加容易就行时序收敛

**ARM Cortex-A系列处理器体系结构要点**

- 32位RISC核心，具有16×32位可见寄存器，具有基于模式的寄存器库
- 改进的哈佛体系结构（对指令和数据的单独、并行访问）
- 加载/存储体系结构
- 标准Thumb-2技术
- VFP和NEON选项
- 与以前ARM处理器的代码向后兼容
- 4 GB的虚拟地址空间和至少4 GB的物理地址空间
- 用于虚拟到物理地址转换的硬件转换表遍历
- 虚拟页面大小为4 KB、64 KB、1 MB和16 MB。可缓存性属性和访问权限可以按页面设置
- 大端和小端数据访问支持
- 对基本加载/存储指令的无对齐访问支持
- MPCore™变体上的对称多处理（SMP）支持，即Cortex-A系列处理器的多核版本，在一级缓存级别具有完全的数据一致性。自动缓存和翻译后备缓冲区（TLB）维护传播提供了高效率的SMP操作
- 物理索引、物理标记（PIPT）数据缓存
  

ARM Cortex-A7 processor

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240501153131609.png" alt="image-20240501153131609" style="zoom:67%;" />

ARM Cortex-A8 processor

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240501153253518.png" alt="image-20240501153253518" style="zoom:67%;" />

## 2.2 ARM软件工具链

工具链工作图解

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240320102001177.png" alt="image-20240320102001177" style="zoom: 33%;" />

ARM Compile Toolchain

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240320110304562.png" alt="image-20240320110304562" style="zoom:50%;" />

# 二、X86架构

IA32，Intel Architecture 32bit的简称，Intel公司1985年推出的80386处理器中首先采用，通常被称为X86，i386

实际上X86是一种微处理器执行的计算机语言指令集，标识一套通用的计算机指令集合

下图为8086的微体系架构图，采用x86指令集合

主要构成单元：
	--- 总线接口单元BIU：负责与外部世界通信，包括指令的读取，数据的存取以及各种控制信号的传输等
	--- 执行单元EU：主要负责执行指令，内部包含8各通用寄存器，标志寄存器Flags以及算术逻辑单元ALU

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240501213046572.png" alt="image-20240501213046572" style="zoom: 50%;" />

主要组成组件

- 寄存器：处于CPU存储金字塔的最顶层，容量最小，速度最快（1-10个指令周期）。主要作用是用来存储数据供运算器运算的。各自都有不同的功能
- 控制器：数据寄存器，指令寄存器，程序计数器，指令译码器，时序产生器，操作控制器所组成
- 运算器：运算器由算术逻辑单元（ALU）、累加寄存器、数据缓冲寄存器和状态条件寄存器组成

**一般x86主板设计的硬件架构示意图**

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240503205738133.png" alt="image-20240503205738133" style="zoom:50%;" />

X86/x64体系结构包含的内容：

- 处理器模式：Real-Address Mode、Protected mode、System management mode（SMM）、IA-32e mode（包括Compatibility mode和64bit Mode）
- Memory Management： Segmentation、Paging
- Interrupt And Exception
- Task Management
- Cache Control
- HW Management： Initialization、Power Management、Multiple-Provessor Management
- Virtualization
- Other： MCA、PMC、TSC、Debug

主要应用领域

- 个人计算机
- 服务器
- 工控领域
- 网络应用
- 消费类电子产品
- 安全产品

特点：

- 兼容性：X86架构具备强的兼容性，可以在不同的X86处理器上运行相同的软件
- 软硬件生态系统完善：X86架构的优点还包括其软硬件生态系统的完善，对多任务处理和科学计算兼容性好，适用于各种应用领域
- 技术创新和突破：从早期的8086微处理器到现在的13代酷睿，X86架构经历了多次技术创新和突破，为各行业提供了强大和灵活的计算能力
- 广泛应用于PC、服务器、工作站等计算机领域：X86架构是英特尔公司和AMD公司等厂商所采用的一种计算机体系结构，其指令集架构广泛应用于PC、服务器、工作站等计算机领域
- 性能高，速度快：X86架构以其高性能和快速处理能力而著称，在高性能计算中应用广泛

# 三、其他常用处理器芯片

## 3.1 CPU

Central Processing Unit，中央处理器，一般作为计算机系统的运算和控制核心，信息处理、程序运行的最终执行单元，现代CPU基本上基于哈佛结构进行改进设计

哈佛架构图：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240501165406308.png" alt="image-20240501165406308" style="zoom: 33%;" />

CPU可谓是现代社会科技发展的一颗闪亮明珠

按用途来分类CPU可分为

- 桌面CPU：个人计算机，包括台式机、笔记本
- 服务器CPU：主要用于服务器，对运算性能和稳定性要求高
- 移动端CPU：各种手机、平板
- 嵌入式CPU：汽车电子、工业控制、自动化、智能硬件等方面

## 3.2 PCH

Platform Controller Bub，平台管理控制中心，Intel公司的集成南桥，负责连接PCI总线，IDE设备，I/O设备等

PCH架构取代了英特尔之前的Hub架构（Hub Architecture），其设计解决了处理器与主板之间最终存在的性能瓶颈问题，PCH除了纳入南桥的所有功能外，还纳入了北桥剩余的一些功能（如时钟）

PCH与CPU之间的架构连接图示：

![image-20240501171037106](C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240501171037106.png)

PCH的主要应用：连接CPU、内存和外部设备，内含USB、SATA、PCI-E等接口，可以实现CPU内存和外部设备之间的数据传输和控制

PCH是南桥和北桥的继承者，集成了南桥和北桥的功能，并添加了一些新的功能。PCH负责控制主板上的一些新型接口，例如USB 3.0、Thunderbolt和M.2等，同时还负责网络控制器、声卡和安全芯片等任务。PCH还负责处理主板上的电源管理和温度监测等任务，与南桥类似

## 3.3 GPU

Graphics Processing Unit，图形处理单元，最初专门用于绘制图像和处理图元数据的特定芯片

GPU架构的主要组成部分包括：

- CUDA核心，用于处理各种数学和逻辑运算。
- 内存系统，包括L1、L2高速缓存和共享内存，减少GPU访问主存的延迟。
- 高速缓存和缓存行，提高内存访问效率。
- TPC/SM，CUDA核心的分组结构，每个SM都有自己的CUDA核心和内存。
- Tensor Core，用于执行张量计算，支持并行执行FP32与INT32运算。
- RT Core，负责处理光线追踪加速

GPU架构的更新主要体现在SM、TPC的增加，最终体现在GPU浮点计算能力的提升

应用领域

- 人工智能、深度学习： 利用GPU的并行性能可适用于深度学习模型以及人工智能大模型中训练大量的计算资源（当下最热）

- 边缘计算、科学计算： 在科学领域，如天气预测、气候建模、医学成像等，需要进行大规模数据分析和模拟

- 大数据分析：同样得益于GPU并行计算的特性，GPU可以加速大数据模型的数据处理、分析和可视化

- 游戏和图形渲染： GPU最初是为了图形渲染而设计的，在游戏领域，可以提供高品质的图形效果和流畅的游戏体验
- ······

## 3.4 FPGA

Field Programmable Gate Array，现场可编程门阵列，可以按照开发者的需求配置**指定的电路结构**，一种技术集合，FPGA区别于ASIC，它包括芯片设计、软件技术（EDA），硬件实体等

FPGA可编程的特性决定了其实现数字逻辑的结构不能像专用ASIC那样通过固定的逻辑门电路来完成，而只能采用一种可以重复配置的结构来实现，即查找表(LUT)，目前主流的FPGA芯片仍是基于SRAM工艺的查找表结构

**查找表**

Look-Up-Table，LUT，本质上是一个RAM。目前FPGA内部中多使用4输入的LUT，每一个LUT可以看成一个有4位地址线的RAM

当用户在EDA工具上通过原理图或硬件描述语言设计了一个逻辑电路以后，FPGA开发软件会自动计算逻辑电路的所有可能结果，并把真值表（即结果）事先写入RAM中。这样，每输入一个信号进行逻辑运算就等于输入一个地址进行查找表操作，通过地址找到对应的RAM中的结果，最后将其输出

FPGA程序开发过程通常使用Quartus Ⅱ编译程序 + modelSim仿真软件进行电路仿真，同时配合FPGA开发板来进行程序上板调试
FPGA编程语言一般有Verilog和VHDL两种

更为复杂的FPGA结构有集成嵌入式处理器、Soc的硬件架构方式

产品应用场景（偏重消费电子）

- 通信：通信基站，收发器基带
- 工业航空和国防
- 数据中心：FPGA低延迟、并行计算形成的高吞吐特性，在数据中心领域使用FPGA方案可有效提高数据处理效率
- 高性能计算
- 封测医疗仿真
- 消费电子及汽车领域：包括自动驾驶、AI等方面

## 3.5 MCU

Microcontroller Unit，MCU，微控制单元，可以看作是CPU的减配版 + 集成外设控制的“小巨人”

一般MCU比CPU的频率较低，且将内存、计数器、A/D转换、UART、DMA等周边接口整合到单一芯片上，形成一个具备计算能力的“微型控制器”

以STM32为例，系统架构图如图所示：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240501203938226.png" alt="image-20240501203938226" style="zoom:50%;" />

由上图可知，STM32的芯片实际上属于哈佛架构，整个硬件系统架构中，除Cortex-M系列处理器外还通总线挂载了包括DMA、TIM、ADC等多种外设设备，可提供各种各样的控制功能，适应不同的嵌入式控制场景

主要应用领域（广泛应用于各种嵌入式系统）

- 消费电子方面：智能手机、家电、电视机、游戏机
- 工业自动化方面：MCU用于控制和监测工厂设备、机器人和自动化生产线
- 汽车电子：车载电子系统、车身控制单元······
- 无人机、物联网、医疗设备、计算机网络和通信等场景

# 随笔

冯诺依曼体系

arm体系结构

- 指令执行过程，指令集，指令集译码过程
- 研究寄存器，操作存储单元
- ARM汇编，操作寄存器的语言
- cache机制，缓存空间
- MMU，内存管理单元
- 通信协议

学习资料的获取：ARM官网

instruction set：指令集

eabi： e： Embedded，嵌入式；a： application，应用程序；b： binary，二进制；i： interface，接口
	即嵌入式应用程序二进制接口

