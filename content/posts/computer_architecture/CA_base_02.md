---
title: "CA_base_02"
date: 2021-12-13T14:00:38+08:00
lastmod: 2021-12-13
tags: [computer_architecture]
categories: [School courses]
slug: Instruction system architecture
draft: true
---
# 2 指令系统结构
## 2 指令系统
### 2.1指令系统简介
从上到下，计算机系统可分为四个层次，分别为应用软件、基础软件、硬件电路和物理载体。软件以指令形式运行在CPU硬件上，而指令系统介于软件和硬件之间，是软硬件交互的界面，有着非常关键的作用。软硬件本身的更新迭代速度很快，而指令系统则可以保持较长时间的稳定。有了稳定不变的指令系统界面，软件与硬件得到有效的隔离，并行发展。遵循同一指令系统的硬件可以运行为该指令系统设计的各种软件，比如X86计算机既可运行最新软件，也可运行30年前的软件；反之，为一个指令系统设计的软件可以运行在兼容这一指令系统的不同的硬件实现上，例如同样的操作系统和应用软件在AMD与Intel的CPU上都可以运行。

指令系统包括对指令功能、运行时环境（如存储管理机制和运行级别控制）等内容的定义，涉及软硬件交互的各个方面内容。
### 2.2 指令系统设计原则
- 兼容
- 通用
- 高效
- 安全
### 2.3 指令系统发展历程
#### 2.3.1 指令内容的演变