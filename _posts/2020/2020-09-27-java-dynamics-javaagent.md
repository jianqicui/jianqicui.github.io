---
layout: post
title: Java 动态性之 Java Agent
category: Java
tags: [JVM, 动态性, Java Agent]
keywords: JVM,动态性,Java Agent
excerpt: Java Agent 又叫做 Java 探针，Java Agent 是在 JDK1.5 引入的，是一种可以动态修改 Java 字节码的技术。
---

Java Agent 又叫做 Java 探针，Java Agent 是在 JDK1.5 引入的，是一种可以动态修改 Java 字节码的技术。Java 类编译之后形成字节码被 JVM 执行，在 JVM 在执行这些字节码之前获取这些字节码信息，并且通过字节码转换器对这些字节码进行修改，来完成一些额外的功能，这种就是 Java Agent 技术。

## Instrucment 与 Attach API

### Instrucment

JDK1.5 中增加了一个包 java.lang.instrucment，能够对 JVM 底层组件进行访问。在 JDK1.5 中，Instrument 要求在运行前利用命令行参数或者系统参数来设置代理类，在实际的运行之中，虚拟机在初始化之时（在绝大多数的 Java 类库被载入之前），Instrument 的设置已经启动，并在虚拟机中设置了回调函数，检测特定类的加载情况，并完成实际工作。

​在 JDK1.5 中，开发基于 Instrucment 的应用，需要以下几个步骤。

* 编写 premain 函数

* 打包 jar 文件

* 运行 agent

### Attach API

但是在实际的很多的情况下，我们没有办法在虚拟机启动之时就为其设定代理，这样实际上限制了 Instrucment 的应用。而 JDK1.6 的新特性改变了这种情况，通过 Java Tool API 中的 attach 方式，我们可以很方便地在运行过程中动态地设置加载代理类，以达到 Instrucment 的目的。

​在 JDK1.6 中，针对这点做了改进，开发者可以在 main 开始执行以后，再开启自己的 Instrucment 程序。

Attach API 不是 Java 的标准 API，而是 Sun 公司提供的一套扩展 API，用来向目标 JVM "附着"（Attach）代理工具程序的。有了它，开发者可以方便的监控一个 JVM，运行一个外加的代理程序，Sun JVM Attach API 功能上非常简单，仅提供了如下几个功能。

* 列出当前所有的 JVM 实例描述

* Attach 到其中一个 JVM 上，建立通信管道

* 让目标 JVM 加载 Agent

## Instrument Agent 两种加载方式

Instrument Agent 的加载有以下两种方式：

* JVM 在指定代理的方式下启动，此时 Instrument 实例会传递到代理类的 **premain** 方法。
* JVM 提供一种在启动之后的某个时刻启动代理的机制，此时 Instrument 实例会传递到代理类代码的 **agentmain** 方法。

**premain** 与 **agentmain** 的区别：

* premain 方式是 JDK1.5 引入的，agentmain 方式是 JDK1.6 引入的，JDK1.6 之后可以自行选择使用 premain 或者 agentmain。

* premain 需要通过命令行使用外部代理 jar 包，即 -javaagent:代理jar包路径。agentmain 则可以通过 attach 机制直接附着到目标 JVM 中加载代理，也就是使用 agentmain 方式下，操作 attach 的程序和被代理的程序可以是完全不同的两个程序。

### premain 方式

premain 方式编写步骤简单如下：

* 编写 premain 函数，包含下面两个方法的其中之一：

```
public static void premain(String agentArgs, Instrumentation inst);
public static void premain(String agentArgs);
```

如果两个方法都被实现了，那么带 Instrumentation 参数的优先级高一些，会被优先调用。agentArgs 是 premain 函数得到的程序参数，通过命令行参数传入。

* 定义一个 MANIFEST.MF 文件，必须包含 Premain-Class 选项，通常也会加入 Can-Redefine-Classes 和 Can-Retransform-Classes 选项。

* 将 premain 的类和 MANIFEST.MF 文件打成 jar 包。

* 使用参数 -javaagent:jar 包路径启动代理。

**代码示例**

TestMain.java

```
public class TestMain {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("Hello");
    }
}
```

TestAgent.java

```
public class TestAgent {

    public static void premain(String args, Instrumentation inst) throws Exception {
        System.out.println("Premain args:" + args);
        Class[] classes = inst.getAllLoadedClasses();
        for (Class clazz : classes) {
            System.out.println(clazz.getName());
        }
    }
}
```

TestAgent 类比较简单，最终它会在目标类的 Main 方法执行之前，执行 premain 方法，其主要动作是将以及加载的类打印出来。我们需要将这个类打包成 jar 文件，以便在目标 JVM 启动时候，以参数形式指定给它。

打成 jar 的同时，设定 MANIFEST.MF 文件的内容，告知目标 JVM 该如何处理。

```
Manifest-Version: 1.0
Premain-Class: TestAgent
Can-Redefine-Classes: true
Can-Retransform-Classes: true
```

