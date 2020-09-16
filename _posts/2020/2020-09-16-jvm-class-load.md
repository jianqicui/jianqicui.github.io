---
layout: post
title: JVM 类加载机制
category: Java
tags: [JVM, 类加载机制]
keywords: JVM,类加载机制
excerpt: 虚拟机把描述类的数据从 .class 文件加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以被虚拟机直接使用的 Java 类型，这就是虚拟机的类加载机制。
---

虚拟机把描述类的数据从 .class 文件加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以被虚拟机直接使用的 Java 类型，这就是虚拟机的类加载机制。

类加载器并不需要等到某个类被“首次主动使用”时再加载它，JVM 规范允许类加载器在预料某个类将要被使用时就预先加载它，如果在预先加载的过程中遇到了 .class 文件缺失或存在错误，类加载器必须在程序首次主动使用该类时才报告错误（LinkageError 错误）如果这个类一直没有被程序主动使用，那么类加载器就不会报告错误。

**加载 .class 文件的方式**

* 从本地系统中直接加载

* 通过网络下载 .class 文件

* 从 zip，jar 等归档文件中加载 .class 文件

* 从专有数据库中提取 .class 文件

* 将 Java 源文件动态编译为 .class 文件

## 类的生命周期

![](/assets/images/2020/Class_Lifecycle.png)

类加载的过程包括了加载、验证、准备、解析、初始化五个阶段。在这五个阶段中，加载、验证、准备和初始化这四个阶段发生的顺序是确定的，而解析阶段则不一定，它在某些情况下可以在初始化阶段之后开始，这是为了支持 Java 语言的运行时绑定（也成为动态绑定或晚期绑定）。另外注意这里的几个阶段是按顺序开始，而不是按顺序进行或完成，因为这些阶段通常都是互相交叉地混合进行的，通常在一个阶段执行的过程中调用或激活另一个阶段。

### 加载

查找并加载类的二进制数据加载时类加载过程的第一个阶段，在加载阶段，虚拟机需要完成以下三件事情：

* 通过一个类的全限定名来获取其定义的二进制字节流。

* 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。

* 在 Java 堆中生成一个代表这个类的 java.lang.Class 对象，作为对方法区中这些数据的访问入口。

相对于类加载的其他阶段而言，加载阶段（准确地说，是加载阶段获取类的二进制字节流的动作）是可控性最强的阶段，因为开发人员既可以使用系统提供的类加载器来完成加载，也可以自定义自己的类加载器来完成加载。

加载阶段完成后，虚拟机外部的二进制字节流就按照虚拟机所需的格式存储在方法区之中，而且在 Java 堆中也创建一个 java.lang.Class 类的对象，这样便可以通过该对象访问方法区中的这些数据。

### 连接

**验证：确保被加载的类的正确性**

验证是连接阶段的第一步，这一阶段的目的是为了确保 Class 文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。验证阶段大致会完成 4 个阶段的检验动作：

* 文件格式验证：验证字节流是否符合 Class 文件格式的规范；例如：是否以 0xCAFEBABE 开头、主次版本号是否在当前虚拟机的处理范围之内、常量池中的常量是否有不被支持的类型。

* 元数据验证：对字节码描述的信息进行语义分析（注意：对比 javac 编译阶段的语义分析），以保证其描述的信息符合 Java 语言规范的要求；例如：这个类是否有父类，除了 java.lang.Object 之外。

* 字节码验证：通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的。

* 符号引用验证：确保解析动作能正确执行。

验证阶段是非常重要的，但不是必须的，它对程序运行期没有影响，如果所引用的类经过反复验证，那么可以考虑采用 -Xverifynone 参数来关闭大部分的类验证措施，以缩短虚拟机类加载的时间。

**准备：为类的静态变量分配内存，并将其初始化为默认值**

准备阶段是正式为类变量分配内存并设置类变量初始值的阶段，这些内存都将在方法区中分配。对于该阶段有以下几点需要注意：

