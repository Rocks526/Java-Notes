- [Scala介绍](#jc)
- [Scala环境搭建](#hj)





# <a id="jc">Scala介绍</a>

- Scala简介

Scala 是 Scalable Language 的简写，是一门面向函数的编程语言，联邦理工学院洛桑（EPFL ）的Martin Odersky于2001年开始设计Scala。

- Scala特点

1. Scala是一门以java虚拟机（JVM）为目标运行环境并将面向对象和函数式编程的最佳特性结合在一起的静态类型编程语言。

> 静态类型也叫强类型，指变量的类型一旦定义，后续不可更改，例如Java，Scala，与之相对的是弱类型语言，如Python和JS，变量类型可以随意更改。

2. Scala 是一门多范式 (multi-paradigm) 的编程语言，Scala支持面向对象和函数式编程

3. Scala源代码(.scala)会被编译成Java字节码(.class)，然后运行于JVM之上，并可以调用现有的Java类库，实现两种语言的无缝对接。

4. Scala作为一门语言来看， 非常的简洁高效

5. Scala 在设计时，马丁·奥德斯基 是参考了Java的设计思想，可以说Scala是源于Java，同时马丁·奥德斯基也加入了自己的思想，将函数式编程语言的特点融合到Java中

> Scala的创始人（Martin Odersky）是编译器及编程的狂热爱好者，长时间的编程之后，希望发明一种语言，能够让写程序这样的基础工作变得高效，简单，且令人愉悦。所以当接触到Java语言后，对Java这门便携式，运行在网络，且存在垃圾回收的语言产生了极大的兴趣，所以决定将函数式编程语言的特点融合到Java中，由此发明了两种语言（Pizza & Scala） 
>
> Pizza和Scala极大地推动了Java编程语言的发展。
>
> Jdk5.0 的泛型，for循环增强, 自动类型转换等，都是从Pizza 引入的新特性。
>
> Jdk8.0 的类型推断，Lambda表达式就是从Scala引入的特性。

# <a id="hj">Scala环境搭建</a>

- Scala运行环境搭建

1. Scala需要Java运行时库，安装Scala需要首先安装JVM虚拟机并配置好，推荐安装JDK1.8

2. 安装好Java环境后，下载Scala安装包，解压，并配置环境变量

![image-20201125161326632](http://rocks526.top/lzx/image-20201125161326632.png)

- Scala的交互式运行环境

![image-20201125161501145](http://rocks526.top/lzx/image-20201125161501145.png)

> Scala也提供类似于Python的交互式运行环境，不同的是，Python是解释性语言，交互式环境其实是解释器在不断的对用户输入的内容计算并输出，而Scala运行于JVM之上，会将scala代码编译成字节码运行，因此此交互式环境其实是将指令代码快速的编译成Java字节码并在JVM加载执行，最终将执行结果输出到命令行中。

- Scala程序

编写一个名字为Hello.scala的程序，内容如下：

![image-20201125161916402](http://rocks526.top/lzx/image-20201125161916402.png)

执行方式有两种，第一种是和Java类似，先通过scalac编译成class字节码，然后运行，第二种是直接scala运行文件，本质上也是先编译成字节码再Jvm加载运行，只不过是放在内存中生成字节码。

![image-20201125162009709](http://rocks526.top/lzx/image-20201125162009709.png)

- IDEA



