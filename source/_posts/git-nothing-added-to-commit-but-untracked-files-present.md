---
title: git nothing added to commit but untracked files present
date: 2016-10-10 14:38:55
tags: Android
---

执行 git add . -A 了，没报错，结果commit的时候死活报这个错nothing added to commit but untracked files present
纠结了老半天，最后用AS提的时候才报出错误信息“filename too long”，原来是我这个插件的路径太深了
执行一句`git config --global core.longpaths true` 再次commit成功了
但是为嘛git命令不提示错误呢=。=

