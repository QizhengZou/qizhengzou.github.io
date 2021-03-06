# Mysql_base_14

# 故障恢复
## 数据库的故障类型及其影响
DBMS的运行方式：
- DBMS利用内存(主存)和外存(辅存)这样的存储体系来进行数据库管理
- 在内存中, 又将其分为程序数据(事务数据)和系统数据

事务：
- 事务是DBMS对数据库进行控制的基本逻辑单元。
- 事务：宏观上是由程序员设置的一条或多条SQL语句的一次执行；微观上是对数据元素的一系列基本操作，如读写等。需要提交和撤销。
- 数据元素：
    - 通常1数据元素 = 1磁盘块/内存页
    - 也可以更小(=1 记录)或更大(=1 关系)
- 事务具有四个特性：ACID特性
    - 原子性Atomicity
    - 一致性Consistency
    - 隔离性Isolation
    - 持久性Durability
- 故障恢复涉及到如何保证原子性和持久性

数据库的故障及其影响
- 事务故障
    - 某一个程序(事务)自身运行错误所引起的故障
    - 影响该程序(事务)本身
- 系统故障
    - 由于掉电、非正常关机等所引起的故障
    - 影响正在运行的事务以及数据库缓冲区, 数据库缓冲区将涉及正在运行和已经运行的事务
- 介质故障
    - 由于介质损坏等所引起的故障
    - 影响是全面的，既影响内存中的数据, 又影响介质中存储的数据

## 数据库故障恢复的宏观思路
数据库故障恢复
- 把DB由当前不正确状态恢复到已知为正确的某一状态。
- 需要保证事务的：
    - 原子性：事务的所有操作，要么全都执行，要么全都不执行。
    - 持久性：已提交的事务对数据库产生的影响是持久的，未提交的事务对数据库不应有影响。

事务故障的恢复
- 事务故障可通过重做事务(Redo)和撤消事务(Undo)来恢复。重做事务可保证已提交事务的持久性，而撤销事务则消除未提交事务的影响

系统故障恢复
- 运行日志(System Log)
    - 运行日志是DBMS维护的一个文件，该文件以流水方式记录了每一个事务对数据库的每一次操作及操作顺序
    - 运行日志直接写入介质存储上，会保持正确性
    - 当事务对数据库进行操作时：先写运行日志；写成功后，再与数据库缓冲区进行信息交换
- 系统故障可通过运行日志来恢复
    - 按照运行日志记录的事务操作顺序重做事务(当事务在发生故障时已正确结束) 或撤消事务(当事务在发生故障时未结束)
- 但故障恢复是需要时间的
    - 运行日志保留了若干天的记录，当发生系统故障时应从哪一个点开始恢复呢？
- DBMS在运行日志中定期的设置和更新检查点(checkpoint)
    - 检查点是这样的时刻: 在该时刻, DBMS强制使内存DB Buffer中的内容与介质DB中的内容保持一致，即将DB Buffer更新的所有内容写回DB中
    - 检查点表征了：在检查点之前内存中数据与介质中数据是保持一致的
- 系统故障的恢复
    - 检查点之前结束的事务不需要恢复(已经写回DB)
    - 检查点之后结束或发生的事务需 要依据运行日志进行恢复(不能确定是否写回DB): 故障点前结束的重做, 故障点时刻未结束的撤消

介质故障恢复
- 副本(Copy)
    - 在某一时刻，对数据库在其他介质存储上产生的另一份等同记录
    - 用副本替换被损坏的数据库
- 介质故障的恢复
    - 用副本替换被破坏的数据库
    - 由于介质故障影响全面, 在用副本恢复后还需要依据运行日志进行恢复
- 如何确定备份的时刻: 转储点
    - 过频，影响系统工作效率；过疏，会造成运行日志过大，也影响系统运行性能
    - 备份转储周期与运行日志的大小密切相关，应注意防止衔接不畅而引起的漏洞

小结：
- 三种类型故障：事务故障、系统故障和介质故障
- 三种恢复手段: 事务的撤消与重做, 运行日志和备份
- 两个重要时刻：检查点和转储点
## 什么是日志
事务涉及到的：
- 数据库通常由元素构成
    - 通常，1 元素 = 1 磁盘块 = 1 内存页/块
    - 可以更小，= 1 记录 或更大 = 1 关系
- 每个事务都会读/写某些元素
    - READ(X,t)：将元素X读到事务的局部变量t中
    - WRITE(X,t)：将事务局部变量t写回元素X 
    - INPUT(X)：将元素X从磁盘读入到内存缓冲区中
    - OUTPUT(X)：将元素X写回到磁盘中
- 每个事务都以提交或者撤销结束
    - COMMIT：事务提交
    - ABORT：事务撤销
- DBMS需要保证事务的：
    - 持久性：已提交的事务对数据库产生的影响是持久的，未提交的事务对数据库不应有影响。
    - 原子性：事务的所有操作，要么全都执行，要么全都不执行

不同的缓冲区策略会影响事务的持久性：
- Force：内存中的数据最晚在commit的时候写入磁盘。
- No steal：不允许在事务commit之前把内存中的数据写入磁盘。
- No force：内存中的数据可以一直保留，在commit之后过一段时间再写入磁盘。(此时在系统崩溃的时候可能还没写入到磁盘，需要Redo)。-- 灵活 
- Steal：允许在事务commit之前把内存中的数据写入磁盘。(此时若系统在commit之前崩溃时，已经有数据写入到磁盘了，要恢复到崩溃前的状态，需要Undo)。--灵活

事务故障会影响事务的原子性

