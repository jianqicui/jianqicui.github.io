---
layout: post
title: JVM 常量池
category: Java
tags: [JVM, 常量池]
keywords: JVM,常量池
excerpt: JVM 中的常量池，实际上分为两种形态：静态常量池和运行时常量池。
---

JVM 中的常量池，实际上分为两种形态：静态常量池和运行时常量池。

## 静态常量池

静态常量池，即 *.class 文件中的常量池，class 文件中的常量池不仅仅包含字符串（数字）字面量，还包含类、方法的信息，占用 class 文件绝大部分空间。这种常量池主要用于存放两大类常量：字面量（Literal）和符号引用量（Symbolic References），字面量相当于 Java 语言层面常量的概念，如文本字符串，声明为 final 的常量值等，符号引用则属于编译原理方面的概念，包括了如下三种类型的常量：

* 类和接口的全限定名

* 字段名称和描述符

* 方法名称和描述符

## 运行时常量池

运行时常量池，则是 JVM 虚拟机在完成类装载操作后，将 class 文件中的常量池载入到内存中，并保存在方法区中，我们常说的常量池，就是指方法区中的运行时常量池。

运行时常量池相对于 class 文件常量池的另外一个重要特征是具备动态性，Java 语言并不要求常量一定只有编译期才能产生，也就是并非预置入 class 文件中常量池的内容才能进入方法区运行时常量池，运行期间也可能将新的常量放入池中，这种特性被开发人员利用比较多的就是 String 类的 intern() 方法。

String 的 intern() 方法会查找在常量池中是否存在一份 equal 相等的字符串，如果有则返回该字符串的引用，如果没有则添加自己的字符串进入常量池。

## 常量池的好处

常量池是为了避免频繁的创建和销毁对象而影响系统性能，其实现了对象的共享。

例如字符串常量池，在编译阶段就把所有的字符串文字放到一个常量池中。

1. 节省内存空间：常量池中所有相同的字符串常量被合并，只占用一个空间。

2. 节省运行时间：比较字符串时，== 比 equals() 快。对于两个引用变量，只用 == 判断引用是否相等，也就可以判断实际值是否相等。

## 基本类型包装类的常量池

Java 中基本类型的包装类的大部分都实现了常量池技术，这些类是 Byte，Short，Integer，Long，Character，Boolean，另外两种浮点数类型的包装类则没有实现。另外 Byte，Short，Integer，Long，Character 这 5 种整型的包装类也只是在对应值小于等于 127 时才可使用对象池，也即对象不负责创建和管理大于 127 的这些类的对象。以下是一些对应的测试代码：

```
public class Test {

    public static void main(String[] args) {
        //5种整形的包装类Byte,Short,Integer,Long,Character的对象
        //在值小于127时可以使用常量池
        Integer i1 = 127;
        Integer i2 = 127;
        System.out.println(i1 == i2);//输出true

        //值大于127时，不会从常量池中取对象
        Integer i3 = 128;
        Integer i4 = 128;
        System.out.println(i3 == i4);//输出false

        //Boolean类也实现了常量池技术
        Boolean bool1 = true;
        Boolean bool2 = true;
        System.out.println(bool1 == bool2);//输出true

        //浮点类型的包装类没有实现常量池技术
        Double d1 = 1.0;
        Double d2 = 1.0;
        System.out.println(d1 == d2);//输出false
    }
}
```

## 字符串常量池

接下来我们引用一些网络上流行的字符串常量池例子，然后借以讲解。

```
String s1 = "Hello";
String s2 = "Hello";
String s3 = "Hel" + "lo";
String s4 = "Hel" + new String("lo");
String s5 = new String("Hello");
String s6 = s5.intern();
String s7 = "H";
String s8 = "ello";
String s9 = s7 + s8;

System.out.println(s1 == s2);  // true
System.out.println(s1 == s3);  // true
System.out.println(s1 == s4);  // false
System.out.println(s1 == s9);  // false
System.out.println(s4 == s5);  // false
System.out.println(s1 == s6);  // true
```

