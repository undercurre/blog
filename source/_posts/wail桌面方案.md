---
title: wail桌面方案
date: 2022-11-13 21:33:32
tags:
---

# Wails

一种专门针对桌面应用的跨端方案。而且是用go写的。

## 安装

这玩意需要Go和npm

## 建立项目
肯定选我们最喜欢的vue-ts来跨端
**这里有个坑，你一定要在系统变量里添加%USERPROFILE%\go\bin**
```
wails init -n myproject -t vue-ts
```

