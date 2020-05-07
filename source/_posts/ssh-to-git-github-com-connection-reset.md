---
title: ssh -T git@github.com Connection reset by XXX port 22
date: 2020-05-07 13:34:09
tags: [Github,SSH]
categories: Github
copyright: true
---
## 概述

​	今天在用**Hexo**发布博客文章时，遇到上传**Github**失败问题，主要提示为

```bash
Connection reset by 52.74.223.119
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```
<!-- more -->

## 问题发现	

​	由于已经在`Git bash`中配置过SSH免密访问，且已正常使用过也没有进行修改，所以排除SSH key配置问题。怀疑是无法连接到`github.com`,尝试执行`ssh -T git@github.com`得到如下结果：

```bash
$ ssh -T git@github.com
Connection reset by 52.74.223.119 port 22
```

​	竟然真的无法SSH连接到github.com？？？增加`-v`选项查看一下详细信息，反馈如下：

```bash
$ ssh -T -v git@github.com
OpenSSH_8.0p1, OpenSSL 1.1.1c  28 May 2019
debug1: Reading configuration data /c/Users/Alder/.ssh/config
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Connecting to github.com [52.74.223.119] port 22.
debug1: Connection established.
debug1: identity file /c/Users/Alder/.ssh/id_rsa type 0
debug1: identity file /c/Users/Alder/.ssh/id_rsa-cert type -1
debug1: identity file /c/Users/Alder/.ssh/id_dsa type -1
debug1: identity file /c/Users/Alder/.ssh/id_dsa-cert type -1
debug1: identity file /c/Users/Alder/.ssh/id_ecdsa type -1
debug1: identity file /c/Users/Alder/.ssh/id_ecdsa-cert type -1
debug1: identity file /c/Users/Alder/.ssh/id_ed25519 type -1
debug1: identity file /c/Users/Alder/.ssh/id_ed25519-cert type -1
debug1: identity file /c/Users/Alder/.ssh/id_xmss type -1
debug1: identity file /c/Users/Alder/.ssh/id_xmss-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_8.0
debug1: Remote protocol version 2.0, remote software version babeld-d45c1532
debug1: no match: babeld-d45c1532
debug1: Authenticating to github.com:22 as 'git'
debug1: SSH2_MSG_KEXINIT sent
Connection reset by 52.74.223.119 port 22
```

## 问题解决

​	在Windows系统下，打开控制面板->系统和安全->Windows Defender 防火墙->高级设置，选择入站规则，点击新建规则，选择端口，在特定本地端口写入22，连续选择下一步三次，输入一个名称（随意命名规则），点击完成，然后再执行命令得到如下格式结果：

```bash
$ ssh -T git@github.com
Hi XXX! You've successfully authenticated, but GitHub does not provide shell access.
```

​	再尝试发布**Hexo**博客到**Github**就可以正常上传了...暂时不知道这其中的原理，即使将刚添加的规则删除了，再打开新的`Git bash`窗口也不会出现Connection reset错误了。。。