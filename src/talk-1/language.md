# 编程语言介绍

> 录制文件: [record/talk-1.mp4](https://cfile.thudep.com:6443/record/talk-1.mp4)

编程语言(programming language), 是用来定义计算机程序的形式语言. 它是一种被标准化的交流技巧, 用来向计算机发出指令, 能够让程序员准确地定义计算机所需要使用数据, 并精确地定义在不同情况下所应当采取的行动. [^1]

到今天为止, 已经出现了上千种编程语言, 根据 Stack Overflow 在 2024 年的开发者调查, 前十项最受欢迎的编程语言[^2]为:
JS, HTML/CSS, Python, SQL, TS, Bash/Shell, Java, C++, C, PHP.

## 语言的分类

编程语言可以按照不同的标准进行分类, 以下列出了一些常见的分类或标签.

我们可以说, C 语言是一种编译型, 过程式, 无垃圾回收的语言.

### 解释型, 编译型

典型的解释型语言为 Python, Python 代码的执行依赖于解释器 (interpreter, 更广泛地说, 需要运行时环境), 而编译型语言如 C 语言, 需要先将源代码针对不同的平台编译成机器码, 然后再在对应的平台上执行.

如何判断一个语言是解释型语言, 还是编译型语言呢? 一个简单的方法是看它是否需要运行时环境. 如果需要, 那么它就是解释型语言, 否则是编译型语言. 例如, Python 语言需要 Python 解释器, Java 语言需要 Java 虚拟机, 它们都是解释型语言.

什么是机器码呢? 机器码 (也称为汇编) 是依赖于平台的低级指令, 直接操作寄存器, 内存等硬件资源. 例如, 下面是一个 C 语言的 add 函数, 它把两个传入参数加起来, 并把结果返回给调用者.

```c
int add(int a, int b) {
    return a + b;
}
```
在 x86-64 架构上, 上面这个函数编译得到的机器码是

```assembly
add(int, int):
        lea     eax, [rdi + rsi]
        ret
```

而在 riscv64 架构上编译得到的机器码是

```assembly
add(int, int):
        addw    a0, a0, a1
        ret
```

这直观地说明了不同平台上的机器码是不同的.

不过, 解释型语言也可以通过 AOT (Ahead of Time) 或 JIT (Just In Time) 技术将热点代码编译成机器码, 以提高性能. 而编译型语言也可能通过 JIT 技术以解释型的样子出现 (例如, CERN 开发的 ROOT 数据分析框架包括 [Cling C++ 解释器](https://root.cern/cling/), 它能够交互地运行 C/C++ 代码!).

因此, 也一直有人呼吁废除这种分类.因为编程**语言**只规定语法语义. 如何让代码可运行, 属于实现的范畴. 因此, 应该说 Python 语言往往被实现为解释型的, 而 C 语言往往被实现为编译型的. 在通常的实现外, 还有很多其他实现方式.

### 面向对象, 函数式, 过程式

面向对象编程 (OOP, Object-Oriented Programming) 是一种编程范式, 它将数据和操作数据的方法组合在一起, 使得对象可以通过调用方法来操作数据. 例如, Python, C++, Java 是面向对象的语言. 最典型的场景是, 你可以定义一个类, 然后创建这个类的实例, 调用实例的方法.

函数式编程 (FP, Functional Programming) 是另一种编程范式, 它将计算过程看作是数学函数的求值, 避免使用可变状态和副作用, 例如 Haskell, Lisp 是函数式语言.

过程式编程 (Procedural Programming) 是最朴素的编程范式, 它认为程序应该是一系列指令 (这很底层). 如果没有用到上面两种范式的话, 那这种编程语言可能就是过程式语言了.

当我们说某某语言是面向对象语言或者如何如何时, 指的该语言支持使用这种范式, 人们常用这种范式编程. 不代表这种语言只能用这种范式编程.

所以, 面向对象和函数式编程并不是互斥的. 你可以在面向对象语言中使用函数式编程的技巧, 也可以在函数式语言中使用面向对象的技巧. 例如 Rust 语言是一门多范式的语言, 但也有批评者认为 Rust 由于不存在继承, 不是一门真正的面向对象语言.

### 垃圾回收

[垃圾回收 (GC, Garbage Collection)](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)) 是一种自动的内存管理技术, 一般实现为定期扫描, 自动标记并释放不再使用的内存, 使得程序员不需要手动管理内存 (显式请求内存分配, 使用完毕后显式释放内存).

垃圾回收会带来相当的性能开销, 但也为程序员减轻了负担.

除了垃圾回收和手动内存管理, 也存在[所有权系统 (Ownership System)](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html) 这种内存管理方式, 例如 Rust 语言就是使用所有权系统来管理内存的.

可以依靠是否有垃圾回收机制来对语言进行分类. 例如, C/C++ 是没有垃圾回收机制的语言, 需要程序员手动管理内存, 而 Java, Python 是具有垃圾回收机制的语言, 至于 Rust, 在编译器无法推导生命周期时, 需要程序员显式标注生命周期, 以此满足所有权系统的要求.

### 高级语言, 低级语言

对于什么是高级语言, 什么是低级语言, 人们没有明确的定义. 一般来说, 直接操作硬件资源的语言被称为低级语言, 而更接近人类语言的语言被称为高级语言.

## 哪种语言适合我?

这个问题的回答取决于你的需求, 以及你熟悉哪些语言.

场景一, 你获得了 CSV 格式记录的数据, 你希望把它转换为 HDF5 格式保存. 在这种情况下, 数据才是最重要的, 处理数据转换的程序只要跑通一次, 你就可以把它扔掉, 那么 Python 生态丰富, 调试方便的特点就很适合.

场景二, 你需要批量为选手推送题目的更新补丁, 因为这个操作需要与 git, ssh 等工具交互, 你需要一个能够方便地调用系统命令的语言. 在这种情况下, Bash 脚本最为适合.

场景三, 你需要快速开发一个高性能的网络服务. Rust 或 Go 可能是多快好省的选择.

场景四, 你需要为单片机编写程序, 或为内核编写驱动. C 语言可能是唯一的选择.

场景五, 你需要做一些数据分析, 需要用到假设检验, 回归分析等统计学库. R, Python 都是不错的选择.

当然, 总是使用你最喜欢的语言也不一定错误, 因为你了解如何 debug. 但一般来说, 选择一种适合当前场景的语言是最佳实践.

## C/C++

贝尔实验室的 Dennis Ritchie 于 1969-1973 年间开发了 C 语言, 很长时间以来, C 语言是系统编程和嵌入式开发的首选语言, 并且在其他领域有着广泛的应用. 它足够接近硬件, 又足够高级, 使得程序员可以在不同的层次上编程.

C 语言还是效率最高的编程语言之一. 衡量一个编程语言的效率, 最主要的指标是它的执行速度. C 语言的执行速度快, 内存占用小.

[Ranking Programming Languages by Energy Efficiency](https://haslab.github.io/SAFER/scp21.pdf) 以 C 语言作为归一化基准, 比较了数十种编程语言的能耗和执行时间, 我们摘录部分数据如下. 可以看到, C 的速度是 Python 的 70 多倍, Python 需要的资源是 C 的 70 多倍!

| 语言       | 能耗  | 时间  |
| ---------- | ----- | ----- |
| C          | 1.00  | 1.00  |
| Rust       | 1.03  | 1.04  |
| C++        | 1.34  | 1.56  |
| Java       | 1.98  | 1.89  |
| Fortran    | 2.52  | 4.20  |
| Go         | 3.23  | 2.83  |
| JavaScript | 4.45  | 6.52  |
| TypeScript | 21.50 | 46.20 |
| Lua        | 45.98 | 82.91 |
| Python     | 75.88 | 71.90 |

作为一个编译型的语言, C 语言需要先编译成机器码. 编译会经过以下几个步骤 (以 gcc 为例):

```txt
               pre processor                   compiler                   assembler                 linker

    hello.c   --------------->      hello.i   --------->    hello.s      ---------->    hello.o    --------->    hello

source program              modified source program     assembly program           relocatable object       executable object
     text                            text                    text                        binary                  binary
```

在 Linux 系统上, 可以使用 clang 或 gcc 编译器来编译 C 语言程序. 例如, 下面是一个简单的 hello world 程序:

```c
#include <stdio.h>
int main() {
    printf("Hello, World!\n"); // 这个程序会输出 Hello, World! 到终端
    return 0;
}
```

编译这个程序的命令是

```sh
clang hello.c -o hello
```

```sh
gcc hello.c -o hello
```

这个程序会输出 `Hello, World!` 到终端.

如果你想快速学习 C 语言, 可以阅读 [Learn C in Y Minutes](https://learnxinyminutes.com/zh-cn/c/), 配合 [C 语言教程 | 菜鸟教程](https://www.runoob.com/cprogramming/c-tutorial.html)一同学习.

想要深入地了解有关 C 的知识, 推荐阅读 [C Primer Plus 第6版 中文版 - C Primer Plus(第六版)中文版.pdf](https://img.anfulai.cn/bbs/94810/C%20Primer%20Plus(%E7%AC%AC%E5%85%AD%E7%89%88)%E4%B8%AD%E6%96%87%E7%89%88.pdf).

## 总结

读完这里, 你只需要知道并记住以下这些概念, 其他作为拓展的知识与后续学习关系不大.

1. 机器码是依赖于平台的低级指令, 直接操作硬件资源.
1. 解释型语言需要运行时环境, 编译型语言需要先编译成机器码.
1. C 语言是一种高效的编译型语言, 应用广泛, 贴近底层, 需要程序员手动管理内存.

[^1]: [编程语言 - 维基百科](https://zh.wikipedia.org/zh-cn/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80)
[^2]: [Technology | 2024 Stack Overflow Developer Survey](https://survey.stackoverflow.co/2024/technology#1-programming-scripting-and-markup-languages)
