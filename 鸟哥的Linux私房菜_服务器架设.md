鸟哥的Linux私房菜_ 服务器架设篇
吴主华

# 第一篇 服务器搭建前基础

## 第一章 网络

网络：通过望先或这是无线网络技术，将主机与设备链接起来，使得数据可以通过网络介质来传输的一种方式
	**传输方式**

计算机网络组成组件
	节点：node，具有网络地址（IP）的设备统称
	服务器主机：server，提供数据以“响应”给用户的主机，都可以称为服务器
	工作站：workstation，或者客户端(client)，任何在计算机网络输入的设备都可以是工作站；主动发起连接“要求”数据的为客户端
	网卡：network Interface card，NIC，用于提供网络连接，大多使用具有RJ-45接口的以太网卡
	网络接口：提供网络地址（IP）的服务
	网络形态或拓扑：topology，各个节点在网络上的链接方式，一般指物理连接方式
	网关：gateway，具有两个以上的网络接口，可以连接两个以上不同的网段的设备

计算机网络的范围：
	局域网络：Local Area Network，LAN，节点间传输距离近
	广域网：Wide Area Network，WAN，传输距离较远

计算机网络协议：OSI七层协议，Open System Interconnection
<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240330161604611.png" alt="image-20240330161604611" style="zoom:50%;" />

**计算机网络协议**：TCP/IP，由OSI七层协议简化而来，分四层：应用层（相当于OSI中的应用、表示、会话三层），传输层、网络层、网络接口层

### 1.1 以太网

以太网主要使用场景位于局域网中

以太网的网线接头：最常见为RJ-45网络接头，共有8蕊，依据每条蕊线对应不同可分为568A和568B接头
	交叉线：一边为568A一边为568B的接头，用在直接连接两台主机的网卡
	直连线：两边接头同为568A或同为568B，用在链接主机网卡与集线器之间的线缆

以太网的传输协议：CSMA/CD，Carrier Sense Multiple Access with Collision Detection，IEEE 802.3标准
网络共享介质：在单一时间点内，仅能被一台主机所使用（类似十字路口），**集线器**

**MAC的封装格式**

通过CSMA/CD发送出去的数据帧格式即为MAC，可将MAC看成网线上一个包裹，该**包裹**是整个网络硬件上传输的**最小单位**
数据帧格式如下图所示：

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240330203901400.png" alt="image-20240330203901400" style="zoom:50%;" />

其中目的地址和来源地址：指网卡卡号，hardware address

**MTU**： Maximum Transmission Unit，最大传输单元，每种网络接口的MTU都不同

### 1.2 TCP/IP网络层相关数据包与数据

**IP数据包的封装**
	IP数据包的包头资料

<img src="C:/Users/Small Black/AppData/Roaming/Typora/typora-user-images/image-20240330204605403.png" alt="image-20240330204605403" style="zoom:50%;" />

Version：版本			；IHL： Internet Header Length，IP包头的长度；Type of Service：服务类型 ；Total Length：总长度 ；Identification：识别码；Flags：特殊标志				 							 ；Fragment Offset：分段偏移；Time To Live： TTL，生存时间
Protocol Number：协议diamagnetic；Header Checksum：报头校验码；Source Address：来源的IP地址；
Destination Address：目标的IP地址；	Options：其他参数；	Padding：补齐项目

**IP地址的组成与分级**
	IP的组成是32 bits的数值，将32位分成四小段，每段含有8 bits，将8 bits换算成十进制，每段以小数点隔开即成为常见的形式
	`xxx.xxx.xxx.xxx`，2^8 ≤ 256，因此换算成十进制最高为256，从0开始，即范围为0 ~ 255
四段数据中，可分为：前三段和后一段两部分
	前三段：网络号码，Net_ID；后一段：主机号码，Host_ID；同一**物理网段**内，主机的IP具有**相同**的Net_ID，**不同**的Host_ID
	**物理网段**：所有主机在物理设备上实际上是链接在一起，即使用同一个网络设备连接在一起

IP在同一网络的意义
	--- Net_ID和Host_ID限制，同一网段，Net_ID不变，Host_ID不重复，同时Host_ID全为0表示整个网段地址（Network IP），全为1表示广播地址（Broadcast IP）
	--- 在局域网中通过IP广播传递数据，同物理网段的主机可通过CSMA/CD的功能直接在局域网内用广播进行网络的连接/直接网卡传递
	--- 同一物理网段内，如果两个主机使用不同的IP网段地址，则广播地址不同，需要通过路由器来沟通才能连接两个网路

**IP分级**：Inter NIC将整个IP网段分为五种等级，每种等级范围与IP的32 bits数值的前几位相关
	Class A： Net_ID开头为0，对应十进制为`0.xxx.xxx.xxx`
	Class B： Net_ID开头为10，对应十进制为`128.xxx.xxx.xxx`
	Class C： Net_ID开头为110，对应十进制为`192.xxx.xxx.xxx`
	Class D： Net_ID开头为1110，对应十进制为`224.xxx.xxx.xxx`；用来作为组播（multicast）
	Class E： Net_ID开头为1111，对应十进制为`240.xxx.xxx.xxx`；保留美哟硧的网段
IP的种类，IPv4中分两类
	--- Public IP：公共IP，经由Inter NIC所统一规划的IP，有这种IP才能连上Internet
	--- Private IP：私有IP或保留IP，不能直接连上Internet的IP，主要用于局域网络内主机连接规划
				私有IP网段：A：`10.xxx.xxx.xxx`；B：`172.16.0.0~172.31.255.255`；C：`192.168.0.0~192.168.255.255`
				-- 以上三段Class A~C的IP是预留使用的，**不能直接作为Internet上的连接使用**
				-- 私有网段IP的路由信息不对外散播；使用私有IP作为来源或目的地址的数据包不能通过Internet来传送

IP的获取：手动配置、拨号、自动取得网络参数

子网划分： Netmask（或Subnet mask），子网掩码，在Net_ID中划分出最低一位bits来细分出两个子网

路由：负则不同网络之间的数据报传递

**IP与MAC**

ARP，Address Resolution Protocol，网络地址解析协议
RARP，Revers ARP，反向网络地址解析协议

获取本机的ARP表格内的IP/MAC对应数据：`arp` 命令

**ICMP协议**

Internet Control Message Protocol，因特网信息控制协议，一种错误检测与报告的机制，确保网络连接状态与连接的正确性

### 1.3 TCP/IP的传输层相关数据包与数据

通信端口：网络通信两端应该要有一个对应的端口来达成连接信道，好让数据可以通过信道进行沟通

特权端口：privileged Ports，小于1024以下的端口要启动是，启动者的身份必须要是root才行，因此叫特权端口

socket pair： IP与端口形成的成对数据
	来源IP + 来源端口
	目的IP + 目的端口

## 第二章 局域网架构

### 2.1 局域网的连接

让Linux与一般PC的地位相同：每台设备都给予一个相同网络的似有IP即可进行网络连接工作

让Linux与一般PC处于不同的地位：使用Public IP，所有的LAN内的计算机与相关设备都会在同一个网络内

让Linux直接管理LAN：需要Linux具备两张网卡，分别对外和对内

让Linux使用Private IP

# 随笔

[网络发展概览](https://v.douyin.com/iYefNAHF/ u@s.eo 05/03 gBg:/ )

任何新技术的开始都是简单的，技术复杂度的提升肯定是针对某一技术难点的完善，找出技术难点的逻辑就能找到底层原理掌握该技术难点











