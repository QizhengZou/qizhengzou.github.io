---
title: "CN_base_01"
date: 2022-01-23T10:50:16+08:00
lastmod: 2022-01-23
tags: [computer network]
categories: [School courses]
slug: CN_intro
draft: true
---
> 来自 中科大郑烇计网PPT，哈工大深圳堵宏伟计网PPT，改编自计算机网络-自顶向下 第七版
# 概论
10%+15%+15%+60%

计算机网络是通信技术与计算机技术紧密结合的产物，就是一种通信网络，是是互连的、自治的计算机集合。

通信系统模型：
```
信源-》发送设备-》信道-》接收设备-》信宿
                   ^
                   |
                噪声源
```
## 1.1 什么是Internet?

具体构成角度：
- 计算设备
    - 主机=端系统（运行网络应用程序）
- 通信链路
    - 光纤、同轴电缆、无线电、卫星（传输速率：带宽（bps=BitPerSecond））
- 分组交换设备
    - 路由器和交换机（转发分组packets）

或者说：
- 节点
    - 主机及其上运行的应用程序
    - 路由器、交换机等网络交换设备
- 边：通信链路
    - 接入网链路：主机连接到互联网的链路
    - 主干链路：路由器间的链路
- 协议：控制发送、接收消息
    - eg:TCP,IP,HTTP,FTP,PPP……

Internet标准：
- RFC: Request for comments 
- IETF: Internet Engineering Task Force

什么是协议：
- 发送特定的消息以及收到消息时采取的特定行动或其他事件
- Internet 中所有的通信行为都受协议制约
- **协议定义了两个或多个通信实体之间交换的报文格式和次序，以及在报文传输或接收或其他事件方面所采取的行动。**

服务角度看什么是Internet:
- 使用通信设施进行通信的分布式应用 
    - Web、VoIP、email、分布式游戏、电子商务、社交网络
- 通信基础设施为apps提供编程接口（通信服务）
    - 将发送和接收数据的apps与互联网连接起来 
    - 为app应用提供服务选择，类似于邮政服务： 
        - 无连接不可靠服务 
        - 面向连接的可靠服务
## 1.2 网络边缘
网络结构：
- 网络边缘
    - 主机
    - 应用程序（客户端和服务器）
- 网络核心
    - 互联着的路由器
    - 网络的网络（互连的ISP互联网服务提供商以及公用Internet,专用Internet）
- 接入网、物理媒体
    - 有线或者无线通信链路

网络边缘：
- 端系统（主机）
    - 运行应用程序（web,email等）
    - 在“网络的边缘”
- 客户/服务器模式
    - 客户端向服务器请求、接收服务
    - 如web浏览器/服务器；email客户端/服务器
- 对等（peer-peer）模式
    - 很少或者没有专门的服务器
    - eg:Gnutella,KaZaA,Emule

网络边缘：
- 采用网络设施的面向连接服务
    - 目标：在端系统之间传输数据
        - 握手：在数据传输之前做好准备
            - 人类协议：你好，你好
            - 两个通信主机之间为**连接建立状态**
        - TCP-传输控制协议(Transmission Control Protocol)
            - Internet上面向连接的服务
    - TCP服务[RFC 793]
        - 可靠地按顺序地传送数据
            - 确认和重传
        - 流量控制
            - 发送方不会淹没接收方
        - 拥塞控制
            - 当网络拥塞时，发送方降低发送速率
- 采用基础设施的无连接服务
    - 目标：在端系统之间传输数据
        - 无连接服务
        - UDP-用户数据报协议(User Datagram Protocol)
            - 无连接
            - 不可靠数据传输
            - 无流量控制
            - 无拥塞控制
        - 使用TCP的应用
            - HTTP(Web)
            - FTP(文件传送)
            - Telnet(远程登陆)
            - SMTP(email)
        - 使用UDP的应用
            - 流媒体
            - 远程会议
            - DNS
            - Internet电话
## 1.3 网络核心
网络分类：
- 通信网络
    - 电路交换网络
        - FDM
        - TDM
    - 分组交换网络
        - 虚电路网络
        - 数据报网络

网络核心的关键功能：
- 路由：决定分组采用的源到目标的路径（路由算法）
- 转发：将分组从路由器的输入链路转移到输出链路

网络核心：**路由器的网状网络**

数据怎样通过网络进行传输？
- 电路交换
    - 为每个呼叫预留一条专有电路：如电话网
- 分组交换
    - 将要传送的数据分成一个个单位：分组  
    - 将分组从一个路由器传到相邻路由器（hop），一段段最终从源端传到目标端
    - 每段：采用链路的最大传输能力（带宽）

