---
layout:     post
title:      "hexo主题同步"
subtitle:   "hexo"
date:       2019-12-26 12:00:00
author:     "weijie"
tags:
    - hexo
---

## 绑定子项目
```
git remote add -f next git@github.com:theme-next/hexo-theme-next
git subtree add --prefix=themes/next next master --squash
```
## 更新子项目
```
git fetch next master
git subtree pull --prefix=themes/next next master --squash
```

## 从子目录push到远程仓库
```
git subtree push --prefix=themes/next next master
```
