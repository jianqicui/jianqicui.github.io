---
layout: post
title: UNIX 和 Linux 的关系及区别
category: Linux
tags: [UNIX, Linux]
keywords: UNIX,Linux
excerpt: 在目前主流的操作系统中，UNIX 诞生于 20 世纪 60 年代末，Windows 诞生于 20 世纪 80 年代中期，Linux 诞生于 20 世纪 90 年代初，可以说 UNIX 是操作系统中的"老大哥"，后来的 Windows 和 Linux 都参考了 UNIX。
---

UNIX 与 Linux 之间的关系是一个很有意思的话题。在目前主流的操作系统中，UNIX 诞生于 20 世纪 60 年代末，Windows 诞生于 20 世纪 80 年代中期，Linux 诞生于 20 世纪 90 年代初，可以说 UNIX 是操作系统中的"老大哥"，后来的 Windows 和 Linux 都参考了 UNIX。

## UNIX 历史

UNIX 操作系统由贝尔实验室的肯•汤普森（Ken Thompson）和丹尼斯•里奇（Dennis Ritchie）发明。目标是开发一种交互式的、具有多道程序处理能力的分时操作系统，以取代当时广泛使用的批处理操作系统。

## Linux 往事

Linux 内核最初是由李纳斯•托瓦兹（Linus Torvalds）在赫尔辛基大学读书时出于个人爱好而编写的，当时他觉得教学用的迷你版 UNIX 操作系统 Minix 太难用了，于是决定自己开发一个操作系统。第 1 版本于 1991 年 9 月发布，当时仅有 10000 行代码。

李纳斯•托瓦兹没有保留 Linux 源代码的版权，公开了代码，并邀请他人一起完善 Linux。与 Windows 及其他有专利权的操作系统不同，Linux 开放源代码，任何人都可以免费使用它。

## UNIX 与 Linux 的关系

二者的关系，不是大哥和小弟，"UNIX 是 Linux 的父亲"这个说法更怡当。之所以要介绍它们的关系，是因为要告诉读者，在学习的时候，其实 Linux 与 UNIX 有很多的共通之处，简单地说，如果你已经熟练掌握了 Linux，那么再上手使用 UNIX 会非常容易。

二者也有两个大的区别：

* UNIX 系统大多是与硬件配套的，也就是说，大多数 UNIX 系统如 AIX、HP-UX 等是无法安装在 x86 服务器和个人计算机上的，而 Linux 则可以运行在多种硬件平台上。

* UNIX 是商业软件，而 Linux 是开源软件，是免费、公开源代码的。

## UNIX/Linux 系统结构

UNIX/Linux 系统可以地抽象为 3 个层次。底层是 UNIX/Linux 操作系统，即系统内核（Kernel）；中间层是 Shell 层，即命令解释层；高层则是应用层。

![](/assets/images/2020/Architecture_Unix_Linux.jpg)

### 内核层
内核层是 UNIX/Linux 系统的核心和基础，它直接附着在硬件平台之上，控制和管理系统内各种资源（硬件资源和软件资源），有效地组织进程的运行，从而扩展硬件的功能，提高资源的利用效率，为用户提供方便、高效、安全、可靠的应用环境。

### Shell 层
Shell 层是与用户直接交互的界面。用户可以在提示符下输入命令行，由 Shell 解释执行并输出相应结果或者有关信息，所以我们也把 Shell 称作命令解释器，利用系统提供的丰富命令可以快捷而简便地完成许多工作。

### 应用层
应用层提供基于 X Window 协议的图形环境。X Window 协议定义了一个系统所必须具备的功能，系统能满足此协议，便可称为 X Window。

X Window 与微软的 Windows 图形环境有很大的区别：

* UNIX/Linux 系统与 X Window 没有必然捆绑的关系，也就是说，UNIX/Linux 可以安装 X Window，也可以不安装；而微软的 Windows 图形环境与内核捆绑密切。

* UNIX/Linux 系统不依赖图形环境，依然可以通过命令行完成 100% 的功能，而且因为不使用图形环境还会节省大量的系统资源。

## 参考

[http://c.biancheng.net/view/707.html](http://c.biancheng.net/view/707.html)
