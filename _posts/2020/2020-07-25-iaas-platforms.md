---
layout: post
title: IaaS 管理平台：OpenStack vs CloudStack
category: Cloud Computing
tags: [IaaS, OpenStack, CloudStack]
keywords: IaaS 管理平台,OpenStack,CloudStack
excerpt: OpenStack vs CloudStack
---

OpenStack 是目前最大和最活跃的开源云计算项目，OpenStack 已经获得了很多的支持，包括很多大牌厂商，例如 Rackspace、戴尔、惠普、思科、VMware 和红帽等。与此同时，另外一个开源云计算项目 CloudStack 也正迅猛的增长，对 OpenStack 发起挑战，目前主要受到思杰（Citrix）和其他较小厂商的支持。

CloudStack 是一个开源的具有高可用性及扩展性的云计算平台。2012 年 4 月 Ctrtix 宣布将 CloudStack 加入 Apache 软件基金会，成为 Apache 许可下的完全开源软件。可以加速高伸缩性的公共和私有云（基础设施即服务）的部署、管理、配置。提到开源的云计算平台，相信大家首先想到的可能是 OpenStack，目前国内的几家云计算平台如阿里云、盛大云以及新浪 SAE 貌似都基于 OpenStack 做了二次开发。OpenStack 由 NASA 和 Rackspace 合作研发的的云计算平台，以 Apache 许可证授权，旨在为公有云及私有云的建设与管理提供软件的自由软件和开源项目。

## OpenStack 和 CloudStack 的比较

| 比较 | OpenStack | CloudStack |
| ---- | ---- | ---- |
| 服务类型 | Iaas | Iaas |
| 授权协议 | Apache 2.0 | Apache 2.0 |
| 许可证 | 不需要 | 不需要 |
| 动态资源调配 | 无现成功能，需要通过 Nova-Scheduler 组件实现 | 主机 Maintainance 模式下自动迁移VM |
| VM模板 | 支持 | 支持 |
| VM Console | 支持 | 支持 |
| 开发语言 | Python | Java |
| 用户界面 | DashBoard，较简单 | Web Console，功能较完善 |
| 负载均衡 | 软件负载均衡（Nova-Network或Openstack Load Balance API）、硬件负载均衡 | 软件负载均衡（Virtual Router）、硬件负载均衡 |
| 虚拟化技术 | XenServer，Oracle VM，ESX/ESXi，KVM，LXC 等 | XenServer，Oracl VM，vShpere，KVM，Bare Metal |
| 最小部署 | 支持 All in one | 一个管理节点，一个主机节点 |
| 支持数据库 | PostgreSQL，MySQL，SQLite | MySQL |
| 组件 | Nova，Glance，Keystone，Horizon，Swift | Console Proxy VM，Second Storage VM，Virtual Router VM，HostAgent，Management Server |
| 网络形版 | VLAN，FLAT，FlatDHCP | Isolation (VLAN)，Share |
| 版本问题 | 存在各个版本兼容性问题 | 版本发布稳定，不存在兼容性问题 |
| VLAN | 支持 VLAN 间互访 | 不能 VLAN 间互访 |

## 参考

[https://blog.csdn.net/carolzhang8406/article/details/56480024](https://blog.csdn.net/carolzhang8406/article/details/56480024)
