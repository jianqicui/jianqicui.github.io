---
layout: post
title: JVM 性能调优监控工具之 JConsole
category: Java
tags: [JVM, 性能, 调优, 监控, JConsole]
keywords: JVM,性能,调优,监控,JConsole
excerpt: JConsole 图形用户界面是符合 Java 管理扩展（JMX）规范的监视工具，可以监测有关在 Java 平台上运行的应用程序的性能和资源消耗的信息。
---

## 介绍

JConsole 图形用户界面是符合 Java 管理扩展（JMX）规范的监视工具，可以监测有关在 Java 平台上运行的应用程序的性能和资源消耗的信息。

**启动 JConsole**

```
jconsole [ options ] [ connection ... ]
```

## 本地监控

```
% jconsole processID
```

![](/assets/images/2020/JConsole_Local_Connect.gif)

## 远程监控

```
％ jconsole hostName：portNum
```

![](/assets/images/2020/JConsole_Remote_Connect.gif)

### 远程监控配置

设置此属性注册了 Java VM 平台的 MBean 并通过专用接口发布了远程方法调用（RMI）连接器，以允许 JMX 客户端应用程序监视本地 Java 平台，即与 JMX 客户机在同一台机器上运行的 Java VM。

```
-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=8999
\-Dcom.sun.management.jmxremote.ssl=false
\-Dcom.sun.management.jmxremote.authenticate=true
\-Dcom.sun.management.jmxremote.password.file=../conf/jmxremote.password
\-Dcom.sun.management.jmxremote.access.file=../conf/jmxremote.access
```

## 功能

### 选项卡

* 概述（Overview）：显示有关 Java VM 和监视值的概述信息。

* 内存（Memory）：显示有关内存使用的信息。

* 线程（Threads）：显示有关线程使用的信息。

* 类（Classes）：显示有关加载类的信息。

* VM（VM Summary）：显示有关 Java VM 的信息。

* MBeans（MBeans）：显示有关 MBean 的信息。

### 概述信息

“概述”选项卡显示有关 CPU 使用情况，内存使用情况，线程计数以及 Java VM 中加载的类的图形监视信息，均在单个屏幕中。

![](/assets/images/2020/JConsole_Tabs_Overview.gif)

### 内存选项卡

“内存”选项卡提供有关内存消耗和内存池的信息。

![](/assets/images/2020/JConsole_Tabs_Memory.gif)

### 线程选项卡

“线程”选项卡提供有关线程使用的信息。

![](/assets/images/2020/JConsole_Tabs_Thread.gif)

左下角的线程列表列出了所有活动线程。如果在“过滤器”字段中输入字符串，“线程”列表将只显示名称包含您输入的字符串的线程。单击“线程”列表中的线程名称，以显示有关该线程的信息，包括线程名称，状态和堆栈跟踪。

检测死锁按钮将检测涉及对象监视器和 java.util.concurrent 可自动同步器的死锁周期。

### 类选项卡

“类”选项卡显示有关类加载的信息。

![](/assets/images/2020/JConsole_Tabs_Class.gif)

红线是加载的类的总数（包括随后卸载的类）。蓝线是当前加载的类的数量。

选项卡底部的“详细信息”部分显示自 Java VM 启动以来加载的类的总数，当前加载的数量和卸载的数量。您可以通过选中右上角的复选框将类加载跟踪设置为详细输出。

### VM 概要选项卡

“VM 概要”选项卡提供有关 Java VM 的信息。

![](/assets/images/2020/JConsole_Tabs_VM_Summary.gif)

## 参考

[https://blog.51cto.com/dba10g/1916416](https://blog.51cto.com/dba10g/1916416)
