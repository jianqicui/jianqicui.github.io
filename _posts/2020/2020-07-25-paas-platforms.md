---
layout: post
title: PaaS 管理平台：OpenShift vs CloudFoundry
category: Cloud Computing
tags: [PaaS, OpenShift, CloudFoundry]
keywords: PaaS 管理平台,OpenShift,CloudFoundry
excerpt: OpenShift vs CloudFoundry
---

云开发平台实现的目标都类似，但仍然需要为企业独特的业务和开发需求选择合适的平台。两种最常见的开源平台即服务（PaaS）工具是 Red Hat 公司的 OpenShift，以及 Pivotal 软件公司的 CloudFroundry。要确定哪一个工具更适合，需要评估三个重要的关键特性：所支持的编程语言、架构以及如何与第三方工具集成。

## 语言支持和功能

企业希望选择一个可以支持当前和未来的语言偏好的开源 PaaS 工具。OpenShift 使用模块的概念，又叫做 cartridges，支持 Java、 PHP、Python、Perl、Node.js，数据库语言则支持 MySQL、MongoDB 和 PostgreSQL。另外，OpenShift 还支持诸如 Jenkins 和 Cron 这样的工具，和包括 Oracle 业务流程管理套件以及 Red Hat Jboss 业务规则管理系统（BRMS）。

与此相比，Cloud Foundry 对语言的支持是以 buildpack 的形式或者是用来编译平台应用程序的脚本。这些 buildpack 包括 Java、Node.js、 Ruby、 binary、Go、PHP、Python 以及其他。用户还可以选择编写自己的 buildpack。

接下来，考虑一个开源 PaaS 工具里所需要的基本特性和功能。记住，每种工具都有开源和商业的版本，同时还有托管和本地的不同。例如，Red Hat 提供 OpenShift 在线版和 OpenShift 企业版，后者是托管在用户的数据中心。两者都提供自服务的开发平台，多语言支持、自动化、协作、容器移植性和对不同云基础架构的支持。

Cloud Foundry 提供安全性、监控和管理、容器移植性、混合云支持、自动更新的功能，以及数据库、分析器和其他中间件的服务集成。

## 架构和集成

开源 PaaS 产品通常由多个模块或组件构成，因此 IT 决策人员要核查每个平台的系统架构。例如，Cloud Foundry 包括一个路由器、认证服务器、应用生命周期管理器、应用程序执行引擎、服务代理、消息，以及指标和日志的元素。

OpenShift 使用的架构同 Cloud Foundry 大相径庭。它由单个节点组成，以容纳应用程序代码和服务，同时还有一系列的单独代理来管理节点和提供服务。除此之外，OpenShift 的架构还包括一个消息系统将节点和代理绑定到一起，并且使用 RESTful 的 API 同外部工具整合。

开源开发平台通常需要同其他工具整合来提供全套的功能。例如，Cloud Foundry 通常同以下的平台工具集成使用：

* Diego：运行环境：提供共享内存，工作负载调度和不同的容器格式。

* Lattice：一个 Pivotal 工具用来在一个云集群中运行容器化的工作负载。Lattice 为 Cloud Foundry 提供路由，健康度，执行，存储和日志的功能。

* Bosh：一个用于云服务的版本控制，部署和生命周期管理的开源工具。

同样，OpenShift 也集成并依赖于几个工具，包括 RHC 客户端工具，主要用于创建，部署和管理应用，以及使用 JBoss Developer Studio 创建基于 Web 的应用。

除了以上提到的开源工具，企业还可以考虑亚马逊 Web 服务、Google App Engine 和微软的 Azure 提供的公有云开发工具。

## 参考

[https://blog.csdn.net/weixin_34261739/article/details/90385320](https://blog.csdn.net/weixin_34261739/article/details/90385320)
