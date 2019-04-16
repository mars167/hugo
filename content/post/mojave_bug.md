---
title: "MacOS Mojave 10.14.3 Wi-Fi卡死"
date: 2019-04-16T14:19:04+08:00
lastmod: 2019-04-16T14:19:04+08:00
draft: false
keywords: ["macos","mojave_bug","MacOS Mojave 10.14.3 Wi-Fi卡死"]
description: "mojave_bug"
tags: ["mac"]
categories: ["mac"]
author: "mars167"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
---
**bug描述：**
当电脑离手机热点太远导致wifi断开，期间电脑进入锁屏。再次唤醒wifi卡死。

> 这个bug在10.14.4还是会出现，不知道跟我开着蓝灯有没有关系
<!--more-->
**可用方案：**

```bash
sudo kill -9 `ps aux | grep -v grep | grep /usr/libexec/airportd | awk '{print $2}'`
```

> 机型：MacBook Pro (13-inch, 2016, Four Thunderbolt 3 Ports)

##### 在桌面建立一个恢复脚本`fix.sh`

```shell
#!/bin/bash

sudo kill -9 `ps aux | grep -v grep | grep /usr/libexec/airportd | awk '{print $2}'`
    
```