* 这时候进行内存分配的仅包括类变量（static），而不包括实例变量，实例变量会在对象实例化时随着对象一块分配在Java堆中。

* 这里所设置的初始值通常情况下是数据类型默认的零值（如 0、0L、null、false 等），而不是被在 Java 代码中被显式地赋予的值。

* 如果类字段的字段属性表中存在 ConstantValue 属性，即同时被 final 和 static 修饰，那么在准备阶段变量 value 就会被初始化为 ConstValue 属性所指定的值。

> 假设一个类变量的定义为：public static int value = 3；<br/>
那么变量value在准备阶段过后的初始值为 0，而不是 3。

> 假设上面的类变量value被定义为： public static final int value = 3；<br/>
编译时 Javac 将会为 value 生成 ConstantValue 属性，在准备阶段虚拟机就会根据 ConstantValue 的设置将 value 赋值为 3。

**解析：把类中的符号引用转换为直接引用**

解析阶段是虚拟机将常量池内的符号引用替换为直接引用的过程，解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符 7 类符号引用进行。符号引用就是一组符号来描述目标，可以是任何字面量。

直接引用就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄。

### 初始化

初始化，为类的静态变量赋予正确的初始值，JVM 负责对类进行初始化，主要对类变量进行初始化。此阶段是执行 \<clinit\> 方法的过程。在 Java 中对类变量进行初始值设定有两种方式：

* 声明类变量是指定初始值
* 使用静态代码块为类变量指定初始值

**JVM 初始化步骤**

* 假如这个类还没有被加载和连接，则程序先加载并连接该类

* 假如该类的直接父类还没有被初始化，则先初始化其直接父类

* 假如类中有初始化语句，则系统依次执行这些初始化语句

**类初始化时机：只有当对类的主动使用的时候才会导致类的初始化，类的主动使用包括以下六种：**

* 创建类的实例，也就是 new 的方式

* 访问某个类或接口的静态变量，或者对该静态变量赋值

* 调用类的静态方法

* 反射（如Class.forName()）

* 初始化某个类的子类，则其父类也会被初始化

* Java 虚拟机启动时被标明为启动类的类（Java Test），直接使用 java.exe 命令来运行某个主类

### 结束生命周期

在如下几种情况下，Java 虚拟机将结束生命周期

* 执行了 System.exit() 方法

* 程序正常执行结束

* 程序在执行过程中遇到了异常或错误而异常终止

* 由于操作系统出现错误而导致 Java 虚拟机进程终止

## 类加载器

把实现类加载阶段中的“通过一个类的全限定名来获取描述此类的二进制字节流”这个动作的代码模块称为“类加载器”。

将 class 文件二进制数据放入方法区内，然后在堆内（heap）创建一个 java.lang.Class 对象，Class 对象封装了类在方法区内的数据结构，并且向开发者提供了访问方法区内的数据结构的接口。

目前类加载器却在类层次划分、OSGi、热部署、代码加密等领域非常重要，我们运行任何一个 Java 程序都会涉及到类加载器。

### 类的唯一性和类加载器

对于任意一个类，都需要由加载它的类加载器和这个类本身一同确立其在Java虚拟机中的唯一性。

即使两个类来源于同一个 Class 文件，被同一个虚拟机加载，只要加载它们的类加载器不同，那这两个类也不相等。
这里所指的“相等”，包括代表类的 Class 对象的 equals() 方法、 isAssignableFrom() 方法、isInstance() 方法的返回结果，也包括使用 instanceof 关键字做对象所属关系判定等情况。

### 双亲委派模型

如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成，每一个层次的类加载器都是如此，因此所有的加载请求最终都应该传送到顶层的启动类加载器中，只有当父加载器反馈自己无法完成这个加载请求（它的搜索范围中没有找到所需的类）时，子加载器才会尝试自己去加载。