用 jar 命令将 TestAgent 打包

```
编译 TestAgent
javac TestAgent.java

jar 打包
jar cvmf MANIFEST.MF xxx.jar TestAgent.class
```

启动 TestMain，并设置 javaagent 参数

```
编译 TestMain
javac TestMain.java

启动 TestMain
java -javaagent:xxx.jar TestMain
```

### agentmain 加载方式

agentmain 方式编写步骤简单如下：

* 编写 agentmain 函数，包含下面两个方法的其中之一：

```
public static void agentmain(String agentArgs, Instrumentation inst);
public static void agentmain(String agentArgs);
```

如果两个方法都被实现了，那么带 Instrumentation 参数的优先级高一些，会被优先调用。agentArgs 是 agentmain 函数得到的程序参数，通过命令行参数传入。

* 定义一个 MANIFEST.MF 文件，必须包含 Agent-Class 选项，通常也会加入Can-Redefine-Classes 和 Can-Retransform-Classes 选项。

* 将 agentmain 的类和 MANIFEST.MF 文件打成 jar 包。

* 通过 attach 工具直接加载 Agent。

**代理示例**

TestMain.java

```
public class TestMain {
    public static void main(String[] args) throws InterruptedException {
        while (true) {
            Thread.sleep(10000);
            new Thread(new WaitThread()).start();
        }
    }

    private static class WaitThread implements Runnable {
        @Override
        public void run() {
            System.out.println("Hello");
        }
    }
}
```

TestAgent.java

```
public class TestAgent {
    public static void agentmain(String args, Instrumentation inst) throws Exception {
        System.out.println("Agentmain args:" + args);
        Class[] classes = inst.getAllLoadedClasses();
        for (Class clazz : classes) {
            System.out.println(clazz.getName());
        }
    }
}
```

动态加载 agent 的情况下，被调用的是 agentmain 方法, 其会在 JVM load 的时候，被调用。

MANIFEST.MF

```
Manifest-Version: 1.0
Agent-Class: TestAgent
Can-Redefine-Classes: true
Can-Retransform-Classes: true
```

将类打包为 jar 包

```
编译 TestAgent
javac TestAgent.java

jar 打包
jar cvmf MANIFEST.MF xxx.jar TestAgent.class
```

动态附着到对应的 JVM 需要使用到 JDK 的 Attach API

Main.java

```
public class Main {

    public static void main(String[] args) throws Exception {
        // 获取当前系统中所有 运行中的 虚拟机
        System.out.println("TestSufMainAgent start...");
        String option = args[0];
        List<VirtualMachineDescriptor> list = VirtualMachine.list();
        if (option.equals("list")) {
            for (VirtualMachineDescriptor vmd : list) {
                // 如果虚拟机的名称为 xxx 则 该虚拟机为目标虚拟机，获取该虚拟机的 pid
                // 然后加载 agent.jar 发送给该虚拟机
                System.out.println(vmd.displayName());
            }
        } else if (option.equals("attach")) {
            String jProcessName = args[1];
            String agentPath = args[2];
            for (VirtualMachineDescriptor vmd : list) {
                if (vmd.displayName().equals(jProcessName)) {
                    VirtualMachine virtualMachine = VirtualMachine.attach(vmd.id());
                    virtualMachine.loadAgent(agentPath);
                }
            }
        }
    }
}
```

## Instrumentation 局限性

大多数情况下，使用 Instrumentation 都是使用其字节码插桩的功能，笼统说是类重转换的功能，但是有以下的局限性：

* premain 和 agentmain 两种方式修改字节码的时机都是类文件加载之后，就是说必须要带有 Class 类型的参数，不能通过字节码文件和自定义的类名重新定义一个本来不存在的类。这里需要注意的就是上面提到过的重新定义，刚才这里说的不能重新定义是指不能重新换一个类名，字节码内容依然能重新定义和修改，不过字节码内容修改后也要满足第二点的要求。

* 类转换其实最终都回归到类重定义 Instrumentation.retransformClasses() 方法，此方法有以下限制：

  1. 新类和老类的父类必须相同。

  2. 新类和老类实现的接口数也要相同，并且是相同的接口。

  3. 新类和老类访问符必须一致。新类和老类字段数和字段名要一致。

  4. 新类和老类新增或删除的方法必须是 private static/final 修饰的。

  5. 可以删除修改方法体。

实际中遇到的限制可能不止这些，遇到了再去解决吧。如果想要重新定义一全新类（类名在已加载类中不存在），可以考虑基于类加载器隔离的方式：创建一个新的自定义类加载器去通过新的字节码去定义一个全新的类。

## 参考

[https://zhuanlan.zhihu.com/p/135872794](https://zhuanlan.zhihu.com/p/135872794)

[https://www.cnblogs.com/LittleHann/p/4783581.html](https://www.cnblogs.com/LittleHann/p/4783581.html)

[https://segmentfault.com/a/1190000021278869](https://segmentfault.com/a/1190000021278869)
