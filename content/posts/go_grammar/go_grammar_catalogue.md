---
title: "Go_grammar_catalogue"
date: 2022-01-06T09:17:19+08:00
lastmod: 2022-02-16
tags: [catalogue, go grammar]
categories: [Catalogue, Go]
slug: Go_Catalogue
draft: false
---
> 参考学习go语言中文网、C语言中文网、golang官方文档等

- [go_base_01](https://qizhengzou.github.io/go_base_01/)
    - go起源
    - 环境相关
    - 主要特征
    - 内置类型与函数
    - init & main以及Go包的初始化顺序
    - go命令
        - go env
        - go run命令
        - go get
        - go build命令
        - go install
        - go clean命令
        - go doc命令
        - go test命令
        - go list命令
        - go fix
        - go vet
        - go tool pprof命令
        - go modules
    - 运算符
    - 下划线
    - 格式占位符%……
    - 变量和常量
    - 基本类型
    - string：
    - 数组
    - 切片
        - 切片底层实现
        - 创建切片
        - 切片拷贝
    - 指针
    - map
        - map实现原理
        - go中map的实现原理
        - map与工厂模式
    - set
    - 结构体
        - 类型别名和自定义类型
        - 结构体
            - 方法和接收者
    - go项目的标准布局演进
    - go应用构建模式的演进
    - 参考
- [go_base_02](https://qizhengzou.github.io/go_base_02/)
    - if
    - switch
        - Type Switch
    - select
        - 基本使用
        - 典型用法
    - for
    - range
    - Goto Break Continue
- [go_base_03](https://qizhengzou.github.io/go_base_03/)
    - 函数定义
    - 参数
        - 不定参
    - 返回值
    - 匿名函数
    - 闭包、递归
        - 闭包
        - go递归函数
    - 延迟调用（defer）
        - defer陷阱
    - 异常处理，错误处理
    - 单元测试
        - go test工具
        - 测试函数
        - 测试组
        - 子测试
        - 测试覆盖率
        - 基准测试
        - x性能比较函数
        - 重置时间
        - 并行测试
        - Setup与TearDown
        - 示例函数
        - func ToUpper
    - 压力测试
        - Go怎么写测试用例
        - 如何编写测试用例
        - 如何编写压力测试
        - 小结
    - BDD
- [go_base_04](https://qizhengzou.github.io/go_base_04/)
    - 方法定义
    - 匿名字段
    - 方法集
    - 表达式
    - 自定义error
        - 抛异常和处理异常
            - 系统抛
            - 返回异常
            - 自定义error
- [go_base_05](https://qizhengzou.github.io/go_base_05/)
    - 匿名字段
    - 接口
        - 接口
        - 类型与接口的关系
        - 空接口
        - 空接口的应用
        - 类型断言
- [go_base_06](https://qizhengzou.github.io/go_base_06/)
    - 互联网协议介绍
        - 互联网分层模型
    - socket编程
    - http编程
    - WebSocket编程
- [go_base_07](https://qizhengzou.github.io/go_base_07/)
    - 并发介绍
    - Goroutine
    - runtime包
    - Channel
    - Goroutine池
    - 定时器
    - select
    - 并发安全和锁
    - Sync
    - 原子操作
    - GMP原理和调度
    - 爬虫小案例
