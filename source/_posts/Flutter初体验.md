---
title: Flutter初体验
date: 2022-11-07 23:19:20
tags:
---

# Flutter

<img src="https://static001.geekbang.org/resource/image/ac/2f/ac7d1cec200f7ea7cb6cbab04eda252f.png?wh=2274*1186" width=1000 height=450 />

作为一个成熟的前端工程师，我地梗是先了解flutter的架构啦！因为要先知道这个玩意究竟是什么？能干嘛？干到哪？

OK!首先第一单也就是，flutter是个跨端技术方案！

然后你要知道它是用来搞跨端APP的首选。

因为它能做到WebView和weex这些方案更好的性能！

至于它怎么做到的呢？看图咯！

唔使慌喔！我知道你看唔明！慢慢看埋啦！

像这张图所画：Flutter 架构采用分层设计，从下到上分为三层，依次为：Embedder、Engine、Framework。Embedder 是操作系统适配层，实现了渲染 Surface 设置，线程设置，以及平台插件等平台相关特性的适配。从这里我们可以看到，Flutter 平台相关特性并不多，这就使得从框架层面保持跨端一致性的成本相对较低。Engine 层主要包含 Skia、Dart 和 Text，实现了 Flutter 的渲染引擎、文字排版、事件处理和 Dart 运行时等功能。Skia 和 Text 为上层接口提供了调用底层渲染和排版的能力，Dart 则为 Flutter 提供了运行时调用 Dart 和渲染引擎的能力。而 Engine 层的作用，则是将它们组合起来，从它们生成的数据中实现视图渲染。Framework 层则是一个用 Dart 实现的 UI SDK，包含了动画、图形绘制和手势识别等功能。为了在绘制控件等固定样式的图形时提供更直观、更方便的接口，Flutter 还基于这些基础能力，根据 Material 和 Cupertino 两种视觉设计风格封装了一套 UI 组件库。我们在开发 Flutter 的时候，可以直接使用这些组件库。