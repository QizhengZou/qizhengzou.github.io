---
title: "DistributedSystem_HongweiDu_01"
date: 2021-12-03T14:35:31+08:00
lastmod: 2021-12-03
tags: [distributed_system]
categories: [School Courses]
slug: DistributedSystem HongweiDu intro
draft: false
---
# Intr
HongweiDu(堵宏伟)
## Topics
- Introduction
- Distributed Systems models
- Distributed Time and Clock Synchronization
- Socket Communication
- Remote Method Invocation(RMI)
- Group Communication
- Mutual exclusion & election algorithms
- Replication
- ……
## Motivation
- Resource sharing
    - Computers connected by the network and share resources.
    - Hardware sharing, software sharing,  data sharing, service sharing.media stream sharing.

- Collaborative computing
    - Parallel computing, distributed computing
## Definition
A distributed system is defined as one in which components at networked computers communicate and coordinate their actions only by passing messages.
## Features
The distributed system features:
- Concurrency
    - Multi-process and multi-threads concurrently execute, share resources.
- No global clock
    - Program coordination depend on message passing.
- Independent failure
    - Some processes failure,  can not be known by other processes.
## Challenges
### Heterogeneity
- Middleware
    - Apply to  a software layer that provides a program abstraction as well as masking the heterogeneity of the underlying layers (networks, hardware, operating systems and programming languages). 
    - Example: Java RMI

- Mobile code
    - Program code that can transferred from one computer to another and run at the destination.  Example: Java Applet.The Java virtual machine (JVM) provide a way of making code executable on a variety of host computers.
### Openness
- Computer System Openness
    - Determines whether the system can be extended and reimplemented in various ways.  For example: UNIX.
- Distributed System Openness
    - The degree to which new resource-sharing services can be added and be made available for use by a variety of client programs.
    - RFC (‘Request For Comments’)
