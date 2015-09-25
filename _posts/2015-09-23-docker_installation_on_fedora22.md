---
author: yingyun001
layout: post
title: "在 Fedora22 上安装 Docker"
date: 2015-09-23 17:08
category: docker
tags:
- docker storage driver
- fedora22
---

## 准备环境

0. 首先准备好 fedora22 操作系统。
   
   我的 fedora22 的内核版本：

   ~~~bash
   # uname -r
   4.0.4-301.fc22.x86_64
   ~~~

1. 更新系统。

   ~~~bash
   # dnf -y update
   ~~~

## 安装
0. 根据[上一篇博客](/docker/2015/09/22/new_apt_and_yum_repos)，我们有两种安装 Docker Engine 的方式：

   ~~~ bash
   $ curl -sSL https://get.docker.com/ | sh
   $ sudo service docker start
   ~~~
   或 

   ~~~ bash
   $ cat >/etc/yum.repos.d/docker.repo <<-EOF
   [dockerrepo]
   name=Docker Repository
   baseurl=https://yum.dockerproject.org/repo/main/fedora/22
   enabled=1
   gpgcheck=1
   gpgkey=https://yum.dockerproject.org/gpg
   EOF

   $ sudo yum install docker-engine
   $ sudo service docker start
   ~~~

   > **注意：**
   > 如果您发现当您开启 Docker 后台程序的时候，您被告知不具有开启 Docker 的权限，请您[往这看](/linux/2015/09/21/systemd_in_Fedora22/)。

   **在您确定您有权限开启 Docker 后台程序的情况下，Docker 仍不能成功开启的话，就涉及到我们接下来要说的 Docker 存储驱动的问题了：**

   * 让我们看看 docker.service 的配置

     ~~~ bash
     [helen@zhangyingyun ~]$ cat /usr/lib/systemd/system/docker.service 
     [Unit]
     Description=Docker Application Container Engine
     Documentation=https://docs.docker.com
     After=network.target docker.socket
     Requires=docker.socket

     [Service]
     Type=notify
     ExecStart=/usr/bin/docker daemon -H fd://
     MountFlags=slave
     LimitNOFILE=1048576
     LimitNPROC=1048576
     LimitCORE=infinity

     [Install]
     WantedBy=multi-user.target
     ~~~
  
     这个和 Fedora 提供的单元配置比起来，在自定义方面略显简单（不能给 Docker 程序添加选项）。
     我们可以对使用了 drop-in 文件的 docker systemd 单元进行自定义。

     ---
   * 让我们来为 Docker systemd 单元创建 drop-in 文件的目录：
     
     ~~~ bash
     mkdir -p /etc/systemd/system/docker.service.d
     ~~~
     
     该目录下文件的名字可以任取，可以使用多个 drop in 文件
     
     ~~~ bash
     vi /etc/systemd/system/docker.service.d/enable-options.conf
     [Service]
     EnvironmentFile=-/etc/sysconfig/docker
     ExecStart=
     ExecStart=/usr/bin/docker -d -H fd:// $OPTIONS
     ~~~

     > **注意**：这里有两行以 **ExecStart** 开头的配置信息，这是用于重写 *ExecStart* 的。
     
     ---
   * 接下来我们要在 /etc/sysconfig/docker 里通过配置 **OPTIONS** 来调整 docker 后台程序了。
     
     ~~~ bash
     OPTIONS="-s overlay"
     ~~~ 

     让我们检查一下配置是否生效。
     
     ~~~ bash
     [root@zhangyingyun ~]# systemctl status docker
     ● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
     Active: failed (Result: exit-code) since 五 2015-09-25 14:10:16 CST; 2h 44min ago
     Docs: https://docs.docker.com
     Process: 5627 ExecStart=/usr/bin/docker daemon -H fd:// (code=exited, status=2)
     Main PID: 5627 (code=exited, status=2)
     CGroup: /system.slice/docker.service

     9月 25 14:08:45 zhangyingyun.eayun systemd[1]: Starting Docker A...
     9月 25 14:10:15 zhangyingyun.eayun systemd[1]: docker.service st...
     9月 25 14:10:16 zhangyingyun.eayun systemd[1]: docker.service: m...
     9月 25 14:10:16 zhangyingyun.eayun systemd[1]: Failed to start D...
     9月 25 14:10:16 zhangyingyun.eayun systemd[1]: Unit docker.servi...
     9月 25 14:10:16 zhangyingyun.eayun systemd[1]: docker.service fa...
     9月 25 16:53:46 zhangyingyun.eayun systemd[1]: Stopped Docker Ap...
     9月 25 16:53:53 zhangyingyun.eayun systemd[1]: Stopped Docker Ap...
     Warning: docker.service changed on disk. Run 'systemctl daemon-reload' to reload units.
     Hint: Some lines were ellipsized, use -l to show in full.
     ~~~
     
     根据上面的提示，我们需要先将单元加载进来：

     ~~~ bash
     # systemctl daemon-reload
     [root@zhangyingyun ~]# systemctl status docker
     ● docker.service - Docker Application Container Engine
        Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
       Drop-In: /etc/systemd/system/docker.service.d
                └─enable-options.conf
        Active: failed (Result: exit-code) since 五 2015-09-25 14:10:16 CST; 2h 47min ago
           Docs: https://docs.docker.com
      Main PID: 5627 (code=exited, status=2)

     9月 25 14:08:45 zhangyingyun.eayun systemd[1]: Starting Docker A...
     9月 25 14:10:15 zhangyingyun.eayun systemd[1]: docker.service st...
     9月 25 14:10:16 zhangyingyun.eayun systemd[1]: docker.service: m...
     9月 25 14:10:16 zhangyingyun.eayun systemd[1]: Failed to start D...
     9月 25 14:10:16 zhangyingyun.eayun systemd[1]: Unit docker.servi...
     9月 25 14:10:16 zhangyingyun.eayun systemd[1]: docker.service fa...
     9月 25 16:53:46 zhangyingyun.eayun systemd[1]: Stopped Docker Ap...
     9月 25 16:53:53 zhangyingyun.eayun systemd[1]: Stopped Docker Ap...
     Hint: Some lines were ellipsized, use -l to show in full.
     ~~~

     虽然 Docker 仍未开启，但是可以从以上内容看出 systemd 已经检测到了 drop-in 文件。
## 关于 Docker 的存储驱动

## 修改 Docker 的存储驱动

## 成功开启 Docker

1. 测试
   通过在容器种运行一个用于测试的 image 来验证 Docker 是否已经安装成功。

   ~~~ bash
   $ sudo docker run hello-world
   ~~~

