![](/assets/images/2020/Class_Loader.png)

这里类加载器之间的父子关系一般不会以继承（Inheritance）的关系来实现，而是都使用组合（Composition）关系来复用父加载器的代码。

双亲委派模型的工作流程是：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。

双亲委派机制:

* 当 AppClassLoader 加载一个 class 时，它首先不会自己去尝试加载这个类，而是把类加载请求委派给父类加载器 ExtClassLoader 去完成。

* 当 ExtClassLoader 加载一个 class 时，它首先也不会自己去尝试加载这个类，而是把类加载请求委派给 BootStrapClassLoader 去完成。

* 如果 BootStrapClassLoader 加载失败（例如在 $JAVA_HOME/jre/lib 里未查找到该 class），会使用 ExtClassLoader 来尝试加载。

* 若 ExtClassLoader 也加载失败，则会使用 AppClassLoader 来加载，如果 AppClassLoader 也加载失败，则会报出异常 ClassNotFoundException。

ClassLoader 源码分析：

```
public Class<?> loadClass(String name) throws ClassNotFoundException {
    return loadClass(name, false);
}

protected synchronized Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
    // 首先判断该类型是否已经被加载
    Class c = findLoadedClass(name);
    if (c == null) {
        // 如果没有被加载，就委托给父类加载或者委派给启动类加载器加载
        try {
            if (parent != null) {
                // 如果存在父类加载器，就委派给父类加载器加载
                c = parent.loadClass(name, false);
            } else {
                // 如果不存在父类加载器，就检查是否是由启动类加载器加载的类，通过调用本地方法native Class findBootstrapClass(String
                // name)
                c = findBootstrapClass0(name);
            }
        } catch (ClassNotFoundException e) {
            // 如果父类加载器和启动类加载器都不能完成加载任务，才调用自身的加载功能
            c = findClass(name);
        }
    }
    if (resolve) {
        resolveClass(c);
    }
    return c;
}
```

双亲委派模型意义：

* 系统类防止内存中出现多份同样的字节码

* 保证 Java 程序安全稳定运行

## 类的加载方式

类加载有三种方式：

* 命令行启动应用时候由JVM初始化加载

* 通过 Class.forName() 方法动态加载

* 通过 ClassLoader.loadClass() 方法动态加载

例子：

```
public class ClassLoaderTest {
    public static void main(String[] args) throws ClassNotFoundException {
        ClassLoader loader = ClassLoaderTest.class.getClassLoader();
        System.out.println(loader);
        // 使用ClassLoader.loadClass()来加载类，不会执行初始化块
        loader.loadClass("Test");
        // 使用Class.forName()来加载类，默认会执行初始化块
        // Class.forName("Test");
        // 使用Class.forName()来加载类，并指定ClassLoader，初始化时不执行静态块
        // Class.forName("Test", false, loader);
    }

    public static class Test {
        static {
            System.out.println("静态初始化块执行了！");
        }
    }
}
```

分别切换加载方式，会有不同的输出结果。

Class.forName() 和 ClassLoader.loadClass() 区别

* Class.forName()：将类的 .class 文件加载到 jvm 中之外，还会对类进行解释，执行类中的 static 块。

* ClassLoader.loadClass()：只干一件事情，就是将 .class 文件加载到 jvm 中，不会执行 static 中的内容，只有在 newInstance 才会去执行 static 块。

* Class.forName(name, initialize, loader) 带参函数也可控制是否加载 static 块。并且只有调用了 newInstance() 方法采用调用构造函数，创建类的对象。

## 参考

[http://www.ityouknow.com/jvm/2017/08/19/class-loading-principle.html](http://www.ityouknow.com/jvm/2017/08/19/class-loading-principle.html)

[https://www.cnblogs.com/czwbig/p/11127222.html](https://www.cnblogs.com/czwbig/p/11127222.html)
