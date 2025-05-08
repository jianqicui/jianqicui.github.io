---
layout: post
title: Java 异常体系
category: Java
tags: [Java, 异常]
keywords: Java,异常
excerpt: Java 异常体系
---

Java 中，受检查异常 和 不受检查异常的区别？

受检查异常编译器在编译期间检查。对于这种异常，方法强制处理或者通过 throws 子句声明。其中一种情况是 `Exception` 的子类但不是 RuntimeException 的子类。非受检查是 RuntimeException 的子类，在编译阶段不受编译器的检查。

![](/assets/images/2025/Java_Exception.jpg)
