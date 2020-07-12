---
title: CentOS6关闭SELINUX和防火墙
date: 2020-07-12 22:20:40
tags: Linux
---

# CentOS6 关闭 SELINUX 和防火墙

## 关闭SELINUX

```
vi /etc/selinux/config
#SELINUX=enforcing #注释掉
#SELINUXTYPE=targeted #注释掉
SELINUX=disabled #增加
```

<!-- more -->

## 关闭防火墙

关闭命令：

```
service iptables stop
```

永久关闭防火墙：

```
chkconfig iptables off
```

运行完成后查看防火墙关闭状态

```
service iptables status
```