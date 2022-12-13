---
title: widget的坎坷一生
date: 2022-12-13 14:38:25
tags:
---
# Flutter的生命周期

了解Flutter的生命周期能帮助你更好了解widget运转过程中数据的流向。

<img src="../images/flutter_life.jpg" width=1000 height=450 />

## 前言

在正题之前我们先要达成几个共识： 
1. Widget只是对视图的配置信息的声明式描述
2. 在真正的渲染过程中有四棵树诞生：widget树（配置处理）->Element树（中间逻辑处理）->Render树（渲染）->layer树（GPU使用）

## 正题
statefuleWidget在跑起来之后

initState先会初始化其中的state