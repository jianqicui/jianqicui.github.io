---
layout: post
title: JVM 性能调优监控工具之 JVisualVM
category: Java
tags: [JVM, 性能, 调优, 监控, JVisualVM]
keywords: JVM,性能,调优,监控,JVisualVM
excerpt: JVisualVM 是 Netbeans 的 profile 子项目，已在 JDK6.0 update 7 中自带，能够监控线程，内存情况，查看方法的 CPU 时间和内存中的对象、已被 GC 的对象，反向查看分配的堆栈（如 100 个 String 对象分别由哪几个对象分配出来的）。
---

JVisualVM 是 Netbeans 的 profile 子项目，已在 JDK6.0 update 7 中自带，能够监控线程，内存情况，查看方法的 CPU 时间和内存中的对象、已被 GC 的对象，反向查看分配的堆栈（如 100 个 String 对象分别由哪几个对象分配出来的）。

## 查看运行环境及 JVM 参数

![](/assets/images/2020/JVisualVM_Overview.png)

## 查看 JVM 垃圾回收、进程等

主要是为了查看垃圾回收次数，频繁的垃圾回收对系统性能也有影响。

![](/assets/images/2020/JVisualVM_Monitor.png)

## 分析应用性能

主要是利用抽样器统计每个线程中，每个方法的执行时间，这样就能看到一次请求过程中，到底是哪个方法导致响应时间长。

**点开抽样器，点击 CPU，进行 CPU 抽样**

![](/assets/images/2020/JVisualVM_Performance_1.png)

**抽样一段时间后，点击快照，对从点击抽样开始的这段时间，进行 CPU 使用的统计**

![](/assets/images/2020/JVisualVM_Performance_2.png)

**然后就能看到这段时间里，每个方法被使用的总时间以及时间占比，调用堆栈可一直追踪至 native 方法**

![](/assets/images/2020/JVisualVM_Performance_3.png)

## 分析远程应用

如果要分析远程应用，需要远程服务暴露 JMX 服务，然后本地再用该工具进行连接。所以需要对远程服务的 JVM 启动参数进行配置。

**在 JVM 启动参数中增加 JMX 配置**

```
#建立链接的端口
JAVA_OPTS="-Dcom.sun.management.jmxremote.port=8090"
#不启用ssl
JAVA_OPTS="-Dcom.sun.management.jmxremote.ssl=false $JAVA_OPTS"
#不启用登陆验证
JAVA_OPTS="-Dcom.sun.management.jmxremote.authenticate=false $JAVA_OPTS"
#rmiServer
JAVA_OPTS="-Djava.rmi.server.hostname=10.137.126.42 $JAVA_OPTS"

#启动命令
java $JAVA_OPTS -jar jarName.jar  >/dev/null 2>&1 &
```

**注意服务器开放端口**

远程服务启动后，除配置端口外，还会开放两个随机端口，本地服务还会访问这两个随机端口，所以在有网络权限限制的时候，需要把本地服务器高位端口（10000-65535） 都申请允许访问。

**最后本地远程添加主机，建立 JMX 链接即可**

## 参考

[https://yq.aliyun.com/articles/693352](https://yq.aliyun.com/articles/693352)

[https://www.cnblogs.com/kongzhongqijing/articles/3625340.html](https://www.cnblogs.com/kongzhongqijing/articles/3625340.html)
