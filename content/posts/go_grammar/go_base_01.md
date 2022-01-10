---
title: "Go_base_01"
date: 2021-11-09T20:55:59+08:00
lastmod: 2021-11-09
tags: [go grammar]
categories: [go]
slug: go_base_01
draft: false
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
- 1）首字符可以是任意的Unicode字符（一个字符两个字节，表示包括了每种语言）或者下划线
- 2）剩余字符可以是Unicode字符、下划线、数字
- 3）字符长度不限

25关键字：
```go
    break        default      func         interface    select
    case         defer        go           map          struct
    chan         else         goto         package      switch
    const        fallthrough  if           range        type
    continue     for          import       return       var
```
- select和switch:select只能应用于channel的操作，既可以用于channel的数据接收，也可以用于channel的数据发送。如果select的多个分支都满足条件，则会随机的选取其中一个满足条件的分支。而switch用于一般的分支判断，顺序执行。
    - 
    ```go
    package main
    import "time"
    import "fmt"
    func main() {
        c1 := make(chan string)
        c2 := make(chan string)
        go func() {
            time.Sleep(time.Second * 1)
            c1 <- "one"
        }()
        go func() {
            time.Sleep(time.Second * 2)
            c2 <- "two"
        }()
        for i := 0; i < 2; i++ {
            select {
            case msg1 := <-c1:
                fmt.Println("received", msg1)
            case msg2 := <-c2:
                fmt.Println("received", msg2)
            }
        }
    }
    ```

- fallthrough:可以使用fallthrough强制执行该case执行完下一条case代码，fallthrough不会判断下一条case的判断结果是否为true。
37个保留字：
```go
    Constants:    true  false  iota  nil

    Types:    int  int8  int16  int32  int64  
              uint  uint8  uint16  uint32  uint64  uintptr
              float32  float64  complex128  complex64
              bool  byte  rune  string  error

    Functions:   make  len  cap  new  append  copy  close  delete
                 complex  real  imag
                 panic  recover
```
- new&make
    - func new(Type) *Type
    - func make(t Type, size ...IntegerType) Type

go声明：var,const,type,func

## 内置类型与函数
值类型：
```go
bool
int(32 or 64), int8, int16, int32, int64
uint(32 or 64), uint8(byte), uint16, uint32, uint64
float32, float64
string
complex64, complex128
array
```
**引用类型**（声明的同时需要分配内存空间，不然会引发panic，分配内存可以用new或者make）：
```go
slice
map
chan
```
内置函数（无需导入即可使用）:
```go
append          -- 用来追加元素到数组、slice中,返回修改后的数组、slice
close           -- 主要用来关闭channel
delete            -- 从map中删除key对应的value
panic            -- 停止常规的goroutine  （panic和recover：用来做错误处理）
recover         -- 允许程序定义goroutine的panic动作
real            -- 返回complex的实部   （complex、real imag：用于创建和操作复数）
imag            -- 返回complex的虚部
make            -- 用来分配内存，返回Type本身(只能应用于slice, map, channel) make 函数允许在运行期动态指定数组长度，绕开了数组类型必须使用编译期常量的限制。
new                -- 用来分配内存，主要用来分配值类型，比如int、struct。返回指向Type的指针
cap                -- capacity是容量的意思，用于返回某个类型的最大容量（只能用于切片和 map）
copy            -- 用于复制和连接slice，返回复制的数目
len                -- 用来求长度，比如string、array、slice、map、channel ，返回长度
print、println     -- 底层打印函数，在部署环境中建议使用 fmt 包
```
内直接口error：
```go
type error interface { //只要实现了Error()函数，返回值为String的都实现了err接口（鸭子类型）
    Error()    String
}
```
## init & main
go语言中init函数用于包(package)的初始化，该函数是go语言的一个重要特性。有下面的特征：
1. init函数是用于程序执行前做包的初始化的函数，比如初始化包里的变量等
2.  每个包可以拥有多个init函数
3. 包的每个源文件也可以拥有多个init函数
4. 同一个包中多个init函数的执行顺序go语言没有明确的定义(说明)
5. 不同包的init函数按照包导入的依赖关系决定该初始化函数的执行顺序
6. init函数不能被其他函数调用，而是在main函数执行之前，自动被调用

Go语言程序的默认入口函数(主函数)：func main(){
    ……
}

init函数和main函数的异同：
- 同
    - 两个函数在定义时不能有任何的参数和返回值，且Go程序自动调用。
- 异
    - init可以应用于任意包中，且可以重复定义多个。
    - main函数只能用于main包中，且只能定义一个。

