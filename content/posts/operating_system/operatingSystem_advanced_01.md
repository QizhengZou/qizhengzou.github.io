---
title: "OperatingSystem_advanced_01"
date: 2021-12-01T11:08:12+08:00
lastmod: 2021-12-01
tags: [operating system]
categories: [Advanced learning]
slug: why OS?
draft: true
---


# 自己实现一个操作系统呗
## 前言
你是否也跟我一样，曾经在一个数千万行代码的大项目中茫然失措？一次次徘徊在内存为什么会泄漏、服务进程为什么会 dang 掉、文件为什么打不开等一系列“基础”问题的漩涡中？你是否惊叹于 Nginx 的高并发性？是不是感觉 Golang 的垃圾回收器真的很垃圾？除了这样的感叹，你也许还好奇过这样一些问题：MySQL 的 I/O 性能还能不能再提升？网络服务为什么会掉线？Redis 中经典的 Reactor 设计模式靠什么技术支撑？Node.js 的 I/O 模型长什么模样……

如果你是后端工程师，在做高性能服务端编程的时候，内存、进程、线程、I/O 相关的知识就会经常用到。还有，在做一些前端层面的性能调优时，操作系统相关的一些知识更是必不可少。除了 Web 开发，做高性能计算超级计算机的时候，操作系统内核相关的开发能力也至关重要。其实，即使单纯的操作系统内核相关的开发能力，对于工程师来说也是绕不过的基本功。对于运维、测试同学，你要维护和测试的任何产品，其实是基于操作系统的。比如给服务配置多大的内存、多大的缓存空间？怎样根据操作系统给出的信息，判断服务器的问题出现在哪里。随着你对操作系统的深入理解和掌握，你才能透过现象看本质，排查监控思路也会更开阔。除了工作，操作系统离我们的生活也并不遥远，甚至可以说是息息相关。要知道，操作系统其实不仅仅局限于手机和电脑，你的智能手表、机顶盒、路由器，甚至各种家电中都运行着各种各样的操作系统。

操作系统相关的内容，已经成为你涨薪、晋升的必考项，比如 Linux 内核相关的技术，中断、I/O、网络、多线程、并发、性能、内存管理、系统稳定性、文件系统、容器和虚拟化等等，这些核心知识都来源于操作系统。
## 0.学习过程
- 从了解计算机的资源开始，如 CPU、MMU、内存和 Cache。其次要为这个操作系统设计基本法，即各种同步机制，如信号量与自旋锁。
- 接着进行授权，从固件程序的手中抢过计算机并进行初始化，其中包含初始化 CPU、内存、中断、显示等。
- 然后，开始建设中枢的各级部门，它们分别是内存管理部门、进程管理部门、I/O 管理部门、文件管理部门、通信管理部门。
- 最后将这些部门组合在一起，就形成了计算机操作系统。
- 最终实现一个基于 x86 平台的 64 位多进程的操作系统——Cosmos。

认知(2、3)->设计(4、5)->硬件(6、7、8)->同步原语(9、10)->启动初始化(11、12、13)->内存(14、15、16、17)->进程(18、19、20)->IO(21、22、23、24)->FS(25、26、27、28)->网络(29、30、31、32、33)->接口(34、35)->番外(36、37、38、39)

