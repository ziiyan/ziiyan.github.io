---
title: 学习笔记：JavaScript运行原理
date: 2020-08-18 09:25:08
tags: [ JavaScript, 浏览器 ]
---

## 总体流程

* JS 源码
	* parser 解析器
* AST 抽象语法树
	* interpreter 解释器
* bytecode 字节码（平台无关）
	* compiler 编译器（根据平台编译相应代码）
* machine code 机器代码（汇编代码）

## V8

* 接收 JavaScript 代码，编译代码然后执行的 C++ 程序，编译后的代码可在多种操作系统、多种处理器上运行
* 负责工作
	* 编译和执行 JS 代码
	* 处理调用栈
	* 内存分配
	* 垃圾回收

### 编译和执行 JS 代码

* JS 源码通过 **parser 解析器**生成 AST 抽象语法树
* AST 抽象语法树通过**基准解释器 Ignition** 生成 bytecode 字节码
	* 此时 AST 被清除，释放内存空间
* bytecode 直接被解释执行
	* 同时 bytecode 将作为**基准执行模型**
	* bytecode 大小相当于**等效机器代码**大小的25%-50%
* **基准解释器 Ignition** 收集优化代码信息，发送给**编译器 TurboFan**
	* 如变量类型、函数执行频率
* **编译器 TurboFan** 根据优化信息 & bytecode 字节码编译出经过优化的机器代码
* deoptimization：优化后的机器代码可能被逆向还原成 bytecode 字节码
	* **基准解释器 Ignition** 收集到的信息是错误的
	* 比如调用函数时，变量类型开始几次传入都是 int，机器代码会假定变量类型为 int，后来改为 string 机器代码就无法执行，需要 deoptimization
	* 最好保持变量类型不变



