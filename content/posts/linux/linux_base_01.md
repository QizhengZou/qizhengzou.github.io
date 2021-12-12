---
title: "Linux_base_01"
date: 2021-11-08T15:34:58+08:00
lastmod: 2021-11-08
tags: [linux]
categories: [advanced learning]
slug: linux
draft: false
---
## 1. Linux 背景知识
Linux 有两种含义：
* 一种是 Linus 编写的开源操作系统的内核
* 另一种是广义的操作系统

执行环境
* 云主机
* 虚拟机（较推荐）
* 无数据的 PC（不推荐多系统混跑）

linux版本：   
* 内核版本
    * 内核版本分为三个部分 
        * 主版本号、次版本号、末版本号
    * 次版本号是奇数为开发版，偶数为稳定版

* 发行版本（因为linux是开源的）5种
    * Redhat Enterprise Linux:RedHat公司发行，软件经过专业人员的测试，非常稳定，但是需要付费。
    * Fedora发行版本，也是RadHat发行，组建一个社区免费提供这个操作系统，软件较新，但未经过RedHat的专业测试，稳定性较差。
    * centos，是基于Enterprise Linux 的源代码经行编译的，把RedHad的商标和字样去掉了，故免费。
    * Debian和Ubuntu 不是字符桌面，有图形界面。
## 2. 系统操作
### 帮助命令man help info
* manual: man ls;man 章节号 man；man -a passwd
* help: 内部命令(命令解释器shell自带的命令 type cd查看cd命令类型)：help cd 外部命令：ls --help
* info：比help 更详细，可以作为help的补充，全英文。
### linux文件管理
一切皆文件
windows里有注册表，资源管理器等组件，控制linux自身的统统都是文件，linux文件管理非常重要
#### 文件查看
tab可以进行目录补全
* pwd
* cd
    * cd - 回到上次目录
    * cd ..(/)回到上一级目录
    * cd /path/to/…. 绝对路径
    * cd ./path/to/… 相对路径（./可以省略）
* ls
    * -l长格式显示（显示大小单位为Mb时可用-lh）
    * -a显示隐藏文件
    * -r逆序显示
    * -t时间顺序显示
    * -R递归显示
#### 目录文件的创建和删除
只列出了少量命令
* mkdir
    * mkdir a b c
    * mkdir /a/b /a目录已存在
    * mkdir -p /a/b/c/d 无需a b c等目录存在
    * mkdir a b c -p  忽略已存在a b c 的报错
* rmdir 删除空目录，目录下有空目录也不行，体现了“一切皆文件”
* rm
    * -r -f（/rf） 可以删除非空目录，不去进行提示删除目录，具有一定危险性 eg: rm -r -f / a 不小心在/a之间加空格会导致删除所有目录且不提示
    * -i 删除前逐一询问确认
    * -f 即使原档案属性设为唯读，亦直接删除，无需逐一确认
    * -r 将目录及以下之档案亦逐一删除
* cp 
    * -r 复制⽬录
    * -p 保留访问权限、修改时间
    * -a 等同于 -dpR（保留权限、属组、修改时间）
    * -v 显示复制过程
* mv
    * mv /a /b将a改名为b（可与移动同时进行），在linux底层操作即进行了一个移动
* 更多命令见[命令大全](https://www.runoob.com/linux/linux-command-manual.html)
#### 通配符（通用的匹配符号）
是shell内建的符号，用于多个相似的文件进行操作  
* *匹配任何字符串   
* ？匹配单个字符   
* [abc]匹配abc任意一个字符  
* [a-z]匹配一个范围   
* [!xyz]或[^xyz] 不匹配   
#### 文件操作
#### 文本内容查看
* cat ⽂本内容显示到终端
* head 查看⽂件开头（默认10行，-5可显示5行）
* tail 查看⽂件结尾（行数同head）(-f可进行跟踪)
* 常⽤参数 -f ⽂件内容更新后，显示信息同步更新
* wc 统计⽂件内容信息（-l查看行数）
* more 分行显示 （空格继续显示）
* less

more和less:
less:由于more不能后退，就取more的反义词less加上后退功能
所以Linux里流传着这样一句话："less is more".
总结下more 和 less的区别:
* less可以按键盘上下方向键显示上下内容,more不能这样
* less不必读整个文件，加载速度会比more更快
* less退出后shell不会留下刚显示的内容,而more退出后会在shell上留下刚显示的内容
#### 打包压缩解压
* 最早的 Linux 备份介质是磁带，使⽤的命令是 tar
* 可以打包后的磁带⽂件进⾏压缩储存，压缩的命令是 gzip 和 bzip2
* 经常使⽤的扩展名是 .tar.gz .tar.bz2 .tgz
* tar 打包命令
    * c 打包(cf  .tar)
    * x 解包
    * f 指定操作类型为⽂件
    * 可以使⽤ gzip 和 bzip2 命令单独操作通常和 tar 命令配合操作
    * -z gzip 格式压缩和解压缩(czf  .tar.gz)
    * -j bzip2 格式压缩和解压缩(cjf  .tar.bz2)，压缩比例更高，执行时间更慢。    
#### vim的四种模式
vim是一个vi向上兼容的文本编辑器
##### 正常模式
正常模式下输入：
* i:进入插入模式光标位置不动
* I:进入插入模式光标移到行首
* a:进入插入模式光标移到下一位
* A:进入插入模式光标移到行末
* o:进入插入模式光标在原来那一行的下一行，另起一行
* O:进入插入模式光标在原来那一行的上一行，另起一行
* ::进入命令模式
* hjkl:正常模式下光标上下左右移动
* 复制剪切粘贴输入错误进行重做
* y:
    * yy:光标在某一行输入n(不输入时默认为1，作为从当前行开始要复制的行数) yy，粘贴用p
    * y$:复制当前字符到行末的字符
* d:剪切
    * dd
    * d$
* u:撤销
* ctrl+r:重做
* x:单个字符删除
* r:替换当前字符
* n(你要移动光标到哪一行)+G
* g到第一行
* G到最后一行
* ^到当前行的开头
* $到当前行的结尾

##### 命令模式
文件的保存、退出、查找、替换，在正常模式下输入：
* w /test.txt 将文本保存到新文件，w直接保存到原始文件
* wq保存并推出
* q!不保存退出
* ！执行linux命令，有时候在打开vim的同时需要临时执行一条命令，eg:!ifconfig(查看ip地址)
* /查找eg:/x+enter光标自动移到第一个x,按n可以移动到下一个x，按N可以移到上一个x
* s/old/new 在光标所在行，将第一个旧的字符替换成新的字符
* %s/old/new 所有行，将第一个旧的字符替换成新的字符
* %s/old/new/g 所有行，将所有旧的字符替换成新的字符、
* n,m+前三个命令是在[n,m]行进行替换，左闭右闭
* set nohlsearch去除高亮显示
* set nu:显示行数
* set nonu:不显示行数
修改vim配置在/etc/vimrc文件里操作   
比如在最后一行添加set nu则以后每次打开文件都会显示行号

##### 可视模式
用于对文件进行重复的大量操作可以一次性执行完成
* v字符可视模式
* V行可视模式
* ctrl+v块可视模式
* 配合d和I可以进行块的便利操作
    * eg:进入块可视模式，用hjkl选择一个块，I可以在块头插入比如abc再按两次esc可见每行都被插入了abc；选中一个块，按d直接删除块


## 3. 服务管理

## 4. Shell

## 5. 文本操作

## 6. 服务篇

## 7. 实战
