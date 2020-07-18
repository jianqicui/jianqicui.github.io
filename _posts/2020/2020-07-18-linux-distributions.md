---
layout: post
title: Linux发行版本
category: Linux
tags: [Linux, Distributions]
keywords: Linux,发行版本
excerpt: Linux发行版本
---

新手往往会被Linux众多的发行版本搞得一头雾水，我们首先来解释一下这个问题。

从技术上来说，李纳斯•托瓦兹开发的Linux只是一个内核。内核指的是一个提供设备驱动、文件系统、进程管理、网络通信等功能的系统软件，内核并不是一套完整的操作系统，它只是操作系统的核心。一些组织或厂商将Linux内核与各种软件和文档包装起来，并提供系统安装界面和系统配置、设定与管理工具，就构成了Linux的发行版本。

在Linux内核的发展过程中，各种Linux发行版本起了巨大的作用，正是它们推动了Linux的应用，从而让更多的人开始关注Linux。因此，把Red Hat、Ubuntu、SUSE等直接说成Linux其实是不确切的，它们是Linux的发行版本，更确切地说，应该叫作“以Linux为核心的操作系统软件包”。

Linux的各个发行版本使用的是同一个Linux内核，因此在内核层不存在什么兼容性问题，每个版本有不一样的感觉，只是在发行版本的最外层（由发行商整合开发的应用）才有所体现。

Linux的发行版本可以大体分为两类：

* 商业公司维护的发行版本，以著名的Red Hat为代表；

* 社区组织维护的发行版本，以 Debian 为代表。

很难说大量Linux版本中哪一款更好，每个版本都有自己的特点。下面为大家介绍几款常用的Linux发行版本。

## Red Hat Linux

![](/assets/images/2020/redhat.jpg)

Red Hat（红帽公司）创建于1993年，是目前世界上资深的Linux厂商，也是最获认可的Linux品牌。

Red Hat公司的产品主要包括RHEL（Red Hat Enterprise Linux，收费版本）和CentOS（RHEL的社区克隆版本，免费版本）、Fedora Core（由Red Hat桌面版发展而来，免费版本）。

Red Hat是在国内使用人群最多的Linux版本，资料丰富，如果你有什么不明白的地方，则容易找到人来请教，而且大多数Linux教程是以 Red Hat为例来讲解的。

## Ubuntu Linux

![](/assets/images/2020/ubuntu.jpg)

Ubuntu基于知名的Debian Linux发展而来，界面友好，容易上手，对硬件的支持非常全面，是目前最适合做桌面系统的Linux发行版本，而且Ubuntu的所有发行版本都免费提供。

Ubuntu的创始人Mark Shuttleworth是非常具有传奇色彩的人物。他在大学毕业后创建了一家安全咨询公司，1999年以5.75亿美元被收购，由此一跃成为南非最年轻有为的本土富翁。作为一名狂热的天文爱好者，Mark Shuttleworth于2002年自费乘坐俄罗斯联盟号飞船，在国际空间站中度过了8天的时光。之后，Mark Shuttleworth创立了 Ubuntu社区，2005年7月1日建立了Ubuntu 基金会，并为该基金会投资1000万美元。他说，太空的所见正是他创立Ubuntu的精神之所在。如今，他最热衷的事情就是到处为自由开源的Ubuntu进行宣传演讲。

## SuSE Linux

![](/assets/images/2020/suse.jpg)

SuSE Linux以Slackware Linux为基础，原来是德国的SuSE Linux AG公司发布的Linux版本，1994年发行了第一版，早期只有商业版本，2004年被Novell公司收购后，成立了OpenSUSE社区，推出了自己的社区版本OpenSUSE。

SuSE Linux在欧洲较为流行，在国内也有较多应用。值得一提的是，它吸取了Red Hat Linux的很多特质。

SuSE Linux可以非常方便地实现与Windows的交互，硬件检测非常优秀，拥有界面友好的安装过程、图形管理工具，对于终端用户和管理员来说使用非常方便。

## Gentoo Linux

![](/assets/images/2020/gentoo.jpg)

Gentoo最初由Daniel Robbins（FreeBSD 的开发者之一）创建，首个稳定版本发布于2002年。Gentoo是所有Linux发行版本里安装最复杂的，到目前为止仍采用源码包编译安装操作系统。

不过，它是安装完成后最便于管理的版本，也是在相同硬件环境下运行最快的版本。自从Gentoo 1.0面世后，它就像一场风暴，给 Linux世界带来了巨大的惊喜，同时也吸引了大量的用户和开发者投入Gentoo Linux的怀抱。

有人这样评价Gentoo：快速、设计干净而有弹性，它的出名是因为其高度的自定制性（基于源代码的发行版）。尽管安装时可以选择预先编译好的软件包，但是大部分使用Gentoo的用户都选择自己手动编译。这也是为什么Gentoo适合比较有Linux使用经验的老手使用。

## 其他 Linux 发行版

除以上4种Linux发行版外，还有很多其他版本，以下罗列了几种常见的Linux发行版以及它们各自的特点：

| 版本名称 | 特点 | 软件包管理器 |
| -----| ---- | ---- | ---- |
| Debian Linux | 开放的开发模式，且易于进行软件包升级 | apt |
| Fedora Core | 拥有数量庞人的用户，优秀的社区技术支持，并且有许多创新 | up2date（rpm），yum（rpm） |
| CentOS | CentOS是一种对RHEL（Red Hat Enterprise Linux）源代码再编译的产物，并在RHEL的基础上修正了不少已知的漏洞 | rpm |
| SUSE Linux | 专业的操作系统，易用的YaST软件包管理系统 | YaST（rpm），第三方apt （rpm）软件库 |
| Mandriva | 操作界面友好，使用图形配置工具，庞大的社区技术支持，支持NTFS分区的大小变更 | rpm |
| KNOPPIX | 可以直接在CD上运行，具有优秀的硬件检测和适配能力，可作为系统的急救盘使用 | apt |
| Gentoo Linux | 高度的可定制性，使用手册完整 | portage |
| Ubuntu | 优秀已用的桌面环境，基于Debian构建 | apt |

## Linux 发行版本的选择

Linux的发行版本众多，在此不逐一介绍，下面给选择Linux发行版本犯愁的朋友一点建议：

1. 如果你需要的是一个服务器系统，而且已经厌烦了各种Linux的配置，只是想要一个比较稳定的服务器系统，那么建议你选择CentOS或RHEL。

2. 如果你只是需要一个桌面系统，而且既不想使用盗版，又不想花大价钱购买商业软件，不想自己定制，也不想在系统上浪费太多时间，则可以选择Ubuntu。

3. 如果你想深入摸索一下Linux各个方面的知识，而且还想非常灵活地定制自己的Linux系统，那就选择Gentoo吧，尽情享受Gentoo带来的自由快感。

4. 如果你对系统稳定性要求很高，则可以考虑FreeBSD。

5. 如果你需要使用数据库高级服务和电子邮件网络应用，则可以选择SuSE。

其实Linux的发行版本众多，但是系统的内核却系出同门，所以只要学会使用其中一种，即可触类旁通。

## 参考

[http://c.biancheng.net/view/710.html](http://c.biancheng.net/view/710.html)
