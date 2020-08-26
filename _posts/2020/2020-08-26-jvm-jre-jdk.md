---
layout: post
title: JVM，JRE，JDK 有什么不同
category: Java
tags: [JVM, JRE, JDK]
keywords: JVM,JRE,JDK
excerpt:
---

## Java 虚拟机（JVM）

使用 Java 编程语言的主要优势就是平台的独立性。你曾经想知道过 Java 怎么实现平台的独立性吗？对，就是虚拟机，它抽象化了硬件设备，开发者和他们的程序的得以操作系统。虚拟机的职责就是处理和操作系统的交流。Java 不同的接口规范对任何平台都有良好的支持，因为 JVM 很好的实现了每个平台的规范。JVM 可以理解伪代码字节码，在用户和操作系统之间建立了一层枢纽。

## Java 运行时环境（JRE）

Java 运行时环境是 JVM 的一个超集。JVM 对于一个平台或者操作系统是明确的，而 JRE 确实一个一般的概念，他代表了完整的运行时环境。我们在 JRE 文件夹中看到的所有的 JAR 文件和可执行文件都会变成运行时的一部分。事实上，运行时 JRE 变成了 JVM。所以对于一般情况时候使用 JRE，对于明确的操作系统来说使用 JVM。当你下载了 JRE 的时候，也就自动下载了 JVM。

## Java 开发工具箱（JDK）

Java 开发工具箱指的是编写一个 Java 应用所需要的所有 JAR 文件和可执行文件。事实上，JRE 是 JDK 的一部分。如果你下载了 JDK，你会看到一个名叫 JRE 的文件夹在里面。JDK 中要被牢记的 JAR 文件就是 tools.jar，它包含了用于执行 Java 文档的类还有用于类签名的 JAR 包。

![](/assets/images/2020/JVM-JRE-JDK.png)

## 参考

[https://www.cnblogs.com/xiaozhijing/p/7919455.html](https://www.cnblogs.com/xiaozhijing/p/7919455.html)

[http://www.cocoachina.com/articles/236399](http://www.cocoachina.com/articles/236399)
