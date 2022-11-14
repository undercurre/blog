---
title: 如何封装Flutter组件
date: 2022-11-09 09:54:17
tags:
---

# 如何封装Flutter组件

组件分两种，stateful 和 stateless

## 封装stateless组件

```dart
import 'package:flutter/material.dart';

class GlassCard extends StatelessWidget {
  
  // 参数定义
  final double width;
  final double height;

  Widget child;

  // 构造函数
  GlassCard({
    this.width = 312,
    this.height = 112,
    required this.child
  });
  
  // 定义组件结构
  @override
  Widget build(BuildContext context) {

    return Container(
        width: width,
        height: height,
        margin: const EdgeInsets.fromLTRB(0, 0, 0, 16),
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(10.0),
          gradient: const LinearGradient(
              begin: Alignment(-1, 1),
              end: Alignment(0, 0.365),
              stops: [0.0, 1.0],
              colors: [Color(0xFF393E43), Color(0xFF333135)]),
        ),
        child: child);
  }
}
```

## 封装stateful组件