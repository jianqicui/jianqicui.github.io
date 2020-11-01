---
layout: post
title: String 字符串的最大长度
category: Java
tags: [Java, String]
keywords: Java,String
excerpt: String 类型的对象，他们是有长度限制的，String 对象并不能“存储”无限长度的字符串。关于 String 的长度限制要从编译时限制和运行时限制两方面考虑。
---

String 字符串的最大长度是多少？

在学习和开发过程中，我们经常会讨论 short，int 和 long 这些基本数据类型的取值范围，但是对于 String 类型我们好像很少注意它的“取值范围”。那么对于 String 类型，它到底有没有长度限制呢？

其实 String 类型的对象，他们是有长度限制的，String 对象并不能“存储”无限长度的字符串。关于 String 的长度限制要从编译时限制和运行时限制两方面考虑。

## 编译期限制

有 JVM 虚拟机相关知识的同学肯定知道，下面定义的字符串常量“自由”会被放入方法区的常量池中。

```
String s = "自由";
System.out.println(s);
```

Stirng 长度之所以会受限制，是因 JVM 规范对常量池有所限制。常量池中的每一种数据项都有自己的类型。Java 中的 UTF-8 编码的 Unicode 字符串在常量池中以 CONSTANT_Utf8 类型表示。

CONSTANT_Utf8 的数据结构如下：

```
CONSTANT_Utf8_info {
    u1 tag;
    u2 length;
    u1 bytes[length];
}
```

我们重点关注下长度为 length 的那个 bytes 数组，这个数组就是真正存储常量数据的地方，而 length 就是数组可以存储的最大字节数。length 的类型是 u2，u2 是无符号的 16 位整数，因此理论上允许的的最大长度是 2^16-1=65535。所以上面 byte 数组的最大长度可以是 65535。

```
// 65535 个 d，编译报错
String s = "dd..dd";

// 65534 个 d，编译通过
String s1 = "dd..d";
```

上面的列子中长度为 65535 的字符串 s 还是编译失败了，但是长度为 65534 的字符串 s1 编译是成功的。这个好像和我们刚刚的结论不符合。

其实，这时 Javac 编译器的额外限制。在 Javac 的源代码中可以找到以下代码：

```
private void checkStringConstant(DiagnosticPosition var1, Object var2) {
    if (this.nerrs == 0 && var2 != null && var2 instanceof String &&   ((String)var2).length() >= 65535) {
        this.log.error(var1, "limit.string", new Object[0]);
        ++this.nerrs;
    }
}
```

代码中可以看出，当参数类型为 String，并且长度大于等于 65535 的时候，就会导致编译失败。

这里需要重点强调下的是：String 的限制并不是对字符串长度的限制，而是对字符串底层存储的限制。这句话可能比较抽象，下面举个列子就清楚了。

Java 中的字符常量都是使用 UTF8 编码的，UTF8 编码使用 1~4 个字节来表示具体的 Unicode 字符。所以有的字符占用一个字节，而我们平时所用的大部分中文都需要 3 个字节来存储。

```
// 65534 个字母，编译通过
String s1 = "dd..d";

// 21845 个中文”自“，编译通过
String s2 = "自自...自";

// 一个英文字母 d 加上 21845 个中文”自“，编译失败
String s3 = "d自自...自";
```

对于 s1，一个字母 d 的 UTF8 编码占用一个字节，65534 字母占用 65534 个字节，长度是 65534，也没超过 Javac 的限制，所以可以编译通过。

对于 s2，一个中文占用 3 个字节，21845 个正好占用 65535 个字节，而且字符串长度是 21845，并没有超过 Javac 对长度的限制，所以可以编译通过。

对于 s3，一个英文字母 d 加上 21845 个中文”自“占用 65536 个字节，超过了最常限制，编译失败。

## 运行时限制

String 运行时的限制主要体现在 String 的构造函数上。下面是 String 的一个构造函数：

```
public String(char value[], int offset, int count) {
    ...
}
```

上面的 count 值就是字符串的最大长度。在 Java 中，int 的最大长度是 2^31-1。所以在运行时，String 的最大长度是 2^31-1。

但是这个也是理论上的长度，实际的长度还要看你 JVM 的内存。我们来看下，最大的字符串会占用多大的内存。

```
(2^31-1)*2*16/8/1024/1024/1024 = 4GB
```

所以在最坏的情况下，一个最大的字符串要占用 4GB 的内存。如果你的虚拟机不能分配这么多内存的话，会直接报错的。

JDK9 以后对 String 的存储进行了优化。底层不再使用 char 数组存储字符串，而是使用 byte 数组。对于 LATIN1 字符的字符串可以节省一倍的内存空间。

## 简单总结

String 的长度是有限制的。

* 编译期的限制：字符串的 UTF8 编码值的字节数不能超过 65535，字符串的长度不能超过 65534。

* 运行时限制：字符串的长度不能超过 2^31-1，占用的内存数不能超过虚拟机能够提供的最大值。

## 参考

[https://www.cnblogs.com/54chensongxia/p/13640352.html](https://www.cnblogs.com/54chensongxia/p/13640352.html)