执行顺序：
- 对同一个go文件的init()调用顺序是从上到下的。
- 对同一个package中不同文件是按**文件名字符串比较“从小到大”顺序**调用各文件中的init()函数。
- 对于不同的package，如果不相互依赖的话，按照main包中”先import的后调用”的顺序调用其包中的init()，如果package存在依赖，则先调用最早被依赖的package中的init()，最后调用main函数。
- 如果init函数中使用了println()或者print()你会发现在执行过程中这两个不会按照你想象中的顺序执行。这两个函数官方只推荐在测试环境中使用，对于正式环境不要使用。
## go命令
```
PS D:\Blog\qizhengzou.github.io-blog\content\posts> go
Go is a tool for managing Go source code.

Usage:

        go <command> [arguments]

The commands are:

        bug         start a bug report
        build       compile packages and dependencies       
        clean       remove object files and cached files    
        doc         show documentation for package or symbol
        env         print Go environment information        
        fix         update packages to use new APIs
        fmt         gofmt (reformat) package sources        
        generate    generate Go files by processing source  
        get         add dependencies to current module and install them
        install     compile and install packages and dependencies
        list        list packages or modules
        mod         module maintenance
        run         compile and run Go program
        test        test packages
        tool        run specified go tool
        version     print Go version
        vet         report likely mistakes in packages

Use "go help <command>" for more information about a command.

Additional help topics:

        buildconstraint build constraints
        buildmode       build modes
        c               calling between Go and C
        cache           build and test caching
        environment     environment variables
        filetype        file types
        go.mod          the go.mod file
        gopath          GOPATH environment variable
        gopath-get      legacy GOPATH go get
        goproxy         module proxy protocol
        importpath      import path syntax
        modules         modules, module versions, and more
        module-get      module-aware go get
        module-auth     module authentication using go.sum
        packages        package lists and patterns
        private         configuration for downloading non-public code
        testflag        testing flags
        testfunc        testing functions
        vcs             controlling version control with GOVCS

Use "go help <topic>" for more information about that topic.
```

go env用于打印Go语言的环境信息。

go run命令可以编译并运行命令源码文件。

go get可以根据要求和实际情况从互联网上下载或更新指定的代码包及其依赖包，并对它们进行编译和安装。

go build命令用于编译我们指定的源码文件或代码包以及它们的依赖包。

go install用于编译并安装指定的代码包及它们的依赖包。

go clean命令会删除掉执行其它命令时产生的一些文件和目录。

go doc命令可以打印附于Go语言程序实体上的文档。我们可以通过把程序实体的标识符作为该命令的参数来达到查看其文档的目的。

go test命令用于对Go语言编写的程序进行测试。

go list命令的作用是列出指定的代码包的信息。

go fix会把指定代码包的所有Go语言源码文件中的旧版本代码修正为新版本的代码。

go vet是一个用于检查Go语言源码中静态错误的简单工具。

go tool pprof命令来交互式的访问概要文件的内容。

