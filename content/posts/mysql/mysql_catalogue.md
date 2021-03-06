---
title: "Mysql_catalogue"
date: 2021-11-08T16:43:15+08:00
lastmod: 2021-11-09
tags: [catalogue, mysql]
categories: [Catalogue, School courses]
slug: Mysql_catalogue
draft: true
---
>base笔记来自哈工大深圳数据库理论课授课老师：刘圣鑫
* [base_01](https://qizhengzou.github.io/2021/mysql-conceptual-structure/)（数据库概念结构）
    * 基本概念
        * 什么是数据库及为什么要学数据库？
        * 数据库(信息库)
        * 数据库系统(工作环境)
        * 数据库管理系统(软件系统)
    * 数据库工作者的分类及要求
    * 标准结构
        * DBMS管理数据的三个层次：(外部)视图、全局视图、内部视图
        * 模式的概念：外模式、(概念)模式、内模式
        * 数据模型与模式
    * 简要发展史及发展趋势
* [base_02](https://qizhengzou.github.io/2021/mysql-relational-model-and-relational-operation./)（关系模型与关系运算）
    * 关系模型与关系运算简述
        * 关系模型的提出与作用
        * 关系模型与关系运算概览
    * 关系与关系模型
        * 关系
        * 关系模型
    * 关系代数运算
* [base_03](https://qizhengzou.github.io/2021/sql/)（sql语言）
    - SQL语言概述
        - SQL语言提出和发展
        - SQL语言概览
    - 简单的SQL-DDL/DML: 创建数据库
    - SQL-DML之查询Select
        - 基本的检索操作
        - 多表联合检索
        - 子查询
        - 结果计算与聚集函数
        - 分组查询与分组过滤
        - 并、交、差的处理
        - 空值处理
        - 内连接、外连接
        - SQL的完整语法
    - SQL-DML之更新Insert/Update/Delete
    - SQL-视图及DDL的进一步介绍
        - 视图的定义与使用
        - 视图更新问题
        - SQL-DDL的进一步介绍: 撤消语句的使用
- [base_04](https://qizhengzou.github.io/2021/database-management-and-maintenance/)（数据库管理与维护）
    - 数据库完整性与安全性控制
        - 数据库完整性的概念及分类
        - SQL-DDL中关于完整性的命令
        - 数据库安全性的概念
        - MySQL 有关权限的表都有哪几个？
        - SQL-DCL中关于安全性的命令
        - 数据字典/系统目录和模式
    - 小结
- [base_05](https://qizhengzou.github.io/2021/ideas-and-methods-of-data-modeling/)（数据库建模思想与方法）
    - 为什么要数据建模和数据库设计?
    - E-R模型--数据建模之基本思想
    - E-R模型的两种图示化表达方法
- [base_06](https://qizhengzou.github.io/2021/database-design/)（数据库设计）
    - 强弱实体的个人理解
    - 数据库设计过程概述
    - 数据库设计过程之需求分析
    - 数据库设计过程之概念数据库设计
    - 数据库设计过程之逻辑数据库设计
        - E-R图向关系模式的转换
    - 数据库设计过程之物理数据库设计
- [base_07](https://qizhengzou.github.io/2021/functional-dependence-and-its-axiom-theorem/)（函数依赖及其公理定理）
    - 函数依赖
    - 完全函数依赖与传递函数依赖
    - 关于函数依赖的公理和定理
    - 函数依赖集的最小覆盖
    - 关系模式设计之规范形式
        - 关系的第1NF和第2NF
        - 关系的第3NF和Boyce-Codd NF
        - 多值依赖及其公理定理
        - 关系的第4NF
- [base_08](https://qizhengzou.github.io/2021/problems-in-schema-decomposition/)（模式分解存在的问题）
    - 模式分解存在什么问题
    - 无损连接分解及其检验算法
    - 保持依赖分解及其检验算法
    - 关系模式无损连接或保持依赖的分解算法
    - 数据库设计需要知道的
    - 总结
- [base_09](https://qizhengzou.github.io/2021/database-physical-storage/)（数据库物理存储）
    - 基础回顾-计算机系统的存储体系
        - 数据库的存储与检索问题
        - 什么是存储体系
        - 不同层次存储的访问时间上的差异
        - 操作系统如何管理磁盘和数据
        - 操作系统对内存-缓冲区的管理
    - 磁盘的结构与特性
    - DBMS数据存储与查询实现的基本思想
    - 数据库之表和记录与磁盘块的映射
    - 数据库之文件组织方法
    - Oracle数据库的数据组织
- [base_10](https://qizhengzou.github.io/2021/database-index/)（数据库索引）
    - 为什么需要索引与什么是索引
    - 索引的简单分类
    - B+树索引
    - 散列索引
- [base_11](https://qizhengzou.github.io/2021/database-query-implementation-algorithm/)（数据库查询实现算法）
    - 数据库查询实现算法-I
        - 数据库查询实现算法概述
        - 以连接操作为例看逻辑实现算法与物理实现算法
        - 利用迭代器构造查询实现算法
        - 几个关系操作的一趟扫描算法
        - 基于索引的算法
        - 总结
    - 数据库查询实现算法-II
        - 为什么需要两趟算法? 
        - 两阶段多路归并排序TPMMS
        - 基于排序的两趟扫描算法
        - 基于散列的两趟扫描算法
        - 总结
- [base_12](https://qizhengzou.github.io/2021/query-optimization/)（数据库查询优化）
    - 为什么要及什么是查询优化?
    - 查询优化的基本思路
    - 逻辑查询优化
    - 物理查询优化
- [base_13](https://qizhengzou.github.io/2021/database-transaction-processing-technology/)（事务处理）
    - 为什么需要并发控制
    - 事务调度及可串行性
    - 基于封锁的并发控制方法
    - 基于时间戳的并发控制方法
    - 基于有效性确认的并发控制方法
    - 总结
- [base_14](https://qizhengzou.github.io/2021/fault-recovery/)（故障恢复）
    - 数据库的故障类型及其影响
    - 数据库故障恢复的宏观思路
    - 什么是日志
    - Undo型日志及其故障恢复
    - Redo型日志及其故障恢复
    - Undo/Redo结合型日志及其故障恢复
- [advanced_01](https://qizhengzou.github.io/2021/a-query-sentence/)（详解MYSQL查询语句）
    - 连接器
        - 长连接和短连接
        - 查询缓存
        - 分析器
        - 优化器
        - 执行器
- [advanced_02](https://qizhengzou.github.io/2021/an-update-sentence/)（详解MYSQL更新语句）
    - 重要的日志模块redo log
    - 重要的日志模块
    - 两阶段提交
    -  一天一备份和一周一备份的区别
    

```sql
DROP TABLE IF EXISTS `policy`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `policy` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `instanceID` varchar(32) DEFAULT NULL,
  `name` varchar(45) NOT NULL,
  `username` varchar(255) NOT NULL,
  `policyShadow` longtext DEFAULT NULL,
  `extendShadow` longtext DEFAULT NULL,
  `createdAt` timestamp NOT NULL DEFAULT current_timestamp(),
  `updatedAt` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  PRIMARY KEY (`id`),
  UNIQUE KEY `instanceID_UNIQUE` (`instanceID`),
  KEY `fk_policy_user_idx` (`username`),
  CONSTRAINT `fk_policy_user` FOREIGN KEY (`username`) REFERENCES `user` (`name`) ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB AUTO_INCREMENT=47 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;
```
对于外键，
完整语法：
[CONSTRAINT symbol] FOREIGN KEY [id] (index_col_name, …)
REFERENCES tbl_name (index_col_name, …)
[ON DELETE {RESTRICT | CASCADE | SET NULL | NO ACTION}]
[ON UPDATE {RESTRICT | CASCADE | SET NULL | NO ACTION}]
参数说明：
CASCADE
在父表上update/delete记录时，同步update/delete掉子表的匹配记录 

SET NULL
在父表上update/delete记录时，将子表上匹配记录的列设为null (要**注意**子表的外键列不能为not null)  

NO ACTION
如果子表中有匹配的记录,则不允许对父表对应候选键进行update/delete操作  

RESTRICT
同no action, 都是立即检查外键约束

SET NULL
父表有变更时,子表将外键列设置成一个默认的值 但Innodb不能识别
>参考链接：https://www.cnblogs.com/yzuzhang/p/5174720.html
