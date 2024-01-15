# Go

## Intro

特点：

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

file structure

Project01/main, package

windows: go build -> hello .exe, go run -> run