go modules 是 golang 1.11 新加的特性
- go
    - download
        - download modules to local cache(下载依赖包)
    - edit
        - edit go.mod from tools or scripts（编辑go.mod）
    - graph
        - print module requirement graph (打印模块依赖图)
    - init
        - initialize new module in current directory（在当前目录初始化mod）
    - tidy
        - add missing and remove unused modules(拉取缺少的模块，移除不用的模块)
    - vendor
        - make vendored copy of dependencies(将依赖复制到vendor下)
    - verify
        - verify dependencies have expected content (验证依赖是否正确
    - why
        - explain why packages or modules are needed(解释为什么需要依赖)
- go.mod文件一旦创建后，它的内容将会被go toolchain全面掌控。go toolchain会在各类命令执行时，比如go get、go build、go mod等修改和维护go.mod文件。
## 运算符
Go 语言内置的运算符有：
- 算术运算符
- 关系运算符
- 逻辑运算符
- 位运算符
- 赋值运算符
    - =	简单的赋值运算符，将一个表达式的值赋给一个左值
    - +=	相加后再赋值
    - -=	相减后再赋值
    - *=	相乘后再赋值
    - /=	相除后再赋值
    - %=	求余后再赋值
    - <<=	左移后赋值
    - >>=	右移后赋值
    - &=	按位与后赋值
    - l=	按位或后赋值
    - ^=	按位异或后赋值
## 下划线
“_”是特殊标识符，用来忽略结果。
- 下划线在import中
    -  import 下划线（如：import _ hello/imp）的作用：当导入一个包时，该包下的文件里所有init()函数都会被执行，然而，有些时候我们并不需要把整个包都导入进来，仅仅是是希望它执行init()函数而已。这个时候就可以使用 import _ 引用该包。即使用【import _ 包路径】只是引用该包，仅仅是为了调用init()函数，所以无法通过包名来调用包中的其他函数。
- 下划线在代码中
    - 占位符
## 变量和常量
变量：
- 为什么要有变量：程序运行过程中的数据都是保存在内存中，我们想要在代码中操作某个数据时就需要去内存上找到这个变量，但是如果我们直接在代码中通过内存地址去操作变量的话，代码的可读性会非常差而且还容易出错，所以我们就利用变量将这个数据的内存地址保存起来，以后直接通过这个变量就能找到内存上对应的数据了。
- Go语言中的变量需要声明后才能使用，同一作用域内不支持重复声明。并且Go语言的变量声明后必须使用。
- 批量声明变量：
```go
var (
        a string
        b int
        c bool
        d float32
    )
```
- 在函数内部，可以使用更简略的 := 方式声明并初始化变量。
- 匿名变量_

常量:
```
const (
        pi = 3.1415
        e = 2.7182
    )
```
- 如果省略了值则表示和上面一行的值相同
- **iota**是go语言的常量计数器，只能在常量的表达式中使用。iota在const关键字出现时将被重置为0。const中每新增一行常量声明将使iota计数一次(iota可理解为const语句块中的行索引)。 使用iota能简化定义，在定义枚举时很有用。
    - 可以使用_跳过某些值
```go
 const (
            _  = iota
            KB = 1 << (10 * iota)
            MB = 1 << (10 * iota)
            GB = 1 << (10 * iota)
            TB = 1 << (10 * iota)
            PB = 1 << (10 * iota)
        )
 const (
            a, b = iota + 1, iota + 2 //1,2
            c, d                      //2,3
            e, f                      //3,4
        )
```
## 基本类型
|类型|长度(字节)|默认值|说明|
|:--|:--|:--|:--|
bool   |1  |**false**
byte	|1	|0	|uint8，一个ASCII字符
rune	|4	|0	|Unicode Code Point，int32，一个utf-8字符
int, uint	|4或8	|0	|由操作系统位数(32/64)决定
int8, uint8 |1	|0	|-128 ~ 127, 0 ~ 255，**byte是uint8 的别名**
int16, uint16	|2	|0	|-32768 ~ 32767, 0 ~ 65535
int32, uint32	|4	|0	|-21亿~ 21亿, 0 ~ 42亿，**rune是int32 的别名**
int64, uint64	|8	|0	
float32	|4	|0.0	
float64	|8	|0.0	
complex64	|8		
complex128	|16		
uintptr	|4或8	|	|以存储指针的 uint32 或 uint64 整数
array	|	|	|值类型
struct	|	|	|值类型
string	|	|“”	|UTF-8 字符串
slice	|	|nil	|引用类型
map	|	|nil	|引用类型
channel	|	|nil	|引用类型
interface	|	|nil	|接口
function	|	|nil	|函数
- uintptr 实际上就是一个 uint 用来表示地址，go 的指针和 c 不一样不能进行偏移操作，如果非要偏移的话就需要 unsafe.Pointer 和 uintptr 配合来实现。uintptr 不是一个指针 所以 GC 时也不会处理 uintptr 的引用。如果不涉及地址偏移时没有必要使用 uintptr 。------[来自知乎回答](https://www.zhihu.com/question/439659823)
- 标准库 math 定义了各数字类型取值范围。
- 空指针值 nil，而非C/C++ NULL。golang中有多种引用类型：pointer、interface、slice、map、channel、function。go作为一个强类型语言，不同引用类型的判空（nil）规则是不同的；比如：interface的判空规则是，需要判断类型和值是否都为nil(interface的底层是有类型和值构成的)slice的判空，需要判断slice引用底层数组的指针为空，容量和size均为0。
- 不允许将整型强制转换为布尔型。
- 字符串的内部实现使用UTF-8编码。
- 只有显示类型转化。
## 字符串：
- 多行字符串，用反引号`
```go
s1 := `第一行
    第二行
    第三行
    `
    fmt.Println(s1)
```
方法   |介绍
:--|:--
len(str)	 |求长度
+或fmt.Sprintf	|拼接字符串
strings.Split	|分割
strings.Contains	|判断是否包含
strings.HasPrefix,strings.HasSuffix	|前缀/后缀判断
strings.Index(),strings.LastIndex()	|子串出现的位置
strings.Join(a[]string, sep string)	|join操作

遍历字符串：
```go
 // Traversal strings
    func traversalString() {
        s := "JeFo的博客"
        for i := 0; i < len(s); i++ { //Traversal by byte
            fmt.Printf("%v(%c) ", s[i], s[i])
        }
        fmt.Println()
        for _, r := range s { //Traversal by rune
            fmt.Printf("%v(%c) ", r, r)
        }
        fmt.Println()
    }
```
```
74(J) 101(e) 70(F) 111(o) 231(ç) 154() 132(
                                           ) 229(å) 141() 154() 229(å) 174(®) 162(¢) 
74(J) 101(e) 70(F) 111(o) 30340(的) 21338(博) 23458(客)
```

修改字符串：
- 要修改字符串，需要先将其转换成[]rune或[]byte，完成后再转换为string。无论哪种转换，**都会重新分配内存，并复制字节数组。**
## 数组
- 数组可以通过下标进行访问，下标是从0开始，最后一个元素下标是：len-1
- 支持 "=="、"!=" 操作符，因为内存总是被初始化过的。
    - 相同类型的数组之间可以使用 == 或 != 进行比较，但不可以使用 < 或 >，也可以相互赋值。
    - 长度不同类型也不同。
- 指针数组 [n]*T，数组指针 *[n]T。
- 多维数组除了第一维，初始化时都不能用[...]省略长度声明。
- 值拷贝行为会造成性能问题，通常会建议使用 slice，或数组指针。
## 切片
```go
//用make()初始化
var s3 []int = make([]int, 0, 10)//len=0,cap=10
var s4 []int = make([]int, 5)//len=5
//先初始化一个数组，再截取相应部分得到切片
arr := [5]int{1, 2, 3, 4, 5}
var s6 []int
s6 = arr[1:4]  // 左闭右开
s7 = arr[1:3:5]//len=3-1;cap=5-1，这是一个危险的用法，极易产生bug
```
切片追加append（内置函数）：
```go
// The append built-in function appends elements to the end of a slice. If
// it has sufficient capacity, the destination is resliced to accommodate the
// new elements. If it does not, a new underlying array will be allocated.
// Append returns the updated slice. It is therefore necessary to store the
// result of append, often in the variable holding the slice itself:
//	slice = append(slice, elem1, elem2)
//	slice = append(slice, anotherSlice...)
// As a special case, it is legal to append a string to a byte slice, like this:
//	slice = append([]byte("hello "), "world"...)
var a = []int{1, 2, 3}
fmt.Printf("slice a : %v\n", a)
var b = []int{4, 5, 6}
fmt.Printf("slice b : %v\n", b)
c := append(a, b...)
fmt.Printf("slice c : %v\n", c)
d := append(c, 7)
fmt.Printf("slice d : %v\n", d)
e := append(d, 8, 9, 10)
fmt.Printf("slice e : %v\n", e)
slice := append([]byte("hello "), "world"...)//注意是字节数组和字符串。
fmt.Printf("slice slice : %v\n", slice)
```
- 超出原 slice.cap 限制，就会重新分配底层数组，即便原数组并未填满。
- 通常以 2 倍容量重新分配底层数组。在大批量添加数据时，建议一次性分配足够大的空间，以减少内存分配和数据复制开销。或初始化足够长的 len 属性，改用索引号进行操作。
- 及时释放不再使用的 slice 对象，避免持有过期数组，造成 GC 无法回收。

切片resize:
```go
package main

import (
    "fmt"
)

func main() {
    var a = []int{1, 3, 4, 5}
    fmt.Printf("slice a : %v , len(a) : %v\n", a, len(a))
    b := a[1:2]
    fmt.Printf("slice b : %v , len(b) : %v\n", b, len(b))
    c := b[1:3]
    fmt.Printf("slice c : %v , len(c) : %v\n", c, len(c))
}
```
```
slice a : [1 3 4 5] , len(a) : 4
slice b : [3] , len(b) : 1
slice c : [4 5] , len(c) : 2
```

string & slice :
string底层就是一个byte的数组，因此，也可以进行切片操作。
### 切片底层实现
切片的设计想法是由动态数组概念而来，为了开发者可以更加方便的使一个数据结构可以自动增加和减少。但是切片本身并不是动态数据或者数组指针。切片常见的操作有   **reslice、append、copy**。与此同时，切片还具有**可索引，可迭代**的优秀特性。
```go
func main() {
    arrayA := []int{1, 2}
    testArrayPoint(&arrayA)   // 1.传数组指针
    arrayB := arrayA[:]
    testArrayPoint(&arrayB)   // 2.传切片
    fmt.Printf("arrayA : %p , %v\n", &arrayA, arrayA)
}

func testArrayPoint(x *[]int) {
    fmt.Printf("func Array : %p , %v\n", x, *x)
    (*x)[1] += 1
}
```
```
func Array : 0xc4200b0140 , [1 2]
func Array : 0xc4200b0180 , [1 3]
arrayA : 0xc4200b0140 , [1 4]
```
- 传指针会有一个弊端，从打印结果可以看到，第一行和第三行指针地址都是同一个，万一原数组的指针指向更改了，那么函数里面的指针指向都会跟着更改。
- 用切片传数组参数，既可以达到节约内存的目的，也可以达到合理处理好共享内存的问题。打印结果第二行就是切片，切片的指针和原来数组的指针是不同的。
slice数据结构源码：
```go
// runtime/slice.go
type slice struct {
	array unsafe.Pointer // 指向一个数组的指针
	len   int // 切片长度 
	cap   int // 切片容量
}
```
如果想从 slice 中得到一块内存地址，可以这样做：
```go
s := make([]byte, 200)
ptr := unsafe.Pointer(&s[0])
```
自己构造一个slice:
```go
var ptr unsafe.Pointer
var s1 = struct {
    addr uintptr
    len int
    cap int
}{ptr, length, length}
s := *(*[]byte)(unsafe.Pointer(&s1))
```
在 Go 的反射中就存在一个与之对应的数据结构 SliceHeader，我们可以用它来构造一个 slice：
```go
var o []byte
sliceHeader := (*reflect.SliceHeader)((unsafe.Pointer(&o)))
sliceHeader.Cap = length
sliceHeader.Len = length
sliceHeader.Data = uintptr(ptr)
```
并非所有时候都适合用切片代替数组：因为切片底层数组可能会在堆上分配内存，而且小数组在栈上拷贝的消耗也未必比make 消耗大。
### 创建切片
**make 函数允许在运行期动态指定数组长度，绕开了数组类型必须使用编译期常量的限制。**
创建切片有两种形式，make 创建切片，字面量创建切片（既可以初始化一个新的，也可以截取一个数组,截取一个数组的时候cap未声明时为数组容量）。
#### 空切片和nil切片
nil切片：| nil (Pointer) | Len(int) | Cap(int) |
-  var slice []int
nil 切片被用在很多标准库和内置函数中，描述一个不存在的切片的时候，就需要用到 nil 切片。比如函数在发生异常的时候，返回的切片就是 nil 切片。nil 切片的指针指向 nil。

空切片： | Array (Pointer) | Len(int) | Cap(int) |
- silce := make( []int , 0 )
- slice := []int{ }
空切片一般会用来表示一个空的集合。比如数据库查询，一条结果也没有查到，那么就可以返回一个空切片。

空切片和 nil 切片的区别在于，空切片指向的地址不是nil，指向的是一个内存地址，但是它没有分配任何内存空间，即底层元素包含0个元素。

不管是使用 nil 切片还是空切片，对其调用内置函数 append，len 和 cap 的效果都是一样的。
#### 扩容策略
如果切片的容量小于 1024 个元素，于是扩容的时候就翻倍增加容量。

一旦元素个数超过 1024 个元素，那么增长因子就变成 1.25 ，即每次增加原来容量的四分之一。

注意：扩容扩大的容量都是针对原来的容量而言的，而不是针对原来数组的长度而言的。

扩容后的数组是新数组还是老数组？
- 如果如果切片扩容后容量比原来数组的容量最大值还大，扩容切片需要另开一片内存区域，把原来的值拷贝过来，再执行append()操作。
- 否则，不会开辟新数组，这种情况很危险，因为这种情况下，扩容以后的数组还是指向原来的数组,多个原来的数组上的切片会受新切片所影响！
### 切片拷贝
slicecopy 方法会把源切片值(即 fm Slice )中的元素复制到目标切片(即 to Slice )中，并返回被复制的元素个数，copy 的两个类型必须一致。slicecopy 方法最终的复制结果取决于较短的那个切片，当较短的切片复制完成，整个复制过程就全部完成了。

如果用 range 的方式去遍历一个切片，拿到的 Value 其实是切片里面的值拷贝。所以每次打印 Value 的地址都不变。由于 Value 是值拷贝的，并非引用传递，所以直接改 Value 是达不到更改原切片值的目的的，需要通过 &slice[index] 获取真实的地址。
## 指针
区别于C/C++中的指针，Go语言中的指针不能进行偏移和运算，是安全指针。

首先需要知道指针地址、指针类型和指针取值。

指针地址和指针类型：
- Go语言中的值类型（int、float、bool、string、array、struct）都有对应的指针类型

指针取值：*

**空指针**：
- 当一个指针被定义后没有分配到任何变量时，它的值为 nil

**new和make**:
- 在Go语言中对于引用类型的变量，我们在使用的时候不仅要声明它，还要为它分配内存空间，否则我们的值就没办法存储。
- new
    - 函数签名和举例
    ```go
    func new(Type) *Type

    func main() {
    var a *int
    a = new(int)
    *a = 10
    fmt.Println(*a)
    }
    ```
    - new函数不太常用，使用new函数得到的是一个类型的指针，并且该指针对应的值为该类型的零值。
- make
    - **只用于slice、map以及chan的内存创建，而且它返回的类型就是这三个类型本身**,因为这三种类型就是引用类型，所以就没有必要返回他们的指针了。
    - 以map举例：
    ```go
    func main() {
    var b map[string]int
    b = make(map[string]int, 10)
    b["测试"] = 100
    fmt.Println(b)
    }
    ```
## map
```go
    map[KeyType]ValueType
```
初始化时用make申请内存（或者直接填充元素）：
```go
    make(map[KeyType]ValueType, [cap])//cap不是必须的，但最好一开始就申请一个合适的容量
```
**判断某个key是否存在**：
```go
//map[key]会返回两个值，第二个是该键是否存在
    value, ok := map[key]   
```

map的遍历还是正常用for range，但有一点需要注意：**遍历map时元素顺序与添加键值对的顺序无关。**

按照指定顺序遍历map:思路是将map的key取出另存为切片再排序，再按照切片的顺序进行遍历即可。

**map & 切片**：
- 元素为map的切片：
```go
func main() {
    var mapSlice = make([]map[string]string, 3)
    for index, value := range mapSlice {
        fmt.Printf("index:%d value:%v\n", index, value)
    }
    fmt.Println("after init")
    // 对切片中的map元素进行初始化
    mapSlice[0] = make(map[string]string, 10)
    mapSlice[0]["name"] = "王五"
    mapSlice[0]["password"] = "123456"
    mapSlice[0]["address"] = "红旗大街"
}
```
- value为切片的map:
```go
func main() {
    var sliceMap = make(map[string][]string, 3)
    fmt.Println(sliceMap)
    fmt.Println("after init")
    key := "中国"
    value, ok := sliceMap[key]
    if !ok {
        value = make([]string, 0, 2)
    }
    value = append(value, "北京", "上海")
    sliceMap[key] = value
    fmt.Println(sliceMap)
}
```


map删除键值对：
```go
delete(map,key)
```
### map实现原理
map底层存储方式为（结构体）数组，在存储时key不能重复，当key重复时，value进行覆盖，**我们通过key进行hash运算（可以简单理解为把key转化为一个整形数字）然后对数组的长度取余，得到key存储在数组的哪个下标位置，最后将key和value组装为一个结构体，放入数组下标处**。

哈希冲突：即不同key经哈希映射后得到相同的数组下标。
- 解决办法：开放定址法：
    - 发现hashkey(key)的下标已经被别key占用的时候，在这个数组中空间中重新找一个没被占用的存储这个冲突的key。寻找方式有很多。常见的有线性探测法，线性补偿探测法，随机探测法。
        - 线性探测法：
            - 从冲突的下标处开始往后探测，到达数组末尾时，从数组开始处探测，直到找到一个空位置存储这个key，当数组都找不到的情况下会扩容（事实上当数组容量快满的时候就会扩容了）
            - 查找某一个key的时候，找到key对应的下标，比较key是否相等，如果相等直接取出来，否则按照顺寻探测直到碰到一个空位置，说明key不存在。
        - 拉链法：
            - 当key的hash冲突时，我们在冲突位置的元素上形成一个链表，通过指针互连接。
            - 当查找时，发现key冲突，顺着链表一直往下找，直到链表的尾节点，找不到则返回空
    - 开放定址法的优缺点：
        - 由上面可以看出拉链法比线性探测处理简单
        - 线性探测查找是会被拉链法会更消耗时间
        - 线性探测会更加容易导致扩容，而拉链不会
        - 拉链存储了指针，所以空间上会比线性探测占用多一点
        - 拉链是动态申请存储空间的，所以更适合链长不确定的

### go中map的实现原理
在go1.16中，map也是数组存储的的，每个数组下标处存储的是一个bucket,这个bucket的类型见下面代码，每个bucket中可以存储8个kv键值对，当每个bucket存储的kv对到达8个之后，会通过overflow指针指向一个新的bucket，从而形成一个链表,看bmap的结构
```go
// A bucket for a Go map.
type bmap struct {
	// tophash generally contains the top byte of the hash value
	// for each key in this bucket. If tophash[0] < minTopHash,
	// tophash[0] is a bucket evacuation state instead.即桶疏散状态
	tophash [bucketCnt]uint8
	// Followed by bucketCnt keys and then bucketCnt elems.
	// NOTE: packing all the keys together and then all the elems together makes the
	// code a bit more complicated than alternating key/elem/key/elem/... but it allows
	// us to eliminate padding which would be needed for, e.g., map[int64]int8.
	// Followed by an overflow pointer.
}
```
- tophash用来快速查找key值是否在该bucket中，而不同每次都通过真值进行比较。
- **map[int64]int8,key是int64（8个字节），value是int8（一个字节），kv的长度不同，如果按照kv格式存放，则考虑内存对齐v也会占用int64，而按照后者存储时，8个v刚好占用一个int64**
- **当往map中存储一个kv对时，通过k获取hash值，hash值的低八位和bucket数组长度取余，定位到在数组中的那个下标，hash值的高八位存储在bucket中的tophash中，用来快速判断key是否存在，key和value的具体值则通过指针运算存储，当一个bucket满时，通过overfolw指针链接到下一个bucket。**
- map的存储源码：
```go
// Like mapaccess, but allocates a slot for the key if it is not present in the map.如果key不在map里为其分配一个插槽（狭槽）
func mapassign(t *maptype, h *hmap, key unsafe.Pointer) unsafe.Pointer {
	if h == nil {
		panic(plainError("assignment to entry in nil map"))
	}
	if raceenabled {
		callerpc := getcallerpc()
		pc := funcPC(mapassign)
		racewritepc(unsafe.Pointer(h), callerpc, pc)
		raceReadObjectPC(t.key, key, callerpc, pc)
	}
	if msanenabled {
        //获取hash算法
		msanread(key, t.key.size)
	}
	if h.flags&hashWriting != 0 {
		throw("concurrent map writes")
	}
    //计算哈希值
	hash := t.hasher(key, uintptr(h.hash0))

	// Set hashWriting after calling t.hasher, since t.hasher may panic,
	// in which case we have not actually done a write.
	h.flags ^= hashWriting

    //如果bucket数组一开始为空，则初始化
	if h.buckets == nil {
		h.buckets = newobject(t.bucket) // newarray(t.bucket, 1)
	}

again:
    //定位在哪一个bucket中
	bucket := hash & bucketMask(h.B)
	if h.growing() {
		growWork(t, h, bucket)
	}
    //得到bucket的结构体
	b := (*bmap)(add(h.buckets, bucket*uintptr(t.bucketsize)))
    //获取高八位的哈希值
	top := tophash(hash)

	var inserti *uint8
	var insertk unsafe.Pointer
	var elem unsafe.Pointer
bucketloop:
    //死循环
	for {
        //循环bucket中的tophash数组
		for i := uintptr(0); i < bucketCnt; i++ {
            //如果hash不相等
			if b.tophash[i] != top {
                //判断是否为空，为空则插入
				if isEmpty(b.tophash[i]) && inserti == nil {
					inserti = &b.tophash[i]
					insertk = add(unsafe.Pointer(b), dataOffset+i*uintptr(t.keysize))
					elem = add(unsafe.Pointer(b), dataOffset+bucketCnt*uintptr(t.keysize)+i*uintptr(t.elemsize))
				}
                //插入成功，终止外层循环
				if b.tophash[i] == emptyRest {
					break bucketloop
				}
				continue
			}
            //高八位哈希值一样，获取已存在的kay
			k := add(unsafe.Pointer(b), dataOffset+i*uintptr(t.keysize))
			if t.indirectkey() {
				k = *((*unsafe.Pointer)(k))
			}
            //判断两个key是否相等，不相等就循环下一个
			if !t.key.equal(key, k) {
				continue
			}
			// already have a mapping for key. Update it.
			if t.needkeyupdate() {
				typedmemmove(t.key, k, key)
			}
            //获取已存在的value
			elem = add(unsafe.Pointer(b), dataOffset+bucketCnt*uintptr(t.keysize)+i*uintptr(t.elemsize))
			goto done
		}
        //如果上一个bucket没能找到插入，则通过overflow获取链表上的下一个bucket
		ovf := b.overflow(t)
		if ovf == nil {
			break
		}
		b = ovf
	}

	// Did not find mapping for key. Allocate new cell & add entry.

	// If we hit the max load factor or we have too many overflow buckets,
	// and we're not already in the middle of growing, start growing.
	if !h.growing() && (overLoadFactor(h.count+1, h.B) || tooManyOverflowBuckets(h.noverflow, h.B)) {
		hashGrow(t, h)
		goto again // Growing the table invalidates everything, so try again
	}

	if inserti == nil {
		// The current bucket and all the overflow buckets connected to it are full, allocate a new one.
		newb := h.newoverflow(t, b)
		inserti = &newb.tophash[0]
		insertk = add(unsafe.Pointer(newb), dataOffset)
		elem = add(insertk, bucketCnt*uintptr(t.keysize))
	}

	// store new key/elem at insert position
	if t.indirectkey() {
		kmem := newobject(t.key)
		*(*unsafe.Pointer)(insertk) = kmem
		insertk = kmem
	}
	if t.indirectelem() {
		vmem := newobject(t.elem)
		*(*unsafe.Pointer)(elem) = vmem
	}
	typedmemmove(t.key, insertk, key)
    //将高八位hash值存储
	*inserti = top
	h.count++

done:
	if h.flags&hashWriting == 0 {
		throw("concurrent map writes")
	}
	h.flags &^= hashWriting
	if t.indirectelem() {
		elem = *((*unsafe.Pointer)(elem))
	}
	return elem
}
```
## 结构体
**Go语言中通过结构体的内嵌再配合接口比面向对象具有更高的扩展性和灵活性。**

### 类型别名和自定义类型
自定义类型（新类型）：
```go
 //将MyInt定义为int类型
    type MyInt int 
```
- 可以基于内置的基本类型定义，也可以通过struct定义。

类型别名（Go1.9添加的新功能，注意编译后是原来的类型）：
```go
//将MyInt作为为int类型的昵称
    type MyInt = int 
```
## 结构体
本质上是一种聚合型的数据类型。

通过struct可以实现面向对象。

定义的时候，同样类型的字段也可以写在一行。

只有结构体被实例化时，才会真正被分配内存。

匿名结构体：定义临时数据结构时可能会用到。

语法糖：Go语言中支持对结构体指针直接使用.来访问结构体的成员，在使用new分配内存后得到的便是结构体指针。

**使用&对结构体进行取地址操作相当于对该结构体类型进行了一次new实例化操作。**
```go
p := &person{}
```
初始化（没有指定初始值的字段的值就是该字段类型的零值）：
```go
p := person{
    a: "1a",
    b: "2b",
}
//结构体指针
q := &ss{
    a: "1a",
    b: "2b",
}
//简写需注意三点：1.必须初始化结构体的所有字段。2.初始值的填充顺序必须与字段在结构体中的声明顺序一致。3.该方式不能和键值初始化方式混用。  
s := &d{
    "aq",
    "sw",
}
```

自己实现一个结构体构造函数：
```go
//值拷贝开销太大，返回结构体指针
func newPerson(name, city string, age int8) *person {
    return &person{
        name: name,
        city: city,
        age:  age,
    }
}
```
### 方法和接收者
Go语言中的方法（Method）是一种作用于特定类型变量的函数。这种特定类型变量叫做接收者（Receiver）。**接收者的概念就类似于其他语言中的this或者 self。**
```go
//接收者变量：接收者中的参数变量名在命名时，官方建议使用接收者类型名的第一个小写字母，而不是self、this之类的命名。例如，Person类型的接收者变量应该命名为 p，Connector类型的接收者变量应该命名为c等。
func (接收者变量 接收者类型) 方法名(参数列表) (返回参数) {
        函数体
}
``` 

**指针类型的接收者：**
- 指针类型的接收者由一个结构体的指针组成，由于指针的特性，调用方法时修改接收者指针的任意成员变量，在方法结束后，修改都是有效的。这种方式就十分接近于其他语言中面向对象中的this或者self。 
- 例如我们为Person添加一个SetAge方法，来修改实例变量的年龄。
```go
// SetAge 设置p的年龄
// 使用指针接收者
func (p *Person) SetAge(newAge int8) {
    p.age = newAge
}
//调用该方法
func main() {
    p1 := NewPerson("测试", 25)
    fmt.Println(p1.age) // 25
    p1.SetAge(30)
    fmt.Println(p1.age) // 30
} 
```

值类型的接收者：
```go
// SetAge2 设置p的年龄
// 使用值接收者
func (p Person) SetAge2(newAge int8) {
    p.age = newAge
}

func main() {
    p1 := NewPerson("测试", 25)
    p1.Dream()
    fmt.Println(p1.age) // 25
    p1.SetAge2(30) // (*p1).SetAge2(30)
    fmt.Println(p1.age) // 25
} 
```

**什么时候应该使用指针类型接收者：**
1. 需要修改接收者中的值
2. 接收者是拷贝代价比较大的大对象
3. 保证一致性，如果有某个方法使用了指针接收者，那么其他的方法也应该使用指针接收者。

为任意类型添加方法：
- 在Go语言中，接收者的类型可以是任何类型，不仅仅是结构体，任何类型都可以拥有方法。 举个例子，我们基于内置的int类型使用type关键字可以定义新的自定义类型，然后为我们的自定义类型添加方法。
- 注意事项：非本地类型不能定义方法，也就是说我们不能给别的包的类型定义方法。

结构体的匿名字段：
- 结构体允许其成员字段在声明时没有字段名而只有类型，这种没有名字的字段就称为匿名字段。
- 匿名字段默认采用类型名作为字段名，结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能有一个。

**嵌套结构体**
一个结构体中可以嵌套包含另一个结构体或结构体指针。

嵌套结构体内部可能存在相同的字段名。这个时候为了避免歧义需要指定具体的内嵌结构体的字段。

**结构体的继承**：
Go语言中使用结构体也可以实现其他编程语言中面向对象的继承。
```go
//Animal 动物
type Animal struct {
    name string
}

func (a *Animal) move() {
    fmt.Printf("%s会动！\n", a.name)
}

//Dog 狗
type Dog struct {
    Feet    int8
    *Animal //通过嵌套匿名结构体实现继承
}

func (d *Dog) wang() {
    fmt.Printf("%s会汪\n", d.name)
}

func main() {
    d1 := &Dog{
        Feet: 4,
        Animal: &Animal{ //注意嵌套的是结构体指针
            name: "旺财",
        },
    }
    d1.wang() //旺财会汪
    d1.move() //旺财会动！
}
```

结构体中字段大写开头表示可公开访问，小写表示私有（**仅在定义当前结构体的包中可访问**）。

**结构体与JSON序列化**：
- JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。易于人阅读和编写。同时也易于机器解析和生成。JSON键值对是用来保存JS对象的一种方式，键/值对组合中的键名写在前面并用双引号””包裹，使用冒号:分隔，然后紧接着值；多个键值之间使用英文,分隔。
```go
//Student 学生
type Student struct {
    ID     int
    Gender string
    Name   string
}

//Class 班级
type Class struct {
    Title    string
    Students []*Student
}

func main() {
    c := &Class{
        Title:    "101",
        Students: make([]*Student, 0, 200),
    }
    for i := 0; i < 10; i++ {
        stu := &Student{
            Name:   fmt.Sprintf("stu%02d", i),
            Gender: "男",
            ID:     i,
        }
        c.Students = append(c.Students, stu)
    }
    //JSON序列化：结构体-->JSON格式的字符串
    data, err := json.Marshal(c)
    if err != nil {
        fmt.Println("json marshal failed")
        return
    }
    fmt.Printf("json:%s\n", data)
    //JSON反序列化：JSON格式的字符串-->结构体
    str := `{"Title":"101","Students":[{"ID":0,"Gender":"男","Name":"stu00"},{"ID":1,"Gender":"男","Name":"stu01"},{"ID":2,"Gender":"男","Name":"stu02"},{"ID":3,"Gender":"男","Name":"stu03"},{"ID":4,"Gender":"男","Name":"stu04"},{"ID":5,"Gender":"男","Name":"stu05"},{"ID":6,"Gender":"男","Name":"stu06"},{"ID":7,"Gender":"男","Name":"stu07"},{"ID":8,"Gender":"男","Name":"stu08"},{"ID":9,"Gender":"男","Name":"stu09"}]}`
    c1 := &Class{}
    err = json.Unmarshal([]byte(str), c1)
    if err != nil {
        fmt.Println("json unmarshal failed!")
        return
    }
    fmt.Printf("%#v\n", c1)
} 
```

**结构体标签Tag:**
Tag是结构体的元信息，可以在运行的时候通过反射的机制读取出来。

Tag在结构体字段的后方定义，由一对反引号包裹起来，具体的格式如下：
```go
    `key1:"value1" key2:"value2"`  
```
- 结构体标签由一个或多个键值对组成。键与值使用冒号分隔，值用双引号括起来。键值对之间使用一个空格分隔。 注意事项： 为结构体编写Tag时，必须严格遵守键值对的规则。结构体标签的解析代码的容错能力很差，一旦格式写错，编译和运行时都不会提示任何错误，通过反射也无法正确取值。例如不要在key和value之间添加空格。
- 例如我们为Student结构体的每个字段定义json序列化时使用的Tag：
```go
//Student 学生
type Student struct {
    ID     int    `json:"id"` //通过指定tag实现json序列化该字段时的key
    Gender string //json序列化是默认使用字段名作为key
    name   string //私有不能被json包访问
}

func main() {
    s1 := Student{
        ID:     1,
        Gender: "女",
        name:   "pprof",
    }
    data, err := json.Marshal(s1)
    if err != nil {
        fmt.Println("json marshal failed!")
        return
    }
    fmt.Printf("json str:%s\n", data) //json str:{"id":1,"Gender":"女"}
} 
```
## 参考
- https://cloud.tencent.com/developer/article/1526095
- https://www.topgoer.cn/docs/golang/chapter02