---
title: "Composer_install"
date: 2019-05-12T02:16:50+08:00
lastmod: 2019-05-12T02:16:50+08:00
draft: ture
keywords: ["composer"]
description: ""
tags: ["composer"]
categories: ["composer"]
author: ""

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



总结一下composer的优化，今天装服务器composer慢的怀疑人生

<!--more-->

```bash
Loading composer repositories with package information
Updating dependencies (including require-dev)
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - yiisoft/yii2-bootstrap 2.0.9 requires bower-asset/bootstrap 3.4.* | 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.8 requires bower-asset/bootstrap 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.7 requires bower-asset/bootstrap 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.6 requires bower-asset/bootstrap 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.5 requires bower-asset/bootstrap 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.4 requires bower-asset/bootstrap 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.3 requires bower-asset/bootstrap 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.2 requires bower-asset/bootstrap 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.10 requires bower-asset/bootstrap 3.4.* | 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.1 requires bower-asset/bootstrap 3.3.* | 3.2.* | 3.1.* -> no matching package found.
    - yiisoft/yii2-bootstrap 2.0.0 requires bower-asset/bootstrap 3.2.* | 3.1.* -> no matching package found.
    - Installation request for yiisoft/yii2-bootstrap * -> satisfiable by yiisoft/yii2-bootstrap[2.0.0, 2.0.1, 2.0.10, 2.0.2, 2.0.3, 2.0.4, 2.0.5, 2.0.6, 2.0.7, 2.0.8, 2.0.9].

```