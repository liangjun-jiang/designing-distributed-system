分布式系统的设计模式和范例 - Designing Distribued Systems (patterns & Paradgims for Scalable, Reliable Service)

## 前言
本文基本上是个搬运文章。搬运书本2017年12月出版的<Designing Distributed Systems>  作者：Brendan Burns. Microsoft Azure
技术总监和Kubernetes的创始人

## 本书官方下载地址
https://azure.microsoft.com/en-us/resources/designing-distributed-systems/en-us/

## 致谢
本文受到团队成员David和铁三的指正。David是真的distributed system expert. 铁三是真得填坑专家。

## 版权申明
大部门内容是直接从原书来直接抄袭的。因此强烈建议读原书比较好。预计2小时能翻阅完。

## 作者觉得该书的意义
1. 容器(container)的出现从根本上改变了开发开发分布式系统。 
2. 是时间需把大家各自理解的术语和实践统一起来。
3. 既然都说同样的术语了，我们建的轮子就容易表述明白是做什么用、该怎么用。
4. 本书介绍了常用设计和快速开发可靠性高的分布式系统的实践。

## 主要话题
1. Use the side-car, adapter and ambassador patterns to split your application into a group of containers on a single machine
2. Explore loosely coupled multi-node distributed patterns for replication, scaling and communication between the components
3. Learn distributed system patterns for large-scale batch data processing covering work queues, event-based processing, and coordinated workflows.


## 我觉得本书的意义
Jeff Bezos，国际著名电商和云服务供应商的CEO， 在2002年左右的某一天发布如下邮件：[1](https://apievangelist.com/2012/01/12/the-secret-to-amazons-success-internal-apis/)
1. All teams will henceforth expose their data and functionality through service interfaces.
2. Teams must communicate with each other through these interfaces.
3. There will be no other form of inter-process communication allowed: no direct linking, no direct reads of another team’s data store, no shared-memory model, no back-doors whatsoever. The only communication allowed is via service interface calls over the network.
4. It doesn’t matter what technology they use.
5. All service interfaces, without exception, must be designed from the ground up to be externalizable. That is to say, the team must plan and design to be able to expose the interface to developers in the outside world. No exceptions.
6. Anyone who doesn’t do this will be fired.  
7. Thank you; have a nice day!

Jeff Bezos的邮件和本书的联系：
做为业务方有很大的业务述求，我们可能不仅只需要个交互平台，留些API接口给内部用户呀。


## 几个设计例子
考虑下如下几个例子， 你觉得该如何设计。前提条件，既然是在“分布式系统“这个概念下来设计，考虑的都是类似建
YouTube.com, Facebook Photos这样的数据量的。
0. 内网能从外部访问。比如我本地起的localhost:8080能让外面人访问
1. 从视频文件里产生视频的缩略图 - Implementing a Video Thumbnailer
2. 事件驱动（event-drive)的新用户注册
3. 图片打标签和处理 An Image Tagging & Processing Pipeline

## 蹭个热点
[鉴于内外上关于开发机不够用，开发机部署难得问题]()，似乎本书有些解答。






 