怎样记录日志：
- 日志：
    - 一个包含日志记录的只能追加的顺序文件, 不同事务的日志记录交错存储，按发生时间存储
    - 发生系统故障时，使用日志进行恢复:
        - 故障时已提交的事务，重做(Redo)
        - 故障时未提交的事务，撤销(Undo)
    - 日志记录的信息
        - < Start T>，表示事务T已经开始
        - < Commit T>，表示事务T成功完成
        - < Abort T>，事务T未成功，被中止
        - < T, X, v1> 或者 < T, X, v2> 或者 < T, X, v1,v2>表示事务T改变了数据库元素X，X原来的值为v1(X的旧值)，X新的值为v2
    - 三种日志: Undo型日志，Redo型日志，Undo/Redo型日志
## Undo型日志及其故障恢复
Undo型日志
- 对于任一事务T，按下列顺序向磁盘输出T的日志信息：
    - 首先，< T, X, v>被写到日志中
    - 其次，OUTPUT(X)
    - 最后，< COMMIT T>或< ABORT T>被写到日志中
- 注意：Undo型日志仅保留旧值。< T, X, v>，v为X原来的值(X的旧值)
- Undo型日志：“将事务改变的所有数据写到磁盘前不能提交该事务

利用undo型日志进行恢复
- 首先，确定每一个事务是否已完成?
    - < START T>….< COMMIT T>…. = yes
    - < START T>….< ABORT T>……. = no(已结束，但未完成)
    - < START T>……………………… = no
- 然后，从日志的尾部开始按日志记录的反序，处理每一日志记录，撤销未完成事务的所有修改
    - < COMMIT T>:标记T已完成
    - < ABORT T>: 标记T已结束但未完成
    - < T,X,v>: 如果T未完成，则将X=v写回磁盘；否则跳过；
    - < START T>: 跳过

检查点
- 静止检查点：周期性地对日志设置检查点
    - 停止接受新的事务, 等到所有当前活跃事务提交或终止，并在日志中写入了COMMIT或ABORT记录后
    - 将日志刷新到磁盘，写入日志记录< CKPT>，并再次刷新日志
- 非静止检查点
    - 在设置检查点时不必关闭系统，允许新事务进入
    - 写入一条< START CKPT(T1,…,Tk)>其中T1,…,Tk 是所有活跃的未结束的事务
    - 继续正常的操作，直到T1,…,Tk都完成时，写入< END CKPT
## Redo型日志及其故障恢复
Redo型日志
- Undo型日志的问题“将事务改变的所有数据写到磁盘前不能
提交该事务”—如何解决?
- 对于任一事务T，按下列顺序向磁盘输出T的日志信息：
    - 首先，< T, X, v>被写到日志中
    - 其次，< COMMIT T>被写到日志中
    - 最后，OUTPUT(X)
- 注意：redo型日志保留新值。< T, X, v>，v为X更新后的值(X的新值)
- 注意：与undo型的差别，在后两步，先写提交记录后输出，还是先输
出，再写提交记录。

利用redo日志进行恢复
- 确定每一个事务是否已完成?
    - < START T>….< COMMIT T>…. = yes
    - < START T>….< ABORT T>…….= no(已结束，但未完成)
    - < START T>………………………= no
- 从日志的起始位置开始按日志记录的正序处理每一日志记录，重做已提交事务的所有修改：
    - < COMMIT T>:标记T已完成
    - < ABORT T>:标记T已结束但未完成
    - < T,X,v>: 如果T已完成，则将X=v写回磁盘；否则跳过；
    - < START T>: 跳过

检查点
- 非静止检查点
    - 在进行检查点设置时不必关闭系统，允许新事务进入
    - 写入一条< START CKPT(T1,…,Tk)>其中T1,…,Tk 是所有活跃的未结束的事务
    - 将所有已提交的事务写回磁盘，
    - 继续正常的操作，直到T1,…,Tk都完成时，写入< END CKPT>
## Undo/Redo结合型日志及其故障恢复
Redo型日志与Undo型日志的比较:
- Undo型日志:
    - OUTPUT必须先做。
    - 如果< COMMIT T>可见, T确定地已将所有其数据写回磁盘，因此不必重做 –-- 但可能引起性能下降(因可能频繁地写磁盘)
- Redo型日志：
    - OUTPUT必须后做。
    - 如果< COMMIT T>不可见, T确定地没有将其任何数据写回到磁盘，因此无需撤销 –-- 但灵活性差(数据必须在Commit后才可见)
- 如更喜欢灵活性 -- Undo/Redo型日志

Undo/Redo型日志
- 对于任一事务T，按下列顺序向磁盘输出T的日志信息：
    - 第(1)步，< T, X, u, v>被写到日志中
    - 第(2)or(3)步，< COMMIT T>被写到日志中
    - 第(3)or(2)步，OUTPUT(X)
- 注意：undo/redo型日志既保留新值v，也保留旧值u。
- 注意：与undo型和redo型的差别，在后两步。Redo型是先写提交记录 后输出；undo型是先输出，再写提交记录；undo/redo型则无所谓谁先谁 后，只要保证< T,X,u,v>被先于OUTPUT写完即可。

利用undo/Redo型日志进行恢复
- 首先，确定每一个事务是否已完成?
    - < START T>….< COMMIT T>…. = yes
    - < START T>….< ABORT T>……. = no(已结束，但未完成)
    - < START T>……………………… = no
- 自前向后地，按日志记录的正序，重做所有已提交的事务；自 后向前，按日志记录的反序，撤销所有未完成事务的所有修改。
    - < COMMIT T>:标记T已完成
    - < ABORT T>:标记T已结束但未完成
    - < T,X,u,v>:如果T未完成，则将X=u写回磁盘；否则将x=v写回磁盘；
    - < START T>:跳过



