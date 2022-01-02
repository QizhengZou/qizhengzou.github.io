---
title: "DistributedSystem_HongweiDu_14"
date: 2022-01-01T15:21:52+08:00
lastmod: 2022-01-02
tags: [distributed_system]
categories: [School Courses]
slug: DS_Summary
draft: false
---
# topics
Understanding lecture notes and tutorial questions.
## 1.Introduction
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

## 2.System Model
- Difficulties for and threats to distributed systems
- Physical Model
    - Three generations of distributed systems and the emergence of ultra-large-scale (ULS) distributed systems.
- Architectural Model 
    - Entities that are communicating in the distributed system 
    - Types of communication paradigms:
        - Interprocess communication
        - Remote invocation
        - Indirect communication
    - Architectural styles: client-server and peer-to-peer
    - Vertical distribution (Multi-Tier) and horizontal distribution of c/s systems
- Fundamental Model
    - Characteristics of synchronous distributed systems and asynchronous distributed system(Interaction model)

## 3.Physical Clock Synchronization
### 3.1Cristian’s algorithm: 
synchronize clocks with a UTC server 
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20220102082432.png)
### 3.2Berkeley Algorithm: 
synchronize a set of clocks as close as possible
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20220102082505.png)
## 4.NTP Symmetric mode
- There can be a non-negligible delay between the arrival of one message and the dispatch of the next
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20220102082646.png)
- Delay = total transmission time of the two messages
$$d_i = (T_i – T_{i-3} ) – (T_{i-1}– T_{i-2})$$
- Offset of clock A relative to clock B:  
    - Offset of clock A: $o_i =((T_i-2 – T_i-3 ) + ( T_i-1 – T_i))/2
    - Accuracy bound: d_i /2

## 5.Logical Clock Synchronization:
- Event ordering: happened before (->), concurrent (||)
- Lamport’s algorithm: single timestamp clock synchronization & features 
    - If a -> b then V(a) < V(b)
    - The reverse is not always true
- Timestamp vector synchronization and features
    - a -> b iff V(a) < V(b)
    - a || b iff neither V(a) ≤ V(b) nor V(b) ≤ V(a)
- Application of timestamp vectors: causal ordered multicast

## 6.Interprocess Communication (IPC)
- Socket operations and Socket types
    - Socket/bind
    - listen, accept, connect
    - Read/write functionalities
    - close/shutdown
- Programming by using sockets

## 7.Remote Invocation (RPC)
- Interface Definition of a client-server system
- Client program development
- Server program development
- Client-server interactions in RPC 

## 8.Group Communication
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20220102083158.png)

## 9.Mutual Exclusion & Election Algorithm
- Mutual Exclusion 
    - Centralized Algorithm
    - Token Ring Algorithm
    - Ricart & Agrawala algorithm
    - Distributed Mutual Exclusion
- Election 
    - Bully Algorithm
    - Ring election Algorithm
    - Robert & Chang Algorithm

## 10.Data Centric Consistency Models
- consistency---description
- Strict---**Absolute time ordering** of all shared accesses matters.
- Linearizability---All processes must see all shared accesses in the **same order**. Accesses are furthermore ordered according to a (nonunigue) global timestamp
- Sequential---All processes see all shared accesses in the **same order**. Accesses are not ordered in time
- Causal---All processes see **causally-related** shared accesses in the same order.
- FIFO---All processes see writes from each other **in the order they were used**. Writes from different processes may not always be seen in that order
- Weak---Shared data can be counted on to be consistent **only after a synchronization** is done
- Release---Shared data are made consistent when a **critical region is exited**
- Entry---Shared data **pertaining to a critical region** are made consistent when a **critical region is entered.**

- Consistency models **not using synchronization** operations.
- Models **with synchronization** operations.

## 11.Client Centric Consistency
1. 4 rules of client centric consistency.
2. What is to be propagated?
3. How is it to be propagated?
4. Consistency Protocols
    - Primary-based protocols 
    - Replicated write protocols
    - Cache-coherence protocols

## 12.Internetworking
- CIDR - Classless InterDomain Routing
- NAT - Network Address Translation
- DHCP - Dynamic Host Configuration Protocol
- ARP - Address Resolution Problem
- OSPF - Interior Gateway Routing Protocol 
- BGP - Exterior Gateway Routing Protocol

## 13.Name Services（本有题目，被删了）
- DNS Name Servers
- Recursive query & Iterative query
- DNS Records
- IP-address to Name: Reverse Mapping

## 14.Unix File System
- Internal File Structure : file attributes and data index
- Unix Directory File Directory structure and name resolution
- Superblock
- Internal Structure for File Accesses
- Mount and Unmount 

## 15.Distributed File System
- Distributed File System Components 
- Differences between DFS and stand-alone FS 
- Google FS and NFS

## 16.Transaction Processing System（一定会考）
- Definitions of Atomic Transactions
- RM-ODP properties
- How to achieve atomicity of transactions?
    - Failure Recovery (guarantee nothing-or-all) 
        - Intention list approach
        - Shadow version approach
    - Concurrency Control (guarantee serializability)
        - 2-Phase Locking
        - Timestamp Ordering
        - Optimistic Method

## 17.Web Searching
- Search Engines Architecture
    - Spider
    - Indexer
    - Database
    - Search Engine
- Web Data Mining: Digraph
- FAN - WebPage Ranking
- Inverted File: indexing for search

