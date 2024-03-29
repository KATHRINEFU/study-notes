# Go

## Intro

### 特点：

1. 继承C理念包括表达式语法、控制结构、基础数据类型、调用参数传值、指针等，也保留了和C一样的编译执行方式及弱化的指针
2. 引入包的概念，用于组织程序结构，一个文件要归属于一个包不能单独存在
3. 垃圾回收机制，内存自动回收
4. 天然并发
   1. 从语言层面支持并发，实现简单
   2. goroutine 轻量级虔诚可实现大病发处理，高效利用多核
   3. 基于CPS并发模型实现 communicating sequential process
5. 吸收了管道通信机制实现不同goroute之间的通信
6. 支持函数返回多个值
7. 新的创新：比如切片Slice（动态数组）、延时执行defer
8. 快速编译，高效执行，易于开发。
9. 非常重要的特性就是它的构建速度
10. Go 语言采用包模型，这个模型通过严格的依赖关系检查机制来加快程序构建的速度，提供了非常好的可量测性。

### 语言特性

- Go 语言没有类和继承的概念，它通过接口（interface）的概念来实现多态性。
- 函数是 Go 语言中的基本构件
- Go 语言使用静态类型，所以它是类型安全的一门语言
- Go 语言其实也有一些动态语言的特性（通过关键字 `var`）
- 作为强类型语言，隐式的类型转换是不被允许的，记住一条原则：让所有的东西都是显式的。
- Go 语言支持交叉编译，比如说你可以在运行 Linux 系统的计算机上开发运行 Windows 下运行的应用程序。这是第一门完全支持 UTF-8 的编程语言，

### 目标

- 主要目标是将静态语言的安全性和高效性与动态语言的易开发性进行有机结合
- 另一个目标是对于网络通信、并发和并行编程的极佳支持

### 执行流程分析：

.go 文件 go build编译 ->  .exe文件（可指定文件名 -o） -> 运行得到结果,

.go 文件 go run编辑运行一步 -> 得到结果

如果先编译了可执行文件那么改文件拷贝到没有go开发环境及其上仍然可以运行，编译时，编译器会将程序运行依赖的库文件包含在可执行文件中。

### 开发注意事项

- 源代码以go为拓展名
- 执行入口是main函数
- 严格区分大小写
- Go方法由一条条语句组成，不需要分号
- Go编译器是一行一行编译的，一行只有一个语句
- go定义的变量或者import的包如果没有使用到，代码不能编译通过
- 大括号成对出现

### 格式化代码

在命令行输入 `gofmt –w program.go` 会格式化该源文件的代码然后将格式化后的代码覆盖原始内容（如果不加参数 `-w` 则只会打印格式化后的结果而不重写文件）；`gofmt -w *.go` 会格式化并重写所有 Go 源文件；`gofmt map1` 会格式化并重写 map1 目录及其子目录下的所有 Go 源文件。

## 基本结构和数据类型

Go 的源文件以 `.go` 为后缀名存储在计算机中，这些文件名均由小写字母组成，如 `scanner.go` 。如果文件名由多个部分组成，则使用下划线 `_` 对它们进行分隔，如 `scanner_test.go` 。文件名不包含空格或其他特殊字符。

下面列举了 Go 代码中会使用到的 25 个关键字或保留字：

| break    | default     | func   | interface | select |
| -------- | ----------- | ------ | --------- | ------ |
| case     | defer       | go     | map       | struct |
| chan     | else        | goto   | package   | switch |
| const    | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |

### 包

每个 Go 文件都属于且仅属于一个包，所有的包名都应该使用小写字母。

你必须在源文件中非注释的第一行指明这个文件属于哪个包，如：`package main`

如果想要构建一个程序，则包和包内的文件都必须以正确的顺序进行编译。包的依赖关系决定了其构建顺序。

属于同一个包的源文件必须全部被一起编译，一个包即是编译时的一个单元，因此根据惯例，每个目录都只包含一个包。

**如果对一个包进行更改或重新编译，所有引用了这个包的客户端程序都必须全部重新编译。**

Go 中的包模型采用了显式依赖关系的机制来达到快速编译的目的，编译器会从后缀名为 `.o` 的对象文件（需要且只需要这个文件）中提取传递依赖类型的信息。

