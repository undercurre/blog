---
title: 如何封装Flutter组件
date: 2022-11-09 09:54:17
tags:
---

# 如何封装Flutter组件

组件分两种，stateful 和 stateless

## 封装stateless组件(渐变按钮)

```dart
import 'package:flutter/material.dart';

class GradientButton extends StatelessWidget {
  const GradientButton({Key? key, 
    this.colors,
    this.width,
    this.height,
    this.onPressed,
    this.borderRadius,
    required this.child,
  }) : super(key: key);

  // 渐变色数组
  final List<Color>? colors;

  // 按钮宽高
  final double? width;
  final double? height;
  final BorderRadius? borderRadius;

  //点击回调
  final GestureTapCallback? onPressed;

  final Widget child;

  @override
  Widget build(BuildContext context) {
    ThemeData theme = Theme.of(context);

    //确保colors数组不空
    List<Color> _colors =
        colors ?? [theme.primaryColor, theme.primaryColorDark];

    return DecoratedBox(
      decoration: BoxDecoration(
        gradient: LinearGradient(colors: _colors),
        borderRadius: borderRadius,
        //border: RoundedRectangleBorder(borderRadius: BorderRadius.circular(20.0)),
      ),
      child: Material(
        type: MaterialType.transparency,
        child: InkWell(
          splashColor: _colors.last,
          highlightColor: Colors.transparent,
          borderRadius: borderRadius,
          onTap: onPressed,
          child: ConstrainedBox(
            constraints: BoxConstraints.tightFor(height: height, width: width),
            child: Center(
              child: Padding(
                padding: const EdgeInsets.all(8.0),
                child: DefaultTextStyle(
                  style: const TextStyle(fontWeight: FontWeight.bold),
                  child: child,
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

## 封装stateful组件（毛玻璃卡片）

```dart
import 'package:flutter/material.dart';

class GlassCard extends StatelessWidget {
  final double width;
  final double height;
  final Widget child;

  const GlassCard(
      {super.key, this.width = 312, this.height = 112, required this.child});

  @override
  Widget build(BuildContext context) {
    return Container(
      width: width,
      height: height,
      margin: const EdgeInsets.fromLTRB(0, 0, 0, 16),
      decoration: BoxDecoration(
        borderRadius: BorderRadius.circular(10.0),
        border: Border.all(width: 0.2, color: const Color(0xff979797)),
        gradient: const LinearGradient(
          begin: Alignment(-1, 1), //右上
          end: Alignment(0, 0.365), //左下
          stops: [0.0, 1.0], //[渐变起始点, 渐变结束点]
          colors: [Color(0xFF393E43), Color(0xFF333135)],
        ),
      ),
      child: child,
    );
  }
}
```