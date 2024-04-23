---
layout:       post
title:        "ubuntu crash"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - linux
    - ubuntu
    - crash
---

1. 问题现象  
`You are in emergency mode. After logging in, type "journaltcl -xb to view system logs, "systemctrl reboot" to reboot "systemctl default" or "exit" to boot into default mode.`

2. 解决办法  
* 使用 `journaltcl -xb` 命令查看日志，我的错误是`failed to start File System Check on /dev/sdb1`  
* 使用`fsck /dev/sdb1` 命令对文件系统进行检查和修复  