首先说明一点，在 Java 中，直接使用 == 操作符，比较的是两个字符串的引用地址，并不是比较内容，比较内容请用 String.equals()。

s1 == s2这个非常好理解，s1、s2 在赋值时，均使用的字符串字面量，说白话点，就是直接把字符串写死，在编译期间，这种字面量会直接放入 class 文件的常量池中，从而实现复用，载入运行时常量池后，s1、s2 指向的是同一个内存地址，所以相等。

s1 == s3 这个地方有个坑，s3 虽然是动态拼接出来的字符串，但是所有参与拼接的部分都是已知的字面量，在编译期间，这种拼接会被优化，编译器直接帮你拼好，因此 String s3 = "Hel" + "lo"; 在 class 文件中被优化成 String s3 = "Hello"，所以 s1 == s3 成立。只有使用引号包含文本的方式创建的 String 对象之间使用 “+” 连接产生的新对象才会被加入字符串池中。

s1 == s4 当然不相等，s4 虽然也是拼接出来的，但 new String("lo") 这部分不是已知字面量，是一个不可预料的部分，编译器不会优化，必须等到运行时才可以确定结果，结合字符串不变定理，鬼知道 s4 被分配到哪去了，所以地址肯定不同。对于所有包含 new 方式新建对象（包括null）的“+”连接表达式，它所产生的新对象都不会被加入字符串池中。

配上一张简图理清思路：

![](/assets/images/2020/String_Pool_1.jpg)

s1 == s9 也不相等，道理差不多，虽然 s7、s8 在赋值的时候使用的字符串字面量，但是拼接成 s9 的时候，s7、s8 作为两个变量，都是不可预料的，编译器毕竟是编译器，不可能当解释器用，不能在编译期被确定，所以不做优化，只能等到运行时，在堆中创建 s7、s8 拼接成的新字符串，在堆中地址不确定，不可能与方法区常量池中的 s1 地址相同。

![](/assets/images/2020/String_Pool_2.png)

s4 == s5 已经不用解释了，绝对不相等，二者都在堆中，但地址不同。

s1 == s6 这两个相等完全归功于 intern 方法，s5 在堆中，内容为 Hello，intern 方法会尝试将 Hello 字符串添加到常量池中，并返回其在常量池中的地址，因为常量池中已经有了 Hello 字符串，所以 intern 方法直接返回地址；而 s1 在编译期就已经指向常量池了，因此 s1 和 s6 指向同一地址，相等。

**特例 1**

```
public static final String A = "ab"; // 常量A
public static final String B = "cd"; // 常量B
public static void main(String[] args) {
     String s = A + B;  // 将两个常量用+连接对s进行初始化
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 }
s等于t，它们是同一个对象
```

A 和 B 都是常量，值是固定的，因此 s 的值也是固定的，它在类被编译时就已经确定了。也就是说：String s = A+B; 等同于：String s = "ab"+"cd";

**特例2**

```
public static final String A; // 常量A
public static final String B;    // 常量B
static {   
     A = "ab";   
     B = "cd";   
 }   
 public static void main(String[] args) {   
    // 将两个常量用+连接对s进行初始化   
     String s = A + B;   
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 }
s不等于t，它们不是同一个对象
```

A 和 B 虽然被定义为常量，但是它们都没有马上被赋值。在运算出 s 的值之前，他们何时被赋值，以及被赋予什么样的值，都是个变数。因此 A 和 B 在被赋值之前，性质类似于一个变量。那么 s 就不能在编译期被确定，而只能在运行时被创建了。

## 总结

* 必须要关注编译期的行为，才能更好的理解常量池。

* 运行时常量池中的常量，基本来源于各个 class 文件中的常量池。

* 程序运行时，除非手动向常量池中添加常量（比如调用 intern 方法），否则 JVM 不会自动添加常量到常量池。

## 参考

[https://www.cnblogs.com/syp172654682/p/8082625.html](https://www.cnblogs.com/syp172654682/p/8082625.html)

[http://www.51gjie.com/java/111.html](http://www.51gjie.com/java/111.html)