网络核心：**电路交换**
- 端到端的资源被分配给从源端到目标端的呼叫 “call”：
    -  独享资源：不同享
        - 每个呼叫一旦建立起来就能够保证性能
    - 如果呼叫没有数据发送，被分配的资源就会被浪费 (no sharing)
    -  通常被传统电话网络采用
- 为呼叫预留端-端资源
    - 链路带宽、交换能力
    - 专用资源：不共享
    - 保证性能
    - 要求建立呼叫连
- 网络资源（如带宽）被分成片
    - 为呼叫分配片
    - 如果某个呼叫没有数据，则其资源片处于空闲状态（不共享）
    - 将带宽分成片
        - 频分(Frequency-division multiplexing)
        - 时分(Time-division multiplexing)
        - 波分(Wave-division multiplexing)

**计算举例**：在一个电路交换网络上，从主机A到主机B发送一个640,000比特的文件需要多长时间？
- 所有的链路速率为1.536 Mbps
- 每条链路使用时隙数为24的TDM
- 建立端-端的电路需500 m

解：
- 每条链路的速率（一个时间片）：1.536Mbps/24=64kbps
- 传输事件：640kb/64kps=10s
- 共用时间：传输时间+建立链路时间=10s+500ms=10.5s

电路交换不适合计算机之间的通信
- 连接建立时间长
- 计算机之间的通信有突发性，如果使用线路交换，则浪费的片较多
    - 即使这个呼叫没有数据传递，其所占据的片也不能够被别的呼叫使用
- 可靠性不高

网络核心：**分组交换**
- 以分组为单位存储-转发方式
    - 网络带宽资源不再分分为一个个片，传输时使用全部带宽
    - 主机之间传输的数据被分为一个个分组
- 资源共享，按需使用：
    - 存储-转发：分组每次移动一跳（ hop ） 
        - 在转发之前，节点必须收到整个分组 
        - 延迟比线路交换要大 
        - 排队时间

分组交换
- 存储-转发
    - 被传输到下一个链路之前，整个分组必须到达路由器：存储-转发
    - 在一个速率为R bps的链路，一个长度为L bits 的分组的存储转发延时： L/R s
    - 分组的存储转发一段一段从源端传到目标端，按照有无网络层的连接，分成：
        - 数据报网络： 
            - 数据报工作原理：
                - 在通信之前,无须建立起一个连接,有数据就传输
                - 每一个分组都独立路由(路径不一样,可能会失序)
                - 路由器根据分组的目标地址进行路由
            - 分组的目标地址决定下一跳 
            - 在不同的阶段，路由可以改变 
            - 类似：问路 
            - Internent
        - 虚电路网络： 
            - 虚电路工作原理：
            ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123134658.png)
            - 每个分组都带标签（虚电路标识 VC ID），标签决定下一跳 
            - 在呼叫建立时决定路径，在整个呼叫中路径保持不变 
            - 路由器维持每个呼叫的状态信息 
            - X.25 和ATM
- 排队延迟和丢失
    - 排队和延迟：
        - 如果到达速率 \> 链路的输出速率：
            - 分组将会排队，等待传输
            - 如果路由器的缓存用完了，分组将会被抛弃 
- 统计多路复用
    - A&B时复用链路资源，A&B分组没有固定的模式，所有需要统计多路复用

分组交换VS电路交换
- 同样的网络资源，分组交换允许更多用户使用网络
- 分组交换是“突发数据的胜利者”
    - 适合于对突发式数据传输
        - 资源共享
        - 简单，不必建立呼叫
    - 过度使用会造成网络拥塞：分组延时和丢失
        - 对可靠地数据传输需要协议来约束：拥塞控制
    - Q: 怎样提供类似电路交换的服务？
        - 保证音频/视频应用需要的带宽
        - 一个仍未解决的问题(chapter 7)

报文交换：
- 报文：源（应用）发送信息整体，比如一个文件

报文交换VS分组交换
- 报文交换:
    - 报文长度为M bits
    - 链路带宽为R bps
    - 每次传输报文需要M/R秒
- 分组交换:
    - 报文被拆分为多个分组
    - 分组长度为L bits
    - 每个分组传输时延为L/R秒
## 1.4 接入网和物理媒体
### 接入网
接入网：digital subscriber line (DSL,数字用户线)
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123135824.png)

接入网：线缆网络
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123135926.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123140015.png)

接入网：家庭网络
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123140121.png)

