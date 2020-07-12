---
title: Linux 用户和权限
date: 2020-07-12 22:19:06
tags: Linux
---

# linux 用户管理及权限

## 用户管理

```shell
useradd username #添加用户
su username #切换用户
passwd username #修改用户密码，默认只能root用户修改

username:x:502:502::/home/kun:/bin/bash
1. 用户名
2. 密码，不可见
3.uid
4.gid
5.注释
6.家目录
7.环境变量

userdel kun  #删除用户，会留下用户文件夹，相关文件少一行。

groupadd  gname #创建组。
groupdel  gname  #删除组。
```

<!-- more -->

创建用户的时候发生了想那些事情。

1. home文件夹下面多了一个用户名命名的文件夹。
2. /etc/passwd文件是记录用户的文件，新增之后就会多一行
3. /etc/shadow文件是记录的不可描述的密码，无卵用
4. /etc/group用户组文件，如果你创建用户的时候，没有制定用户组，会自动创建袼跟用户名一直的用户组
5. uid和gid都是从500开始

## 权限

```shell
rwx r-x r-x
所属用户的权限
所属用户组的权限
其他人的权限

一个文件它可以属于某一个用户。
同时还可以属于一个用户组。

用户组包含一堆用户。

4 r：可读
2 w：可写，删除、修改、新增
1 x：可执行

数字权限代表法

rwx rwx rwx
7   7   7

rwx r-x r-x
7   5   5   

rwx r-x ---
7   5    0

umask  0022

chmod 777 index.php   #修改文件权限
chmod -R 777 /var    #修改文件夹的权限。

chgrp bo index.php	#修改所属组。
chown bo:bo  index.php   #修改所属用户以及用户组。
chown -R bo:bo index.php   #递归修改权限。

stat   index.php     #查询文件的各种时间
```