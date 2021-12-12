---
title: "Go_base_01"
date: 2021-11-09T20:55:59+08:00
lastmod: 2021-11-09
tags: [go]
categories: [go]
slug: base
draft: true
---
## 主要特征
1. 自动立即回收。(自带GC)
2. 更丰富的内置类型。
3. 函数多返回值。
4. 错误处理。
5. 匿名函数和闭包。
6. 类型和接口。
7. 并发编程。
8. 反射。
9. 语言交互性。

Go的函数、变量、常量、自定义类型、包(package)的命名方式遵循以下规则：
- 1）首字符可以是任意的Unicode字符或者下划线
- 2）剩余字符可以是Unicode字符、下划线、数字
- 3）字符长度不限

25关键字：
```
    break        default      func         interface    select
    case         defer        go           map          struct
    chan         else         goto         package      switch
    const        fallthrough  if           range        type
    continue     for          import       return       var
```
37个保留字：
```
    Constants:    true  false  iota  nil

    Types:    int  int8  int16  int32  int64  
              uint  uint8  uint16  uint32  uint64  uintptr
              float32  float64  complex128  complex64
              bool  byte  rune  string  error

    Functions:   make  len  cap  new  append  copy  close  delete
                 complex  real  imag
                 panic  recover
```
## 参考
- https://cloud.tencent.com/developer/article/1526095