![img](https://static001.geekbang.org/resource/image/d6/d9/d68f8a262c1582f04377476f9ed9yyd9.jpg?wh=3145*2404)

我们的课程就是按照上述逻辑，依次为你讲解这些部门的实现过程和细节。每节课都配有可以工作的代码，让你能跟着课程一步步实现。你也可以直接使用我提供的[代码](https://gitee.com/lmos/cosmos)一步步调试，直到最终实现一个基于 x86 平台的 64 位多进程的操作系统——Cosmos。

![img](https://static001.geekbang.org/resource/image/5f/cf/5fbeyy963478d11db45da0dd3e8effcf.jpg?wh=3245*2265)

下面，我给出一个简化的操作系统知识体系图，也是后面课程涉及到的所有知识点。尽管图中只是最简短的一些词汇，但随着课程的展开，你会发现图中的每一小块，都犹如一片汪洋。

![img](https://static001.geekbang.org/resource/image/2c/bd/2c6abcd035e5c83cdd7d356eca26b9bd.jpg?wh=6120*6599)

![img](https://static001.geekbang.org/resource/image/ae/cc/aefea2e304e431a85e95684f1a2f7bcc.jpg?wh=1814x1112)

如果你还是想把操作系统的相关资料也都一并啃下来，那可以看看 LMOS 提供的参考书单，在学有余力的情况下拓展阅读。
1. 关于编译工具：LD 手册、GAS 手册、[GCC 手册](https://www.gnu.org/manual/manual.html)、[nasm 手册](https://www.nasm.us/xdoc/2.15.05/html/nasmdoc0.html)、[make 手册](https://www.gnu.org/software/make/manual/make.html)；
2. 关于 GRUB：[GRUB 手册](https://www.gnu.org/software/grub/grub-documentation.html)；
3. 关于 CPU：[Intel 手册](https://software.intel.com/content/www/us/en/develop/articles/intel-sdm.html)；
4. 关于汇编：《汇编程序设计》；
5. 关于 C 语言：[《C 语言程序设计现代方法》](https://book.douban.com/subject/35503091/)；
6. 关于操作系统：《操作系统设计与实现》。

## 1.基础之程序的运行过程
第一位牛人，是世界级计算机大佬的传奇——Unix 之父 Ken Thompson。在上世纪 60 年代的一个夏天，Ken Thompson 的妻子要回娘家一个月。呆在贝尔实验室的他，竟然利用这极为孤独的一个月，开发出了 UNiplexed Information and Computing System（UNICS）——即 UNIX 的雏形，一个全新的操作系统。要知道，在当时 C 语言并没有诞生，从严格意义上说，他是用 B 语言和汇编语言在 PDP-7 的机器上完成的。

牛人的朋友也是牛人，他的朋友 Dennis Ritchie 也随之加入其中，共同创造了大名鼎鼎的 C 语言，并用 C 语言写出了 UNIX 和后来的类 UNIX 体系的几十种操作系统，也写出了对后世影响深远的第一版“Hello World”：
```c
#include "stdio.h"
int main(int argc, char const *argv[])
{
  printf("Hello World!\n");
  return 0;
}
```
计算机硬件是无法直接运行这个 C 语言文本程序代码的，需要 C 语言编译器，把这个代码编译成具体硬件平台的二进制代码。再由具体操作系统建立进程，把这个二进制文件装进其进程的内存空间中，才能运行。


### 程序编译过程
使用 GCC 相关的工具链。那么使用命令：gcc HelloWorld.c -o HelloWorld 或者 gcc ./HelloWorld.c -o ./HelloWorld ，就可以编译这段代码。其实，GCC 只是完成编译工作的驱动程序，它会根据编译流程分别调用预处理程序（->HelloWorld.i）、编译程序（->HelloWorld.s）、汇编程序（->HelloWorld.o）、链接程序(将HelloWorld.o与其他的库进行链接形成可执行文件)来完成具体工作。

![img](https://static001.geekbang.org/resource/image/f2/4a/f2b10135ed52436888a793327e4d5a4a.jpg?wh=3015*2410)

其实，我们也可以手动控制以上这个编译流程，从而留下中间文件方便研究：
- gcc HelloWorld.c -E -o  HelloWorld.i 预处理：加入头文件，替换宏。
- gcc HelloWorld.c -S -c -o HelloWorld.s 编译：包含预处理，将 C 程序转换成汇编程序。
- gcc HelloWorld.c -c -o HelloWorld.o 汇编：包含预处理和编译，将汇编程序转换成可链接的二进制程序。
- gcc HelloWorld.c -o HelloWorld 链接：包含以上所有操作，将可链接的二进制程序和其它别的库链接在一起，形成可执行的程序文件。

反汇编指令objdump细节可[参考](https://zhuanlan.zhihu.com/p/335550245)
### 程序装载执行
对运行内容有了了解后，我们开始程序的装载执行。

- 图灵机：
    - 一个抽象的模型：有一条无限长的纸带，纸带上有无限个小格子，小格子中写有相关的信息，纸带上有一个读头，读头能根据纸带小格子里的信息做相关的操作并能来回移动。

这个理想的模型是好，但是理想终归是理想，想要成为现实，我们得想其它办法。于是，第四位牛人来了，他提出了电子计算机使用二进制数制系统和储存程序，并按照程序顺序执行，他叫冯诺依曼，他的电子计算机理论叫冯诺依曼体系结构。
- 根据冯诺依曼体系结构构成的计算机，必须具有如下功能：
    - 把程序和数据装入到计算机中；
    - 必须具有长期记住程序、数据的中间结果及最终运算结果；
    - 完成各种算术、逻辑运算和数据传送等数据加工处理；
    - 根据需要控制程序走向，并能根据指令控制机器的各部件协调操作；
    - 能够按照要求将处理的数据结果显示给用户。

- 为了完成上述的功能，计算机必须具备五大基本组成部件：
    - 装载数据和程序的输入设备；
    - 记住程序和数据的存储器；
    - 完成数据加工处理的运算器；
    - 控制程序执行的控制器；
    - 显示处理结果的输出设备。


根据冯诺依曼的理论，我们只要把图灵机的几个部件换成电子设备，就可以变成一个最小核心的电子计算机，如下图：

![img](https://static001.geekbang.org/resource/image/bd/26/bde34df011c397yy42dc00fe6bd35226.jpg?wh=1386*1026)

是不是非常简单？这次我们发现读头不再来回移动了，而是靠地址总线寻找对应的“纸带格子”。读取写入数据由数据总线完成，而动作的控制就是控制总线的职责了。



下面，我们尝试将 HelloWorld 程序装入这个原型计算机，在装入之前，我们先要搞清楚 HelloWorld 程序中有什么。我们可以通过 gcc -c -S HelloWorld 得到（只能得到其汇编代码，而不能得到二进制数据）。我们用 objdump -d HelloWorld 程序，得到 /lesson01/HelloWorld.dump，其中有很多库代码（只需关注 main 函数相关的代码），如下图：

![img](https://static001.geekbang.org/resource/image/39/14/3991a042107b90612122b14596c65614.jpeg?wh=820*291)

以上图中，分成四列：第一列为地址；第二列为十六进制，表示真正装入机器中的代码数据；第三列是对应的汇编代码；第四列是相关代码的注释。这是 x86_64 体系的代码，由此可以看出 x86 CPU 是变长指令集。接下来，我们把这段代码数据装入最小电子计算机，状态如下图：

![img](https://static001.geekbang.org/resource/image/5d/6e/5d4889e7bf20e670ee71cc9b6285c96e.jpg?wh=3810*1815)


**现代电子计算机正是通过内存中的信息（指令和数据）做出相应的操作，并通过内存地址的变化，达到程序读取数据，控制程序流程（顺序、跳转对应该图灵机的读头来回移动）的功能。和图灵机的核心思想相比，没有根本性的变化。只要配合一些 I/O 设备，让用户输入并显示计算结果给用户，就是一台现代意义的电子计算机。**



## 2.实现一个最简单的内核
基于硬件，写一个最小的操作系统——Hello OS

本节课的配套代码，你可以从[这里](https://gitee.com/lmos/cosmos/tree/master/lesson02/HelloOS)下载。
### PC 机的引导流程
写操作系统要用汇编和 C 语言，尽管这个 Hello OS 很小，但也要用到两种编程语言。其实，现有的商业操作系统都是用这两种语言开发出来的。
- hello os的引导流程：
    - PC机加电
    - PC机BIOS固件
    - 加载可引导设备中的**GRUB**(GrandUnified Boot Loader引导内核加载程序)
    - GRUB引导
    - 加载硬盘分区的hello os 文件
    - hello os

- **PC机BIOS固件**：
    - PC 机 BIOS 固件是固化在 PC 机主板上的 ROM 芯片中的，掉电也能保存，PC 机上电后的第一条指令就是 BIOS 固件中的，它负责**检测和初始化 CPU、内存及主板平台，然后加载引导设备（大概率是硬盘）中的第一个扇区数据（这个扇区一共512字节，前446字节内容存放grub（bootloader）的关键引导程序，接着64字节放置硬盘分区表DPT（Disk Partition Table），一共可以有四个主分区，占64个字节，这也是为什么主分区最多只有四个的原因，最后2个字节是固定的标志0x55AA。）到 0x7c00 地址开始的内存空间，再接着跳转到 0x7c00 处执行指令**，在我们这里的情况下就是 GRUB 引导程序。当然，更先进的[UEFI BIOS](https://www.uefi.org/)则不同，这里就不深入其中了，你可以通过链接自行了解。
    - 当BIOS把引导程序加载到内存后就把控制权交给grub，而后grub的剩余代码将完成其它代码的加载和搬移以及文件系统初始化查找等工作，最终加载内核映像文件，从而把控制权交给真正的内核运行。（[参考](https://blog.csdn.net/rosetta/article/details/8687556)）
### Hello OS 引导汇编代码
为什么不直接开始用c写操作系统？
- C 作为通用的高级语言，不能直接操作特定的硬件，而且 C 语言的函数调用、函数传参，都需要用栈。
- 栈简单来说就是一块内存空间，其中数据满足后进先出的特性，它由 CPU 特定的栈寄存器指向，所以我们要先用汇编代码处理好这些 C 语言的工作环境。
```s
;彭东 @ 2021.01.09
MBT_HDR_FLAGS EQU 0x00010003
MBT_HDR_MAGIC EQU 0x1BADB002 ;多引导协议头魔数
MBT_HDR2_MAGIC EQU 0xe85250d6 ;第二版多引导协议头魔数
global _start ;导出_start符号
extern main ;导入外部的main函数符号
[section .start.text] ;定义.start.text代码节
[bits 32] ;汇编成32位代码
_start:
jmp _entry
ALIGN 8
mbt_hdr:
dd MBT_HDR_MAGIC
dd MBT_HDR_FLAGS
dd -(MBT_HDR_MAGIC+MBT_HDR_FLAGS)
dd mbt_hdr
dd _start
dd 0
dd 0
dd _entry
;以上是GRUB所需要的头
ALIGN 8
mbt2_hdr:
DD MBT_HDR2_MAGIC
DD 0
DD mbt2_hdr_end - mbt2_hdr
DD -(MBT_HDR2_MAGIC + 0 + (mbt2_hdr_end - mbt2_hdr))
DW 2, 0
DD 24
DD mbt2_hdr
DD _start
DD 0
DD 0
DW 3, 0
DD 12
DD _entry
DD 0
DW 0, 0
DD 8
mbt2_hdr_end:
;以上是GRUB2所需要的头
;包含两个头是为了同时兼容GRUB、GRUB2
ALIGN 8
_entry:
;关中断
cli
;关不可屏蔽中断
in al, 0x70
or al, 0x80
out 0x70,al
;重新加载GDT
lgdt [GDT_PTR]
jmp dword 0x8 :_32bits_mode
_32bits_mode:
;下面初始化C语言可能会用到的寄存器
mov ax, 0x10
mov ds, ax
mov ss, ax
mov es, ax
mov fs, ax
mov gs, ax
xor eax,eax
xor ebx,ebx
xor ecx,ecx
xor edx,edx
xor edi,edi
xor esi,esi
xor ebp,ebp
xor esp,esp
;初始化栈，C语言需要栈才能工作
mov esp,0x9000
;调用C语言函数main
call main
;让CPU停止执行指令
halt_step:
halt
jmp halt_step
GDT_START:
knull_dsc: dq 0
kcode_dsc: dq 0x00cf9e000000ffff
kdata_dsc: dq 0x00cf92000000ffff
k16cd_dsc: dq 0x00009e000000ffff
k16da_dsc: dq 0x000092000000ffff
GDT_END:
GDT_PTR:
GDTLEN dw GDT_END-GDT_START-1
GDTBASE dd GDT_START
```
以上的汇编代码（/lesson02/HelloOS/entry.asm）分为 4 个部分：
1. 1~40行用汇编定义的 GRUB 的多引导协议头，其实就是一定格式的数据，我们的 Hello OS 是用 GRUB 引导的，当然要遵循 GRUB 的多引导协议标准，让 GRUB 能识别我们的 Hello OS。之所以有两个引导头，是为了兼容 GRUB1 和 GRUB2。
2. 44~52行关掉中断，设定 CPU 的工作模式。
3. 54~73行初始化cpu的寄存器和c语言的运行环境，最后调用c语言的mian函数
4. 78~87行GDT_START 开始的，是 CPU 工作模式所需要的数据。
### HELLO OS的主函数
main函数是用 C 语言写的在（main.c）中，最终它们分别由 **nasm**(NASM是一个为可移植性与模块化而设计的一个80x86的汇编器。它支持相当多的目标文件格式，包括Linux和''NetBSD/FreeBSD'',''a.out'',''ELF'',''COFF'',微软16位的''OBJ''和''Win32''。它还可以输出纯二进制文件。它的语法设计得相当的简洁易懂，和Intel语法相似但更简单。它持''Pentium'',''P6'',''MMX'',''3DNow!'', ''SSE'' and ''SSE2''指令集。[参考](https://www.cnblogs.com/wufu/articles/5077359.html)) 和 GCC 编译成可链接模块，由 **LD 链接器**链接在一起，形成可执行的程序文件。

main.c
```c
#include "vgastr.h"
void main()
{
  printf("Hello OS!");
  return;
} 
```
vgastr.h：控制计算机屏幕VGABIOS固件程序显示特定字符（自己实现）
printf也需要自己实现
### 控制计算机屏幕
计算机屏幕显示往往是显卡的输出，显卡有很多形式：集成在主板的叫集显，做在 CPU 芯片内的叫核显，独立存在通过 PCIE 接口（PCIe是PCI Express的简写，是新一代的总线接口，是由20多家业界主导公司共同起草并完成的新技术规范，采用点对点的串行连接，可以将数据传输率提高到一个很高的频率，以此提供更高的带宽。[参考](new.qq.com/omn/20200701/20200701A0I3C000.html)）连接的叫独显，性能依次上升，价格也是。
- 独显
    - 3D 图形显示往往要涉及顶点处理、多边形的生成和变换、纹理、着色、打光、栅格化等。而这些任务的计算量超级大，所以独显往往有自己的 RAM、多达几百个运算核心的处理器。因此独显不仅仅是可以显示图像，而且可以执行大规模并行计算，比如“挖矿”。

要在屏幕上显示字符，就要编程操作显卡。

无论PC上是什么显卡，它们都支持一种叫 VESA 的标准，这种标准下有两种工作模式：字符模式和图形模式。显卡们为了兼容这种标准，不得不自己提供一种叫 VGABIOS 的固件程序。

显卡的字符模式的工作细节:
- 它把屏幕分成 24 行，每行 80 个字符，把这（24*80）个位置映射到以 0xb8000 地址开始的内存中，每两个字节对应一个字符，其中一个字节是字符的 ASCII 码，另一个字节为字符的颜色值。

![img](https://static001.geekbang.org/resource/image/78/f5/782ef574b96084fa44a33ea1f83146f5.jpg?wh=3530*1605)

注：C 语言字符串是以 0 结尾的，其字符编码通常是 utf8，而 utf8 编码对 ASCII 字符是兼容的，即英文字符的 ASCII 编码和 utf8 编码是相等的

在vgastr.h里实现：

```c
void _strwrite(char* string)
{
  char* p_strdst = (char*)(0xb8000);//指向显存的开始地址
  while (*string)                   //将字符串里每个字符依次定入到 0xb8000 地址开始的显存中。
  {
    *p_strdst = *string++;
    p_strdst += 2;                  //为了跳过字符的颜色信息的空间。
  }
  return;
}

void printf(char* fmt, ...)
{
  _strwrite(fmt);
  return;
}
```
### 编译和安装 Hello OS
不像HELLO OS,一个成熟的商业操作系统多达几万个代码模块文件，几千万行的代码量。需要一个工具来控制这个巨大的编译过程----make
#### make工具
在软件开发中，make 是一个工具程序，它读取一个叫“makefile”(makefile是一种用于编译的脚本语言)的文件，也是一种文本文件，这个文件中写好了构建软件的规则，它能根据这些规则自动化构建软件。

makefile 文件中规则是这样的：首先有一个或者多个构建目标称为“target”；目标后面紧跟着用于构建该目标所需要的文件，目标下面是构建该目标所需要的命令及参数。与此同时，它也检查文件的依赖关系，如果需要的话，它会调用一些外部软件来完成任务。

第一次构建目标后，下一次执行 make 时，它会根据该目标所依赖的文件是否更新决定是否编译该目标，如果所依赖的文件没有更新且该目标又存在，那么它便不会构建该目标。这种特性非常有利于编译程序源代码。

任何一个 Linux 发行版中都默认自带这个 make 程序，所以不需要额外的安装工作，我们直接使用即可。

一个有关makefile的例子：
```makefile
CC = gcc #定义一个宏CC 等于gcc
CFLAGS = -c #定义一个宏 CFLAGS 等于-c
OBJS_FILE = file.o file1.o file2.o file3.o file4.o #定义一个宏
.PHONY : all everything #定义两个伪目标all、everything
all:everything #伪目标all依赖于伪目标everything
everything :$(OBJS_FILE) #伪目标everything依赖于OBJS_FILE，而OBJS_FILE是宏会被
#替换成file.o file1.o file2.o file3.o file4.o
%.o : %.c
   $(CC) $(CFLAGS) -o $@ $<
```
- makefile 中可以定义宏，方法是在一个字符串后跟一个“=”或者“:=”符号，引用宏时要用“$(宏名)”，宏最终会在宏出现的地方替换成相应的字符串，例如：$(CC) 会被替换成 gcc，$( OBJS_FILE) 会被替换成 file.o file1.o file2.o file3.o file4.o。
- .PHONY 在 makefile 中表示定义伪目标。所谓伪目标，就是它不代表一个真正的文件名，在执行 make 时可以指定这个目标来执行其所在规则定义的命令。但是伪目标可以依赖于另一个伪目标或者文件，例如：all 依赖于 everything，everything 最终依赖于 file.c file1.c file2.c file3.c file4.c。
- 通用规则：“%.o : %.c”。其中的“%”表示通配符，表示所有以“.o”结尾的文件依赖于所有以“.c”结尾的文件。
- 针对这些依赖关系，分别会执行：$(CC) $(CFLAGS) -o $@ $< 命令，当然最终会转换为：gcc –c –o xxxx.o xxxx.c，这里的“xxxx”表示一个具体的文件名。
### 编译

![img](https://static001.geekbang.org/resource/image/cb/34/cbd634cd5256e372bcbebd4b95f21b34.jpg?wh=4378*4923)


1. makefile进行make后产生三个文件：entry.asm(汇编程序文件) main.c vgastr.c
2. entry.asm经nasm汇编器汇编为entry.o(二进制文件)
3. main.c和vgastr.c经gcc编译为main.o和vgastr.o
4. 三个二进制文件经LD链接为HELLO OS.elf(Executable Linkable Format，二进制可执行文件的一种形式) 
5. HELLO OS.elf经**objcopy**命令格式转化为 HELLO OS.bin(objcopy 把一种目标文件中的内容复制到另一种类型的目标文件中)
### 安装HELLO OS
前面得到了 Hello OS.bin 文件，但是我们还要让 GRUB 能够找到它，才能在计算机启动时加载它，即---安装。

GRUB 在启动时会加载一个 **grub.cfg** 的文本文件，根据其中的内容执行相应的操作，其中一部分内容就是启动项。GRUB 首先会显示启动项到屏幕，然后让我们选择启动项，最后 GRUB 根据启动项对应的信息，加载 OS 文件到内存。

HELLO OS启动项：
```conf
menuentry 'HelloOS' {
     insmod part_msdos #GRUB加载分区模块识别分区
     insmod ext2 #GRUB加载ext文件系统模块识别ext文件系统
     set root='hd0,msdos4' #**注意**boot目录挂载的分区，这是我机器上的情况，命令df /boot/可查看
     multiboot2 /boot/HelloOS.bin #GRUB以multiboot2协议加载HelloOS.bin
     boot #GRUB启动HelloOS.bin
}
```
![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211202195453.png)
df /boot/后也可能是：
```
文件系统          1K-块    已用     可用      已用% 挂载点
/dev/sda4      48752308 8087584 38158536   18%    /
```
其中的“**sda4**”就是硬盘的第四个分区（硬件分区选择 MBR），但是 GRUB 的 **menuentry** 中不能写 sda4，而是要写“**hd0,msdos4**”，这是 GRUB 的命名方式，hd0 表示第一块硬盘，结合起来就是第一块硬盘的第四个分区。

**MBR**与**GPT**（[参考](https://baijiahao.baidu.com/s?id=1660593088582666423&wfr=spider&for=pc)）:
- **MBR**，它的全称是主引导记录(Master Boot Record)，它最早是在1983年的IBM PC DOS 2.0中提出的，这种模式下会在系统驱动器的一个特殊的位置建立一个用于保存操作系统BootLoader和驱动器逻辑分区的扇区。它只能最大支持2TB的磁盘，超过之后就无法识别，而在Windows下也只能创建最多4个分区。
- GPT，他是指的GUID分区表，也就是GUID意为全局唯一标识符，会生成一个唯一的识别码来进行引导创建，在功能上则没有MBR那么多的限制，支持2TB以上的磁盘，并且在Windows下支持最高128个分区的建立。
- 如果你的电脑硬件比较老旧的话，或者安装的系统版本也比较老旧的话，那就为了兼容性更好，选择MBR，而反之的话，就选择标准更新更加没有限制的GPT。

把上面启动项的代码插入到你的 Linux 机器上的 /boot/grub/grub.cfg 文件末尾，然后把 Hello OS.bin 文件复制到 /boot/ 目录下，一定**注意**这里是追加不是覆盖。最后重启计算机，你就可以看到 Hello OS 的启动选项了。选择 Hello OS，按下 Enter 键（或者重启按 ESC 键），这样就可以成功启动我们自己的 Hello OS 了。
### HELLO OS回顾
- 首先，我们了解了从按下 PC 机电源开关开始，PC 机的引导过程。它从 CPU 上电，到加载 BIOS 固件，再由 BIOS 固件对计算机进行自检和默认的初始化，并加载 GRUB 引导程序，最后由 GRUB 加载具体的操作系统。
- 其次，用汇编语言和 C 语言实现我们的 Hello OS。
    - 第一步，用汇编程序初始化 CPU 的寄存器、设置 CPU 的工作模式和栈，最重要的是加入了 GRUB 引导协议头；
    - 第二步，切换到 C 语言，用 C 语言写好了主函数和控制显卡输出的函数，其间还了解了显卡的一些工作细节。
- 最后，就是编译和安装 Hello OS 了。我们用了 make 工具编译整个代码，其实 make 会根据一些规则调用具体的 nasm、gcc、ld 等编译器，然后形成 Hello OS.bin 文件，你把这个文件写复制到 boot 分区，写好 GRUB 启动项，这样就好了。

[参考](https://time.geekbang.org/column/intro/100078401?tab=catalog)

## 设计
## 3.内核结构与设计
下面我们就先搞清楚内核之中有些什么东西，然后探讨一下怎么组织它们、用什么架构来组织、并对比成熟的架构，最后设计出我们想要的内核架构。

### 黑盒之中有什么

从抽象角度来看，内核就是计算机资源的管理者，当然管理资源是为了让应用使用资源。既然内核是资源的管理者，我们先来看看计算机中有哪些资源，然后通过资源的归纳，就能推导出内核这个大黑盒中应该有什么。

计算机中资源大致可以分为两类资源，一种是硬件资源，一种是软件资源。先来看看硬件资源有哪些，如下：1. 总线，负责连接各种其它设备，是其它设备工作的基础。2.CPU，即中央处理器，负责执行程序和处理数据运算。3. 内存，负责储存运行时的代码和数据。4. 硬盘，负责长久储存用户文件数据。5. 网卡，负责计算机与计算机之间的通信。6. 显卡，负责显示工作。7. 各种 I/O 设备，如显示器，打印机，键盘，鼠标等。

下面给出一幅经典的计算机内部结构图，如下：

![img](https://static001.geekbang.org/resource/image/28/14/28cc064d767d792071a789a5b4e7d714.jpg?wh=4367*2105)

而计算机中的软件资源，则可表示为计算机中的各种形式的数据。如各种文件、软件程序等。

内核作为硬件资源和软件资源的管理者，其内部组成在逻辑上大致如下：
1.管理 CPU，由于 CPU 是执行程序的，而内核把运行时的程序抽象成进程，所以又称为进程管理。
2.管理内存，由于程序和数据都要占用内存，内存是非常宝贵的资源，所以内核要非常小心地分配、释放内存。
3.管理硬盘，而硬盘主要存放用户数据，而内核把用户数据抽象成文件，即管理文件，文件需要合理地组织，方便用户查找和读写，所以形成了文件系统。
4.管理显卡，负责显示信息，而现在操作系统都是支持 GUI（图形用户接口）的，管理显卡自然而然地就成了内核中的图形系统。
5.管理网卡，网卡主要完成网络通信，网络通信需要各种通信协议，最后在内核中就形成了网络协议栈，又称网络组件。
6.管理各种 I/O 设备，我们经常把键盘、鼠标、打印机、显示器等统称为 I/O（输入输出）设备，在内核中抽象成 I/O 管理器。


内核除了这些必要组件之外，根据功能不同还有安全组件等，最值得一提的是，各种计算机硬件的性能不同，硬件型号不同，硬件种类不同，硬件厂商不同，内核要想管理和控制这些硬件就要编写对应的代码，通常这样的代码我们称之为驱动程序。硬件厂商就可以根据自己不同的硬件编写不同的驱动，加入到内核之中。以上我们已经大致知道了内核之中有哪些组件，但是另一个问题又出现了，即如何组织这些组件，让系统更加稳定和高效，这就需要我们从现有的一些经典内核结构里找灵感了。


### 宏内核结构
其实看这名字，就已经能猜到了，宏即大也，这种最简单适用，也是最早的一种内核结构。宏内核就是把以上诸如管理进程的代码、管理内存的代码、管理各种 I/O 设备的代码、文件系统的代码、图形系统代码以及其它功能模块的代码，把这些所有的代码经过编译，最后链接在一起，形成一个大的可执行程序。这个大程序里有实现支持这些功能的所有代码，向用户应用软件提供一些接口，这些接口就是常说的系统 API 函数。而这个大程序会在处理器的特权模式下运行，这个模式通常被称为宏内核模式。结构如下图所示。

![img](https://static001.geekbang.org/resource/image/eb/6b/eb8e9487475f960dccda0fd939999b6b.jpg?wh=4298*3328)



尽管图中一层一层的，这并不是它们有层次关系，仅仅表示它们链接在一起。为了理解宏内核的工作原理，我们来看一个例子，宏内核提供内存分配功能的服务过程，具体如下：

1. 应用程序调用内存分配的 API（应用程序接口）函数。
2. 处理器切换到特权模式，开始运行内核代码。
3. 内核里的内存管理代码按照特定的算法，分配一块内存。把分配的内存块的首地址，返回给内存分配的 API 函数。5. 内存分配的 API 函数返回，处理器开始运行用户模式下的应用程序，应用程序就得到了一块内存的首地址，并且可以使用这块内存了。








































































































































































































## 4.Linux内核架构？！




































































































































































































































## 硬件
## 5.CPU工作模式




































































































































































































































## 6.程序中的地址转化




































































































































































































































## 7.程序放在cache还是内存？




































































































































































































































## 同步原语
## 8.并发如何解决数据同步




































































































































































































































## 9.Linux自旋锁、信号量




































































































































































































































## 启动初始化
## 10~12.设置工作模式与环境




































































































































































































































## 13.第一个c函数：如何实现板级初始化




































































































































































































































## 14~15.Linux初始化




































































































































































































































## 内存
## 16~18.内存的划分、初始化、分配、释放




































































































































































































































## 19.管理内存对象




































































































































































































































## 20~21.虚拟内存如何表示、分配、释放



































































































































































































































## 22~23.Linux伙伴系统和SLAB如何分配内存



































































































































































































































## 进程
## 24.什么是进程？



































































































































































































































## 25~26.进程调度、等待与唤醒



































































































































































































































## 27.Linux如何实现的进程及其调度



































































































































































































































## 设备I/O  
## 28.表示设备类型与设备驱动



































































































































































































































## 29.在内核中注册设备



































































































































































































































## 30.设备如何处理内核I/O包



































































































































































































































## 31.Linux是如何获取设备信息的？



































































































































































































































## 文件系统
## 32.文件组织



































































































































































































































## 33.FS的格式化操作



































































































































































































































## 34.文件的6大操作



































































































































































































































## 35.Linux虚拟文件系统如何管理文件



































































































































































































































## 网络
## 36.如何全局观察网络数据流动



































































































































































































































## 37.网络数据在内核中如何流转



































































































































































































































## 38.操作系统的宏观网络架构



































































































































































































































## 39.Linux的socket实现与网络编程接口



































































































































































































































## 40.Linux详解socket的接口实现



































































































































































































































## 接口



































































































































































































































## 41.服务接口



































































































































































































































## 42.Linux如何实现系统API



































































































































































































































## 虚拟化
## 43.KVM是啥



































































































































































































































## 44.容器的实现



































































































































































































































## 45.ARM新宠：苹果M1芯片



































































































































































































































## 46.ARM最新编程架构模型剖析

[参考](https://time.geekbang.org/column/intro/100078401?tab=catalog)