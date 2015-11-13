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

# 先说说 Ceph

[Ceph Object Storage](http://docs.ceph.com/docs/master/glossary/#term-ceph-object-storage)：你可以叫它对象存储“产品”，服务或者一个功能。包含了以下两个内容：

* [Ceph Storage Cluster]()：核心存储软件之一，这里存储了用户的数据(MON + OSD)。部署 Ceph 存储集群的时候需要具备两类组件：
   * 至少一个 [Ceph Monitor]：Ceph Monitor 会持续更新 cluster 状态的 map，包括以下四个 map。Ceph 还维护了 Ceph Monitor，Ceph OSD 以及 Placement Group 中每次状态发生改变时的历史记录。
      * monitor map
      * OSD map
      * Placement Group map
      * [CRUSH map]
   * 至少两个 [Ceph OSD Daemon]：用于存储数据；处理数据的 [replication]，[recovery]，[backfilling]，[rebalancing]；在每次 Heartbeat 的时候对其它 Ceph OSD 后台程序进行检查从而为 Ceph Monitor 提供一些监控数据。如果集群为您的数据做了两份拷贝，那 Ceph Storage Cluster 需要至少两个 Ceph OSD 后台程序才能达到 active + clean 的状态（**什么是 active + clean 状态..o.O**）。

* [Ceph Object Gateway](http://docs.ceph.com/docs/master/radosgw/)：是构建在 `librados` 之上的对象存储接口，用来给应用程序提供 Ceph 存储集群的 RESTful gateway.这里支持以下两种接口；Ceph Object Storage 利用 Ceph Object Gateway 后台程序(**[radosgw](http://docs.ceph.com/docs/v0.69/man/8/radosgw/)**)，radosgw 是用于和 Ceph Storage Cluster 进行交互的 FastCGI 模块。
   * **S3-compatible**：兼容了 Amazon S3 RESTful API 的接口提供了对象存储的功能。
   * **Swift-compatible**：兼容了 OpenStack Swift API 的接口提供了对想存储的功能。

[Ceph Block Device](http://docs.ceph.com/docs/master/rbd/rbd/)

[Ceph Filesystem](http://docs.ceph.com/docs/master/cephfs/)

一些术语：

* [librados](http://docs.ceph.com/docs/giant/rados/api/librados-intro/)： 
* [replication](https://en.wikipedia.org/wiki/Replication_(computing))：IBM 给出的[解释](https://www-01.ibm.com/software/data/replication/)
* [recovery](https://en.wikipedia.org/wiki/Data_recovery)：
* backfilling：
* rebalancing：
* active + clean：
* [heartbeat](http://linux-ha.org/wiki/Heartbeat)：
