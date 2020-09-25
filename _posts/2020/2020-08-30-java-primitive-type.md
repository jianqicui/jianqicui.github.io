---
layout: post
title: Java 基本数据类型
category: Java
tags: [Java, 基本数据类型]
keywords: Java,基本数据类型
excerpt: Java 为基本类型提供语言级别的支持，即已经在 Java 中预定义，用相应的保留关键字表示。基本类型是单个值，而不是复杂的对象，基本类型不是面向对象的，主要出去效率方面的考虑，但是同时也提供基本类型的对象版本，即基本类型的包装器（wrapper）。
---

Java 语言是静态类型的（statical typed)，也就是说所有变量和表达式的类型再编译时就已经完全确定。由于是 statical typed，导致 Java 语言也是强类型（Strong typed）的。强类型意味着每个变量都具有一种类型，每个表达式具有一种类型，并且每种类型都是严格定义的，类型限制了变量可以 hold 哪些值，表达式最终产生什么值。同时限制了这些值可以进行的操作类型以及操作的具体方式。所有的赋值操作，无论是显式的还是在方法调用中通过参数传递，都要进行类型兼容性检查。

## 数据类型

在 Java 源代码中，每个变量都必须声明一种类型（type）。有两种类型：primitive type 和 reference type。引用类型引用对象（reference to object），而基本类型直接包含值（directly contain value）。primitive types 包括 boolean 类型以及数值类型（numeric types）。numeric types 又分为整型（integer types）和浮点型（floating-point type）。整型有 5 种：byte short int long char（char 本质上是一种特殊的 int）。浮点类型有 float 和 double。关系整理一下如下图：

![](/assets/images/2020/Java_Data_Types.png)

对象是动态创建的类实例或者动态创建的数组。The value of reference types are references to objects，而引用一般是指内存地址。所有的对象（包括数组）支持 Object 类中定义的方法。

null 是一种特殊的 type，但是你不能声明一个变量为 null 类型，null type 的唯一取值就是 null。null 可以负值给任意的引用类型或者转化成任意的引用类型。在实践中，一般把 null 当做字面值（literal），这个字面值可是是任意的引用类型。

## 基本类型

| 名称 | 类型 | 字节 | 默认值 | 取值范围 |
| ---- | ---- | ---- | ---- | ---- |
| byte | 整型 | 1 | 0 | -128~127 |
| short | 整型 | 2 | 0 | -32768~32767 |
| int | 整型 | 4 | 0 | -2147483648~2147483647 |
| long | 整型 | 8 | 0 | -9223372036854774808~9223372036854774807 |
| float | 浮点型 | 4 | 0.0 | 3.402823e+38~1.401298e-45（e+38 表示乘以 10 的 38 次方，而 e-45 表示乘以 10 的负 45 次方）|
| double | 浮点型 | 8 | 0.0 | 1.797693e+308~4.9000000e-324 |
| char | 文本型 | 2 | \u000 | 0~216-1 |
| boolean | 布尔型 | 1 | false | true/false |

在通常情况下，如果 Java 中出现了一个整数数字比如 35，那么这个数字就是 int 型的。如果我们希望它是 byte 型的，可以在数据后加上大写的 B：35B，表示它是 byte 型的。同样的 35S 表示 short 型，35L 表示 long 型的，表示 int 我们可以什么都不用加，但是如果要表示 long 型的，就一定要在数据后面加“L”。

double 型比 float 型存储范围更大，精度更高，所以通常的浮点型的数据在不声明的情况下都是 double 型的，如果要表示一个数据是 float 型的，可以在数据后面加上“F”。

浮点型的数据是不能完全精确的，所以有的时候在计算的时候可能会在小数点最后几位出现浮动，这是正常的。

## 基本类型之间的转换

### 自动类型转换

1. 两种类型是彼此兼容的

2. 转换后的目标类型占的空间范围一定要大于被转化的源类型

由低字节向高字节自动转换（黑线表示无数据丢失的自动数据转换，红线表示转换中可能发生精度丢失）

![](/assets/images/2020/Java_Primitive_Types_Conversion.png)

### 强制类型转换

将容纳更多信息的数据类型转换成一个容量更小的数据类型，可能存在精度损失的风险，编译器要求程序员进行强制类型转换。

强制转换过程中可能发生数据溢出，必须警惕。

```
int a = (int)3.14;
```

## 数据类型自动提升

* 如果两个操作数其中有一个是 double 类型，另一个操作就会转换为 double 类型。

* 否则，如果其中一个操作数是 float 类型，另一个将会转换为 float 类型。

* 否则，如果其中一个操作数是 long 类型，另一个会转换为 long 类型。

* 否则，两个操作数都转换为 int 类型。

## 参考

[https://www.cnblogs.com/liangxiaofeng/p/5721506.html](https://www.cnblogs.com/liangxiaofeng/p/5721506.html)

[https://www.cnblogs.com/mataoshou/p/10819586.html](https://www.cnblogs.com/mataoshou/p/10819586.html)

[https://www.cnblogs.com/tyyt/p/10398270.html](https://www.cnblogs.com/tyyt/p/10398270.html)