怎样将端系统和边缘路由器连接？
- 住宅接入网络（调制解调器modem）
    - 将上网数据调制加载音频信号上，在电话线上传输，在局端将其中的数据解调出来；反之亦然
        - 调频
        - 调幅
        - 调相位
        - 综合调制
    - 拨号调制解调器
        - 56Kbps 的速率直接接入路由器(通常更低)
        - 不能同时上网和打电话：不能总是在线
    - 电缆模式
        - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123140230.png)

- 单位接入网络 （学校、公司）
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123140310.png)
- 无线接入网络
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123140331.png)
**注意**：
- 接入网络的带宽 (bits per second) 
- 共享/专用

### 物理媒体
- Bit: 在发送-接收对间传播
- 物理链路：连接每个发送-接收对之间的物理媒体
- 导引型媒体: 
    - 信号沿着固体媒介被导引：同轴电缆、光纤、 双绞线
        - 同轴电缆
            - 两根同轴的铜导线
            - 双向
            - 基带电缆： 
                - 电缆上一个单个信道 
                - Ethernet
            - 宽带电缆： 
                - 电缆上有多个信道 
                - HFC
        - 光纤和光缆
            - 光脉冲，每个脉冲表示一个bit，在玻璃纤维中传输
            - 高速：点到点的高速传输（如10Gbps-100Gbps传输速率）
            - 低误码率：在两个中继器之间可以有很长的距离，不受电磁噪声的干扰
            - 安全
- 非导引型媒体： 
    - 开放的空间传输电磁波或者光信号，在电磁或者光信号中承载数字数据
    - 无线链路：
        - 开放空间传输电磁波，携带要传输的数据
        - 无需物理“线缆”
        - 双向
        - 传播环境效应：
            - 反射
            - 吸收
            - 干扰
        - 类型有：
            - 地面微波:45Mbps
            - LAN（eg:wifi）
            - wide-area（eg:蜂窝）
                - 3G:~Mbps
                - 4G:10Mbps
                - 5G:数Gbps
            - 卫星
                - 每个信道Kbps到45Mbps（或者多个聚集信道）
                - 270msec端到端延迟
                - 同步静止卫星和低轨卫星
- 双绞线 (TP)
    - 两根绝缘铜导线拧合 
        - 5类：100Mbps 以太网，Gbps 千兆位以太网 
        - 6类：10Gbps万兆以太

## 1.5 Internet结构和ISP
互联网络结构：网络的网络
- 端系统通过接入ISPs (Internet Service Providers)连接到互联网 
    - 住宅，公司和大学的ISPs 
- 接入ISPs相应的必须是互联的 
    - 因此任何2个端系统可相互发送分组到对方 
- 导致的“网络的网络”非常复杂 
    - 发展和演化是通过经济的和国家的政策来驱动的 
- 让我们采用渐进方法来描述当前互联网的结构

给定数百万接入ISPs，如何将它们互联到一起？
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123142814.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123142848.png)
- 但是，如果全局ISP是可行的业务，那会有竞争者有利可图，一定会有竞争。与此同时，通过ISP之间的合作可以完成业务的扩展，肯定会有互联，对等互联的结算关系
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143046.png)
- 然后业务会细分（全球接入和区域接入），区域网络将出现，用与将接入ISPs连接到全局ISPs
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143129.png)
- 然后内容提供商网络(Internet Content Providers,e.g., Google,Microsoft，Akamai) 可能会构建它们自己的网络，将它们的服务、内容更加靠近端用户，向用户提供更好的服务,减少自己的运营支出
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143255.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143336.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143602.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143619.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143648.png)
- 很多内容提供商(如：Google, Akamai )可能会部署自己的网络,连接自己的在各地的DC（数据中心），走自己的数据
- 连接若干local ISP和各级（包括一层）ISP,更加靠近用户
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143838.png)

**ISP之间的连接**
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123143952.png)
- POP: 高层ISP面向客户网络的接入点，涉及费用结算 
    - 如一个低层ISP接入多个高层ISP，多宿（multi home）
- 对等接入：2个ISP对等互接，不涉及费用结算
- IXP：多个对等ISP互联互通之处，通常不涉及费用结算 
    - 对等接入
- ICP自己部署专用网络，同时和各级ISP连接
## 1.6 分组延时、丢失和吞吐量
分组丢失和延时是怎样发生的？
- 在路由器缓冲区的分组队列
    - 分组到达链路的速率超过了链路输出的能力
    - 分组等待排到队头、被传输
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123144141.png)

四种分组延时：
- 1. 节点处理延时： 
    - 检查 bit级差错 
    - 检查分组首部和决定将分组导向何处
