---
layout: post
title: Eclipse Memory Analyzer（Java 内存泄漏分析工具）
category: Java
tags: [Eclipse, Memory, Analyzer, MAT]
keywords: Eclipse,Memory,Analyzer,MAT
excerpt:  Eclipse Memory Analyzer 它被认为是一个“傻瓜式“的堆转储文件分析工具，通过该工具可以生成一个专业的分析报告，从而帮我们准确地定位到问题的所在位置。
---

一个大型的 Java 项目也许从开发到测试结束并未发现一些大的问题，但是在生产环境中还是会出现一些非常棘手的问题，如内存泄漏，遇到这样的问题对于一个经验尚浅的开发人员来说难度非常大，好的一点是 JVM 能够记录下问题发生时系统的部分运行状态，并将其存储在堆转储 （Heap Dump）文件中，从而为我们分析和诊断问题提供了重要的依据。

接下来介绍的一个工具是 Eclipse Memory Analyzer 它被认为是一个“傻瓜式“的堆转储文件分析工具，通过该工具可以生成一个专业的分析报告，从而帮我们准确的定位到问题的所在位置。

## 安装 Eclipse Memory Analyzer

这里介绍三种安装方式

方式一：直接从官网下载程序包 http://www.eclipse.org/mat/downloads.php，解压可直接使用。

方式二：在 Eclipse 中通过 Help -> Install New Software，输入地址 http://download.eclipse.org/mat/1.6.1/update-site/ 进行安装。

方式三（推荐）：Eclipse 的可插拔式插件安装方式，这里不在介绍具体的插件安装步骤，安装后重启 Eclipse，这样就可以在 Eclipse 中直接打开 Heap Dump 文件了。

![](/assets/images/2021/Install_MAT.png)

## 配置环境参数

安装完成之后，为了更有效率的使用 Eclipse Memory Analyzer ，我们还需要做一些配置工作。因为通常而言，分析一个堆转储文件需要消耗很多的堆空间，为了保证分析的效率和性能，在有条件的情况下，我们会建议分配给 Eclipse Memory Analyzer 尽可能多的内存资源。你可以采用如下两种方式来分配内存更多的内存资源给 Eclipse Memory Analyzer。

方式一：修改启动参数 MemoryAnalyzer.exe -vmargs -Xmx4g

方式二：编辑文件 MemoryAnalyzer.ini，在里面添加类似信息 -vmargs – Xmx4g。

## 如何获得堆转储文件

方式一：在 Eclipse 中配置JVM启动参数 -XX:+HeapDumpOnOutOfMemoryError

方式二：通过 JDK 自带的工具 JMap，JConsole 来获得一个堆转储文件

这里我们使用方式一来获得

## 结合案例分析

**编写一个内存溢出的例子然后获得堆转储文件**

```
public class HeapOOM {

    public static void main(String[] args) {
        List<OOMObject> ooms = new ArrayList<OOMObject>();
        while (true) {
            ooms.add(new OOMObject());
        }
    }

    static class OOMObject {
    }
}
```

**VM 参数配置如下**

![](/assets/images/2021/MAT_VM_Arguments.png)

**运行示例代码即可生成如下文件然后双击打开**

![](/assets/images/2021/MAT_Dump.png)

## 分析报告

内存占用整体概览

![](/assets/images/2021/MAT_Overview.png)

直接点击饼图下方的 Reports -> Leak Suspects 链接来生成报告。

## 查看报告一（内存消耗的整体状况）

![](/assets/images/2021/MAT_Leak_Suspects.png)

从图上我们可以清晰地看到一个可疑对象消耗了系统近 98% 的内存。再往下看饼图下方文字简短描述了大量的内存是由属于 Object 实例的对象所消耗的，system class loader 负责加载这个对象。也许从这里还不能找出内存泄漏的具体原因，接着往下看。

## 查看报告二（分析问题的所在）

首先我们简单回顾下 JAVA 的内存回收机制，内存空间中垃圾回收的工作由垃圾回收器（Garbage Collector，GC）完成的，它的核心思想是：对虚拟机可用内存空间，即堆空间中的对象进行识别，如果对象正在被引用，那么称其为存活对象，反之，如果对象不再被引用，则为垃圾对象，可以回收其占据的空间，用于再分配。

在垃圾回收机制中有一组元素被称为根元素集合，它们是一组被虚拟机直接引用的对象，比如，正在运行的线程对象，系统调用栈里面的对象以及被 system class loader 所加载的那些对象。堆空间中的每个对象都是由一个根元素为起点被层层调用的。因此，一个对象还被某一个存活的根元素所引用，就会被认为是存活对象，不能被回收，进行内存释放。因此，我们可以通过分析一个对象到根元素的引用路径来分析为什么该对象不能被顺利回收。如果说一个对象已经不被任何程序逻辑所需要但是还存在被根元素引用的情况，我们可以说这里存在内存泄露。

点击“Details”链接

## 查看从根元素到内存消耗聚集点的最短路径

![](/assets/images/2021/MAT_Shortest_Paths_To_The_Accumulation_Point.png)

我们可以很清楚的看到整个引用链，内存聚集点是在 Java main 线程创建的一个集合元素上。接下来，我们再继续看看，这个对象集合里到底存放了什么，为什么会消耗掉如此多的内存。

> 注：
>
> Shallow Heap 为对象自身占用的内存大小，不包括它引用的对象。
>
> Retained Heap 为当前对象大小 + 当前对象可直接或间接引用到的对象的大小总和，如：
>
> A 对象的 Retained Heap = A 对象的 Shallow Heap + C 对象的 Shallow Heap 这里不包含 D 对象的 Shallow Heap 因为 D 对象被 Root 根对象所引用。
>
> B 对象的 Retained Heap = B 对象的 Shallow Heap。
>
> ![](/assets/images/2021/MAT_Shallow_Heap_Retained_Heap.png)

# 查看内存消耗聚集对象信息

![](/assets/images/2021/MAT_Accumulated_Objects_In_Dominator_Tree.png)

在这张图上，我们可以清楚的看到，这个对象集合中保存了大量 OOMObject 对象的引用，就是它导致的内存泄露。

至此，我们已经拥有了足够的信息去寻找泄露点。

## 参考

[https://my.oschina.net/feinik/blog/874023](https://my.oschina.net/feinik/blog/874023)

[https://www.cnblogs.com/shihaiming/p/11389386.html](https://www.cnblogs.com/shihaiming/p/11389386.html)

[https://www.jianshu.com/p/97251691af88](https://www.jianshu.com/p/97251691af88)

[https://blog.csdn.net/zhanshenzhi2008/article/details/89070049](https://blog.csdn.net/zhanshenzhi2008/article/details/89070049)
