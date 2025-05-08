---
layout: post
title: Serializable 与 Externalizable 的区别？
category: Java
tags: [Java, Serializable, Externalizable]
keywords: Java,Serializable,Externalizable
excerpt: Serializable 与 Externalizable 的区别？
---

Java 中，`Serializable` 与 `Externalizable` 的区别？

`Serializable` 接口是一个序列化 Java 类的接口，以便于它们可以在网络上传输或者可以将它们的状态保存在磁盘上，是 JVM 内嵌的默认序列化方式，成本高、脆弱而且不安全。`Externalizable` 允许你控制整个序列化过程，指定特定的二进制格式，增加安全机制。