- 2. 排队延时
    - 在输出链路上等待传输的时间
    - 依赖于路由器的拥塞程度
- 3. 传输延时:
    - R=链路带宽(bps)
    - L=分组长度(bits)
    - 将分组发送到链路上的时间= L/R
    - 存储转发延时
- 4. 传播延时:
    - d = 物理链路的长度
    - s = 在媒体上的传播速度(~2x108 m/sec)
    - 传播延时 = d/s

车队类比：![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123144751.png)
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123144818.png)

节点延时：
- $d_{nodal}=d_{proc}+d_{queue}+d_{trans}+d_{prop}$
- $d_{proc}$ = 处理延时 
    - 通常是微秒数量级或更少
- $d_{queue}$ = 排队延时 
    - 取决于拥塞程度
- $d_{trans}$ = 传输延时 
    - = L/R, 对低速率的链路而言很大（如拨号），通常为微秒级到毫秒级
- $d_{prop}$ = 传播延时 
    - 几微秒到几百毫秒

![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123145426.png)

Internet的延时和路由
- Internet 的延时和路由是什么样的呢? 
- Traceroute 诊断程序: 提供从源端，经过路由器，到目的的延时测量
    - For all i:
        - 沿着目的的路径，向每个路由器发送3个探测分组
        - 路由器 i 将向发送方返回一个分组
        - 发送方对发送和回复之间间隔计时
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123145646.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123145725.png)

关于traceroute
- 在Windows系统下
    - Tracerert hostname 
        - 如 Tracerert www.gucas.ac.cn
    - 更完整的例子
        - tracert [-d] [-h maximum_hops] [-j computer-list] [-w timeout] target_name  
- 测试网址：
    - www.traceroute.org
    - www.linkwan.com

分组丢失：
- 链路的队列缓冲区容量有限
- 当分组到达一个满的队列时，该分组将会丢失
- 丢失的分组可能会被前一个节点或源端系统重传，或根本不重传
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123150111.png)

吞吐量：
- 吞吐量: 在源端和目标端之间传输的速率（数据量/单位时间）
    - 瞬间吞吐量: 在一个时间点的速率
    - 平均吞吐量: 在一个长时间内平均值
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123150204.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123150244.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123150311.png)
- 互联网场景：![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123150515.png)
## 1.7 协议层次及服务模型
网络是一个复杂的系统! 
- 网络功能繁杂：数字信号的物理信号承载、点到点、路由、rdt、进程区分、应用等
- 现实来看，网络的许多构成元素和设备:
    - 主机
    - 路由器
    - 各种媒体的链路
    - 应用
    - 协议
    - 硬件,软件

如何组织和实现这个复杂的网络功能？

**层次化方式实现复杂网络功能:**
- 将网络复杂的功能分层功能明确的层次，每一层实现了其中一个或一组功能，功能中有其上层可以使用的功能：服务 
- 本层协议实体相互交互执行本层的协议动作，目的是实现本层功能，通过接口为上层提供更好的服务 
- 在实现本层协议的时候，直接利用了下层所提供的服务 
- 本层的服务：借助下层服务实现的本层协议实体之间交互带来的新功能（上层可以利用的）+更下层所提供的服务

服务和服务访问点：
- 服务( Service)：低层实体向上层实体提供它们之间的通信的能力
    - 服务用户(service user)
    - 服务提供者(service provider )
- 原语(primitive)：上层使用下层服务的形式，高层使用低层提供的服务，以及低层向高层提供服务都是通过服务访问原语来进行交互的形式
- 服务访问点 SAP (Services Access Point) ：上层使用下层提供的服务通过层间的接口—地点； 
    - 例子:邮箱 
    - 地址(address)：下层的一个实体支撑着上层的多个实体，SAP有标志不同上层实体的作用 
    - 可以有不同的实现，队列 
    - 例子:传输层的SAP: 端口(port)

服务的类型：
- 面向连接的服务和无连接的服务-方式
    - 面向连接的服务（Connection-oriented Service）
        - 连接 (Connection)：两个通信实体为进行通信而建立的一种结合
        - 面向连接的服务通信的过程：建立连接，通信，拆除连接
        - 面向连接的服务的例子：网络层的连接被成为虚电路
        - 适用范围：对于大的数据块要传输; 不适合小的零星报文
        - 特点：保序
        - 服务类型: 
            - 可靠的信息流 传送页面(可靠的获得，通过接收方的确认)
            - 可靠的字节流 远程登陆
            - 不可靠的连接 数字化声音
