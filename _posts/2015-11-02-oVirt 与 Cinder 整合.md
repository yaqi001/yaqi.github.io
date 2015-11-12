---
author: yingyun001
layout: post
title: "oVirt 与 Cinder 的整合"
date: 2015-11-02 18:00
category: 
- Cinder
- Ceph
tags:
- Cinder
- oVirt
---

刚开始看 Cinder 和 Ceph，一头雾水，a handful of terms。好吧，我还是去看官方文档，老实说看了一些翻译感觉非常不好，有点喝着咖啡吃大蒜的感觉。LOL..

[Ceph Object Storage](http://docs.ceph.com/docs/master/glossary/#term-ceph-object-storage)：你可以叫它对象存储“产品”，服务或者一个功能。包含了以下两个内容：
* [Ceph Storage Cluster]()：核心存储软件之一，这里存储了用户的数据(MON + OSD)。部署 Ceph 存储集群的时候需要具备两类组件：
  * 至少一个 [Ceph Monitor]：
  * 至少两个 [Ceph OSD Daemon]：用于存储数据；处理数据的 [replication]，[recovery]，[backfilling]，[rebalancing]；在 Heartbeat 的时候对其它 Ceph OSD 进行检查从而为 Ceph Monitor 提供一些监控数据。

* [Ceph Object Gateway](http://docs.ceph.com/docs/master/radosgw/)：是构建在 `librados` 之上的对象存储接口，用来给应用程序提供 Ceph 存储集群的 RESTful gateway.这里支持两种接口：
  * **S3-compatible**：
  * **Swift-compatible**： 


[Ceph Block Device]
一些术语：
* librados： 