### Security
- Confidentiality(机密性）
    - Protection against disclosure to unauthorized individuals.
- Integrity （完整性）
    - Protection against alteration or corruption.
    - e.g.  Checksum (校验和）
- Availability（可用性）
    - Protection against interference with the means to access the resources.
### Scalability(可拓展性)
- Controlling the cost of physical resources
    - As the demand for a resource grows, it should be possible to extend the system, at reasonable cost, to meet it.
- Controlling the performance loss
    - Consider the management of a set of data whose size is proportional to the number of users or resources in the system.
- Preventing software resources running out
    - IPv4, IPv6…..
- Avoiding performance bottlenecks
    - In general, algorithms should be decentralized to avoid having performance bottlenecks. 
### Failure handling
- Detecting failures 
    - Some failure can be detected.
- Masking failures
    - Some failure that have been detected can be hidden or made less severe.
- Tolerating failures
    - Most of the services in the Internet do exhibit failures.
- Recovering from failures
    - Recovery involves the design of software so that the state of permanent data can be recovered or ‘roll back’ after a server has crashed.
- Redundancy
    - Services can be made to tolerate failures by the use of redundant components.
### Concurrency
- Consistent
    - Multi-thread concurrent access the sharing resource.
- Performance
### Transparency
- Access transparency: enables local and remote resources to be accessed using identical operations.
- Location transparency: enables resources to be accessed without knowledge of their physical or network location (for example, which building or IP address).
- Concurrency transparency: enables several processes to operate concurrently using shared resources without interference between them.
- Replication transparency: enables multiple instances of resources to be used to increase reliability and performance without knowledge of the replicas by users or application programmers.
- Failure transparency: enables the concealment of faults, allowing users and application programs to complete their tasks despite the failure of hardware or software components.
- Mobility transparency: allows the movement of resources and clients within a system without affecting the operation of users or programs.
- Performance transparency: allows the system to be reconfigured to improve performance as loads vary.
- Scaling transparency: allows the system and applications to expand in scale without change to the system structure or the application algorithms.
### Quality of Service
- Reliability
- Security
- Performance
- Adaptability
## Conclusion
- Distributed system is everywhere.
- The motivation of constructing a distributed system is resource sharing and collaborative computing
- Distributed system features.
    - Concurrency
    - No global clock
    - Independent failure
- Distributed system challenges.
    - Heterogeneity
    - Openness
    - Security
    - Scalability
    - Failure handling
    - Concurrency
    - Transparency
    - Quality of Service


## 复习
3. 分布式系统 1.4  16:00
    - 8-15小题，英文题目
    - 包括计算题、证明题，最好的答题方法：记住答题要点。
        - 1. 分布式系统的产生、动机：共享资源，
        - 2. 分布式系统特点：并发性、没有全局时钟、独立故障；
        - 3. 分布式系统定义：分布式系统定义为网络计算机上的组件仅通过传递消息进行通信和协调其动作的系统。
        - 4. 分布式系统挑战：
            - 异质性（不同设备应用场景等造成异质性问题），可通过中间件和移动代码解决。
            - 公开性：可以添加新资源共享服务并使其可供各种客户端程序使用的程度。
            - 并发性：同一设备进程之间的并发性，不同设备进程之间的并发性
            - 透明性，所有系统里所发生的对于用户设备来说是透明的。
            - 服务质量：分布式系统建立在不稳定的网络之中，增加服务器解决的是负载问题，单台服务器承受不了，所以要用轮询的机制随机地给一台服务器对客户端进行访问。整个网络问题，不稳定，
        - 2. 系统模型，画系统架构图，描述系统，软硬件：机械式的物理模型。体系结构模型。实体：对象、组件、服务……实体通讯方法：直接式、RMI/RPC、间接通讯模式。分布式方式：水平式分布方式，垂直分布方式。轮询机构。两种实现的方式：接口、逻辑结构（通常放在服务器端）、数据。基础模型：故障模型、交互模型、安全模型……
        - 3. 时钟同步
            - 物理时间：时间是怎么测量的，每一个时钟的标准是什么。如何调整计算机的时钟。线性补偿函数。两个算法：知道大体原理。NTP协议。及相关计算。
            - 逻辑时间。逻辑时间本身是做时间顺序、时间顺序。对于相同时间里的事件，我们希望通过时间戳才决定事件之间的顺序结构。最终通过向量时钟，随便扔出两个消息，告诉向量……请解释原因。或者说已知一个向量时钟……需要重新捡起。对比他们之间的关系。解释为什么相关。对于每一个向量时钟之间的元素。属性之间的关系。要么全小于、要么全大于。既小于又大于就没有什么关系了，并发的，一个练习题。
        - IPC Socket operations：本来是重点，考怎么创建连接，但是做了pro。不考编程。因为pro普遍做的不好。但是所有它所用的系统的操作针对于客户端和用户端。这个需要知道。
        - RPC就过了，不考。因为第二个项目没做。给你小段程序，去设计服务器端的程序。再生成一个接口程序。
        - 群组通信：组播式的通讯，因为所有因特网里面访问的主要是组播通讯。
            - 组播通讯：原子组播、稳定组播、不稳定组播。对于原子组播：怎么样满足原子性的、为什么要满足原子性的。一个持续的日志文件还有一个就是它的example.
            - 对于消息的顺序，全局时钟的顺序。全序、偏序、因果顺序。对于消息，判断消息之间接受顺序，遵循了全局时钟的按照发送之间的顺序结构在接受端按顺序的接收。
        - 数据一致性：严格一致性、顺序一致性、因果一致性……。互斥性的方法。很大一部分三种类型的方法：
            - 集中式的：通过一个协调者去协调到底谁能够去占用那一个资源，访问那一个数据。
            - ……，通过一个token，使得在一个逻辑的环状结构里面的所有节点都能够公平地访问某一个数据。当着一个数据在这一个节点上使它既可以访问又可以不访问到下一个节点上去。
            - 选举型的方法：传递的消息，传递的是IP
    - 5个进程，哪些符合因果一致性、顺序一致性，为什么