- 面向连接的服务和无连接的服务
    - 无连接的服务(Connectionless Service)
        - 无连接服务：两个对等层实体在通信前不需要建立一个连接，不预留资源；不需要通信双方都是活跃；(例：寄信)
        - 特点：不可靠、可能重复、可能失序
        - IP分组，数据包；
        - 适用范围：适合传送零星数据；
        - 服务类型： 
            - 不可靠的数据报 电子方式的函件
            - 有确认的数据报 挂号信
            - 请求回答 信息查询

服务和协议的区别和联系：
- 区别
    - 服务(Service)：低层实体向上层实体提供它们之间的通信的能力，是通过原语(primitive)来操作的，垂直
    - 协议(protocol) ：对等层实体(peer entity)之间在相互通信的过程中，需要遵循的规则的集合，水平
- 联系
    - 本层协议的实现要靠下层提供的服务来实现
    - 本层实体通过协议为上层提供更高级的服务

数据单元（DU）
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123152136.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123152156.png)

分层处理和实现复杂系统的好处：
- 对付复杂的系统
    - 概念化：结构清晰，便于标示网络组件，以及描述其相互关系
        - 分层参考模型
    - 结构化：模块化更易于维护和系统升级
        - 改变某一层服务的实现不影响系统中的其他层次 
            - 对于其他层次而言是透明的
        - 如改变登机程序并不影响系统的其它部分 
            - 改变2个秘书使用的通信方式不影响2个翻译的工作 
            - 改变2个翻译使用的语言也不影响上下2个层次的工作

**Internet协议栈**
- 应用层: 网络应用 
    - 为人类用户或者其他应用进程提供网络应用服务 
    - FTP, SMTP, HTTP,DNS
- 传输层: 主机之间的数据传输 
    - 在网络层提供的端到端通信基础上，细分为进程到进程，将不可靠的通信变成可靠地通信 
    - TCP, UDP
- 网络层: 为数据报从源到目的选择路由 
    - 主机主机之间的通信，端到端通信，不可靠 
    - IP, 路由协议
- 链路层: 相邻网络节点间的数据传输 
    - 2个相邻2点的通信，点到点通信，可靠或不可靠 
    - 点对对协议PPP, 802.11(wifi), Ethernet
- 物理层: 在线路上传送bit

ISO/OSI 参考模型:
- 表示层: 允许应用解释传输的数据, e.g., 加密，压缩，机
器相关的表示转换
- 会话层: 数据交换的同步，检查点，恢复
- 互联网协议栈没有这两层!
    - 这些服务，如果需要的话，必须被应用实现
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123153351.png)

封装和解封装：![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123153432.png)
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123153519.png)

**各层次的协议数据单元**
- 应用层：报文(message)
- 传输层：报文段(segment)：TCP段，UDP数据报
- 网络层：分组packet（如果无连接方式：数据报datagram）
- 数据链路层：帧(frame)
- 物理层：位(bit)
## 1.8 历史
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20220123153636.png)
## 回顾与小结
- 什么是Internet ?
- 什么是协议?
- 网络边缘
- 接入网、物理媒体
- 网络核心：分组交换、线路交换
- Internet/ISP 结构
- 性能：丢包、延时、吞吐量
- 协议层次、服务模型
- 历史

- 组成角度看 什么是互联网 
    - 边缘：端系统（包括应用）+接入网 
    - 核心：网络交换设备+通信链路 
    - 协议：对等层实体通信过程中遵守的规则的集合 
        - 语法，语义，时序
- 为了实现复杂的网络功能，采用分层方式设计、实现和调试 
    - 应用层，传输层，网络层，数据链路层，物理层 
    - 协议数据单位： 
        - 报文，报文段，分组，帧，位
- 从服务角度看互联网 
    - 通信服务基础设施 
        - 提供的通信服务：面向连接 无连接 
    - 应用
- 应用之间的交互 
    - C/S模式 
    - P2P模
- 数据交换 
    - 分组数据交换 
    - 线路交换
- 比较线路交换和分组交换
- 分组交换的2种方式 
    - 虚电路 
    - 数据报
- 接入网和物理媒介 
    - 接入网技术： 
        - 住宅：ADSL，拨号，cable modem 
        - 单位：以太网 
        - 无线接入方式 
    - 物理媒介 
        - 光纤，同轴电缆，以太网，双绞线
- ISP层次结构
- 分组交换网络中延迟和丢失是如何发生的
    - 延迟的组成：处理、传输、传播、排队
- 网络的分层体系结构
    - 分层体系结构
    - 服务
    - 协议数据单元
    - 封装与解封装
- 历史