---
title: "Hugo Github Pages 部署 [关于subtree]"
date: 2019-04-16T10:21:11+08:00
lastmod: 2019-04-16T10:21:11+08:00
draft: false
keywords: ["hugo","blog"]
description: "XXX"
tags: ["hugo","blog","git","git pages"]
categories: ["hugo","git"]
author: "mars167"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: true
reward: false
mathjax: false
---

> 写的比较简略，适合熟悉git的朋友参考。由于自己比较菜可能有说的准确的地方，希望朋友们可以结合相关文档操作
<!--more-->
1. 在`hugo new site`之后`cd `到博客的根目录
2. 添加`hugo new `生成一篇文章执行`hugo`生成静态网站到public目录，将master分支push到远程仓库
3. 复制public目录下的内容`git checkout -b gh-pages`将public目录下的内容复制至此`git commit`一下 ，`git push`将此分支到remote仓库
4. `git checkout master` 执行下面的命令，确保工作目录下没有修改的内容

  ```bash
  git subtree add --prefix=public origin gh-pages --squash
  ```

  > 目的是将gh-pages分支下的内容放至public目录下

5. 与远程仓库同步

```bash
git subtree pull --prefix=public   origin gh-pages
```

6. push subtree


```bash
git subtree push --prefix=public origin gh-pages
```

> 如果push遇到问题，这里是强制push的方法
>
> ```bash
> git push origin `git subtree split --prefix=public origin gh-pages`:gh-pages --force
> ```
>

7. 部署脚本`deploy.sh`，将此脚本文件放在根目录下当有新写的文章的时候`./deploy.sh`执行此脚本即刻完成自动部署到github

   > 注意，设置自己的remote
   >
   > ```
   > git remote add origin {远程仓库地址}
   > ```

```bash
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
hugo

# Add changes to git.
git add -A

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push  origin master
git subtree push --prefix=public origin gh-pages
git subtree pull --prefix=public origin gh-pages
```



