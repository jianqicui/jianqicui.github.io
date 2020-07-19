---
layout: post
title: 开源协议
category: Open Source
tags: [Open Source]
keywords: 开源协议
excerpt: 开源协议
---

开源软件在追求“自由”的同时，不能牺牲程序员的利益，否则将会影响程序员的创造激情，因此世界上现在有60多种被开源促进组织（Open Source Initiative）认可的开源许可协议来保证开源工作者的权益。

开源协议规定了你在使用开源软件时的权利和责任，也就是规定了你可以做什么，不可以做什么。

开源协议虽然不一定具备法律效力，但是当涉及软件版权纠纷时，开源协议也是非常重要的证据之一。

对于准备编写一款开源软件的开发人员，也非常建议先了解一下当前最热门的开源许可协议，选择一个合适的开源许可协议来最大限度保护自己的软件权益。

## GNU GPL（GNU General Public License，GNU通用公共许可证）

![](/assets/images/2020/gnu.jpg)

只要软件中包含了遵循GPL协议的产品或代码，该软件就必须也遵循GPL许可协议，也就是必须开源免费，不能闭源收费，因此这个协议并不适合商用软件。

遵循GPL协议的开源软件数量极其庞大，包括Linux系统在内的大多数的开源软件都是基于这个协议的。

**GPL开源协议的主要特点**

| 特点 | 说明 |
| ----| ---- |
| 复制自由 | 允许把软件复制到任何人的电脑中，并且不限制复制的数量。 |
| 传播自由 | 允许软件以各种形式进行传播。 |
| 收费传播 | 允许在各种媒介上出售该软件，但必须提前让买家知道这个软件是可以免费获得的；因此，一般来讲，开源软件都是通过为用户提供有偿服务的形式来盈利的。 |
| 修改自由 | 允许开发人员增加或删除软件的功能，但软件修改后必须依然基于GPL许可协议授权。 |

## BSD（Berkeley Software Distribution，伯克利软件发布版）协议

![](/assets/images/2020/bsd.jpg)

BSD协议基本上允许用户“为所欲为”，用户可以使用、修改和重新发布遵循该许可的软件，并且可以将软件作为商业软件发布和销售，前提是需要满足下面三个条件：

* 如果再发布的软件中包含源代码，则源代码必须继续遵循BSD许可协议。

* 如果再发布的软件中只有二进制程序，则需要在相关文档或版权文件中声明原始代码遵循了BSD协议。

* 不允许用原始软件的名字、作者名字或机构名称进行市场推广。

## Apache许可证版本（Apache License Version）协议

![](/assets/images/2020/apache.jpg)

Apache和BSD类似，都适用于商业软件。Apache协议在为开发人员提供版权及专利许可的同时，允许用户拥有修改代码及再发布的自由。

现在热门的Hadoop、Apache HTTP Server、MongoDB等项目都是基于该许可协议研发的，程序开发人员在开发遵循该协议的软件时，要严格遵守下面的四个条件：

* 该软件及其衍生品必须继续使用Apache许可协议。

* 如果修改了程序源代码，需要在文档中进行声明。

* 若软件是基于他人的源代码编写而成的，则需要保留原始代码的协议、商标、专利声明及其他原作者声明的内容信息。

* 如果再发布的软件中有声明文件，则需在此文件中标注Apache许可协议及其他许可协议。

## MIT（Massachusetts Institute of Technology）协议

目前限制最少的开源许可协议之一（比BSD和Apache的限制都少），只要程序的开发者在修改后的源代码中保留原作者的许可信息即可，因此普遍被商业软件所使用。

使用MIT协议的软件有PuTTY、X Window System、Ruby on Rails、Lua 5.0 onwards、Mono等。

## GUN LGPL（GNU Lesser General Public License，GNU宽通用公共许可证）

LGPL是GPL的一个衍生版本，也被称为GPL V2，该协议主要是为类库设计的开源协议。

LGPL允许商业软件通过类库引用（link）的方式使用LGPL类库，而不需要开源商业软件的代码。这使得采用LGPL协议的开源代码可以被商业软件作为类库引用并发布和销售。

但是如果修改LGPL协议的代码或者衍生品，则所有修改的代码，涉及修改部分的额外代码和衍生的代码都必须采用LGPL协议。因此LGPL协议的开源代码很适合作为第三方类库被商业软件引用，但不适合希望以LGPL协议代码为基础，通过修改和衍生的方式做二次开发的商业软件采用。

## 如何选择开源协议

乌克兰程序员Paul Bagwell画了一张分析图，说明应该怎么选择开源协议，大家看了一目了然。

![](/assets/images/2020/open_source_licenses.gif)

## 参考

[http://c.biancheng.net/view/2947.html](http://c.biancheng.net/view/2947.html)