---
layout: post
title: Java 内存区域
category: Java
tags: [Java, 内存区域]
keywords: Java,内存区域
excerpt: Java 虚拟机在执行 Java 程序过程中会把它所管理的内存划分为若干个不同的数据区域。这些区域都有各自的用途，以及创建和销毁的时间，有的区域随着虚拟机进程的启动而存在，有些区域则是以来用户线程的启动和结束而建立和销毁。
---

Java 虚拟机在执行 Java 程序过程中会把它所管理的内存划分为若干个不同的数据区域。这些区域都有各自的用途，以及创建和销毁的时间，有的区域随着虚拟机进程的启动而存在，有些区域则是以来用户线程的启动和结束而建立和销毁。Java 虚拟机所管理的内存将会包括以下几个运行区域。

![](/assets/images/2020/Java_Memory_Area.png)

## 线程私有的数据区域

### 程序计数器

程序计数器有以下三个特点：

* 较小

  程序计数器是一块较小的内存空间，它的作用可以看做是当前线程所指向的字节码的行号指示器。在虚拟机的概念模型里，字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要指向字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成。

* 线程私有

  由于 Java 虚拟机的多线程是通过线程轮流切换并分配处理器指向时间的方式来实现的，在任何一个确定的时刻，一个处理器（对于多核处理器来说是一个内核）只会执行一条线程的指令。因此，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各条线程之间的计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。

* 无异常

  如果线程正在执行的是一个 Java 的方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址；如果正在执行的是 Native 方法，这个计数器值则为空（Undefined）。此内存区域是唯一一个在 Java 虚拟机规范中没有任何 OutOfMemoryError 情况的区域。

### 虚拟机栈

* 线程私有

  与程序计数器一样，Java 虚拟机栈也是线程私有的，它的生命周期与线程相同。

* 描述 Java 方法执行的内存模型

  虚拟机栈描述的是 Java 方法执行的内存模型：每个方法被执行的时候都会同时创建一个栈帧（Stack Frame，栈帧是方法运行期的基础数据结构）用于存储局部变量表、操作栈、动态链接、方法出口等信息。每一个方法被调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈栈从入栈到出栈的过程。

* 异常

  在 Java 虚拟中，对虚拟机规定了下面两种异常：

  * StockOverflowError

    当执行 Java 方法是会进行压栈的操作，在栈栈会保存局部变量、操作栈和方法出口等信息。

    JVM 规定了栈的最大深度，如果线程请求执行方法时栈的深度大于规定的深度，就会抛出栈溢出异常 StockOverflowError。

  * OutOfMemoryError

    如果虚拟机在扩展时无法申请到足够的内存，就会抛出内存溢出异常 OutOfMemoryError。

### 本地方法栈

本地方法栈的作用于虚拟机非常相似，它有下面两个特点。

* 为 native 服务

  本地方法栈与虚拟机栈的区别是虚拟机栈为 Java 服务，而本地方法栈为 native 方法服务。

* 异常

  与虚拟机栈一样，本地方法栈也会抛出 StackOverflowError 和 OutOfMemoryError 异常。

## 线程共享的数据区域

### Java 堆

Java 堆（Java Heap）也就是实例堆，它有以下四个特点：

* 最大

  对于大多数应用来说，Java 堆（Java Heap）是 Java 虚拟机所管理的内存中最大的一块。

* 线程共享

  Java 堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。

* 存放实例

  此内存区域的唯一目的就是存放对象的实例，几乎所有的对象实例都在这里分配内存。这一点在 Java 虚拟机规范中的描述是：所有的对象实例以及数组都要在堆上分配，但是随着 JIT 编译器的发展与逃逸分析技术的逐渐成熟，栈上分配、标量替换优化技术将会导致一些微妙的变化发送，所有的对象都分配在堆上也渐渐变得不那么“绝对”了 。

* GC

  Java 堆是垃圾收集器管理的主要区域，因此很多时候也被称为“GC堆”（Carbage Collected Heap）。如果从内存回收的角度看，由于现在收集器基本都是采用的分代收集算法，所以 Java 堆中还可以细分为：新生代和老年代。如果从内存分配的角度看，线程共享的 Java 堆中可能划分多个线程私有的分配缓冲区（Thread Local Allocation Buffer，TLAB）。不过，无论如何划分，都与存放内容无关，无论哪个区域，存储的都依然是对象实例，进一步划分的目的是为了更好地回收内存，或者更快地分配内存。

### 方法区

方法区存储的是已经被虚拟机加载的数据，它有以下三个特点:

* 线程共享

  方法区域 Java 堆一样，是各个线程共享的内存区域，它用于存储已经被虚拟机加载的等数据。

* 存储的数据类型

  * 类的信息；

  * 常量；

  * 静态变量；

  * 即时编译器编译后的代码等。

* 异常

  方法区的大小决定履历系统可以保存多少个类，如果系统定义了太多的类，导致方法区溢出，虚拟机同样抛出内存溢出异常 OutOfMemoryError。

**方法区又可以分为运行时常量池和直接内存两部分。**

* **运行常量池**

  运行时常量池（Running Constant Pool）是方法区的一部分。

  Class 文件中处了有类的 版本、字段、方法和接口等描述信息，还有一项信息就是常量池（Constant Pool Table）。

  常量池用于存放编译期生成的各种字面量和符号引用，这部分内容将在类加载后进入方法区的运行时常量池中存放。

  运行时常量池受到方法区内存的限制，当常量池无法再申请到内存时就会抛出 OutOfMemoryError 异常。

* **直接内存**

  直接内存（Direct Memory）有以下四个特点：

  * 在虚拟机数据区外

    直接内存不是虚拟机运行时数据区的一部分，也不是 Java 虚拟机规范中定义的内存区域。

  * 直接分配

    在 JDL1.4 中新加入的  NIO（New Input/Output）类，引入了一种基于通道（Channel）与缓冲区（Buffer）的 I/O 方式，它可以使用 native 函数库直接分配堆外内存，然后通过一个存储在 Java 堆中的 DirectByteBuffer 对象作为这块内存的引用操作，这样能避免在 Java 堆和 native 堆中来回复制数据。

  * 受设备内存大小限制

    直接内存的分配不会受到 Java 堆大小的限制，但是会受到设备总内存（RAM 以及 SWAP 区）大小以及处理器寻址空间的限制。

  * 异常

    直接内存的容量默认与 Java 对的最大值一样，如果超额申请内存，也可能导致 OOM 异常出现。

## 参考

[https://www.cnblogs.com/steffen/p/11368018.html](https://www.cnblogs.com/steffen/p/11368018.html)
